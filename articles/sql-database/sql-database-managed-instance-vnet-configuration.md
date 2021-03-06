---
title: Azure SQL Database 受控執行個體 VNet 組態 | Microsoft Docs
description: 本主題描述具有 Azure SQL Database 受控執行個體之虛擬網路 (VNet) 的設定選項。
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: b17749999f7903746651403c5948933332dbee5d
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047927"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>設定 Azure SQL Database 受控執行個體的 VNet

Azure SQL Database 受控執行個體 (預覽) 必須部署在 Azure [虛擬網路 (VNet)](../virtual-network/virtual-networks-overview.md) 內。 此部署適用於下列案例： 
- 從內部部署網路直接連線到受控執行個體 
- 將受控執行個體連線到連結的伺服器或其他內部部署資料存放區 
- 將受控執行個體連線到 Azure 資源  

## <a name="plan"></a>規劃

使用您對於以下問題的答案，規劃要如何在虛擬網路中部署受控執行個體： 
- 您計劃部署單一或多個受控執行個體？ 

  受控執行個體的數目會決定要為受控執行個體配置的子網路大小下限。 如需詳細資訊，請參閱[決定受控執行個體的子網路大小](#determine-the-size-of-subnet-for-managed-instances)。 
- 您是否需要將受控執行個體部署到現有虛擬網路，或者您要建立新的網路？ 

   如果您打算使用現有的虛擬網路，您需要修改該網路組態以順應您的受控執行個體。 如需詳細資訊，請參閱[針對受控執行個體修改現有的虛擬網路](#modify-an-existing-virtual-network-for-managed-instances)。 

   如果您打算建立新的虛擬網路，請參閱[針對受控執行個體建立新的虛擬網路](#create-a-new-virtual-network-for-managed-instances)。

## <a name="requirements"></a>需求

若要建立受控執行個體，您需要 VNet 內符合下列需求的專用子網路：
- **專用的子網路**：子網路不能包含與其相關聯的任何其他雲端服務，且不能是閘道子網路。 您無法在包含受控執行個體以外資源的子網路中建立受控執行個體，或者稍後於子網路內新增其他資源。
- **沒有 NSG**：子網路不能有與其相關聯的網路安全性群組。 
- **具有特定路由表**：子網路必須有使用者路由表 (UDR)，其中以 0.0.0.0/0 下一個躍點網際網路作為指派給它的唯一路由。 如需詳細資訊，請參閱[建立必要的路由表並產生關聯](#create-the-required-route-table-and-associate-it)
3. **選擇性自訂 DNS**：如果在 VNet 上指定自訂 DNS，則必須將 Azure 的遞迴解析程式 IP 位址 (例如 168.63.129.16) 新增至清單。 如需詳細資訊，請參閱[設定自訂 DNS](sql-database-managed-instance-custom-dns.md)。
4. **沒有服務端點**：子網路不能有與其相關聯的服務端點。 請確定在建立 VNet 時停用 [服務端點] 選項。
5. **足夠的 IP 位址**：子網路必須有最少 16 個 IP 位址 (建議最小值為 32 個 IP 位址)。 如需詳細資訊，請參閱[決定受控執行個體的子網路大小](#determine-the-size-of-subnet-for-managed-instances)

> [!IMPORTANT]
> 如果目的地子網路與上述所有需求不相容，您就無法部署新的受控執行個體。 目的地 Vnet 和子網路必須符合這些受控執行個體需求 (部署前和部署後)，任何違規都可能造成執行個體進入錯誤狀態而變得無法使用。 從該狀態復原時，您需要在 VNet 中以相容的網路原則建立新的執行個體、重新建立執行個體層級資料，以及還原您的資料庫。 這會對您的應用程式造成顯著的停機時間。

隨著_網路意圖原則_引進，您可以在建立受控執行個體之後，在受控執行個體子網路上新增網路安全性群組 (NSG)。

您現在可以使用 NSG，將 IP 範圍縮小為應用程式和使用者可以查詢的範圍，並且藉由篩選通過連接埠 1433 的網路流量來管理資料。 

> [!IMPORTANT]
> 當您設定會將存取限制為連接埠 1433 的 NSG 規則時，您也必須插入下表中顯示的最高優先順序輸入規則。 否則網路意圖原則會以不符合規範為由，封鎖變更。

| 名稱       |連接埠                        |通訊協定|來源           |目的地|動作|
|------------|----------------------------|--------|-----------------|-----------|------|
|管理  |9000、9003、1438、1440、1452|任意     |任意              |任意        |允許 |
|mi_subnet   |任意                         |任意     |MI SUBNET        |任意        |允許 |
|health_probe|任意                         |任意     |AzureLoadBalancer|任意        |允許 |

路由體驗也已經改善，所以除了 0.0.0.0/0 下一個躍點類型網際網路路由之外，您現在還可以將 UDR 新增至透過虛擬網路閘道或虛擬網路設備 (NVA)，前往內部部署私人 IP 範圍的路由流量。

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>決定受控執行個體的子網路大小

當您建立受控執行個體時，Azure 會依據您在佈建期間選取的層來配置虛擬機器數目。 因為這些虛擬機器與您的子網路相關聯，所以它們需要 IP 位址。 為了確保正常作業和服務維護期間的高可用性，Azure 可能會配置額外的虛擬機器。 因此，子網路中所需的 IP 位址數目會大於該子網路中的受控執行個體數目。 

根據設計，受控執行個體在子網路中需要最少 16 個 IP 位址，可能會使用多達 256 個 IP 位址。 因此，您可以在定義子網路 IP 範圍時使用子網路遮罩 /28 到 /24。 

> [!IMPORTANT]
> IP 位址為 16 個的子網路大小是最小值，受控執行個體進一步相應放大的可能性有限。強烈建議您選擇前置詞為 /27 或以下的子網路。 

如果您打算在子網路內部署多個受控執行個體，且需要將子網路大小最佳化，請使用這些參數來進行計算： 

- Azure 會在子網路中針對自己的需求使用 5 個 IP 位址 
- 每個一般用途執行個體都需要 2 個位址 
- 每個業務關鍵執行個體都需要四個位址

**範例**：您計劃要有三個一般用途和兩個業務關鍵受控執行個體。 這表示您需要 5 + 3 * 2 + 2 * 4 = 19 個 IP 位址。 因為 IP 範圍是以 2 的乘冪定義，所以您需要 32 (2^5) 個 IP 位址的 IP 範圍。 因此，您需要保留子網路遮罩為 /27 的子網路。 

> [!IMPORTANT]
> 上方顯示的計算會變成過時並需要進一步改善。 

## <a name="create-a-new-virtual-network-for-managed-instance-using-azure-resource-manager-deployment"></a>使用 Azure Resource Manager 部署為受控執行個體建立新的虛擬網路

建立並設定虛擬網路最簡單的方式是使用 Azure Resource Manager 部署範本。

1. 登入 Azure 入口網站。

2. 使用 [部署至 Azure] 按鈕以在 Azure 雲端中部署虛擬網路：

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  此按鈕將會開啟一個表單，讓您用來設定可在其中部署受控執行個體的網路環境。

  > [!Note]
  > 此 Azure Resource Manager 範本將會部署具有兩個子網路的虛擬網路。 稱為 **ManagedInstances** 的子網路是針對受控執行個體所保留而且有預先設定的路由表，而另一個名為**預設**的子網路是用於應該存取受控執行個體 (例如 Azure 虛擬機器) 的其他資源。 若您不需要**預設**子網路，您可以將它移除。

3. 設定網路環境。 在下一張表單上，您可以設定網路環境的參數：

![設定 Azure 網路](./media/sql-database-managed-instance-get-started/create-mi-network-arm.png)

您可以變更 VNet 與子網路的名稱，以及調整與您的網路資源關聯的 IP 位址。 一旦按下 [購買] 按鈕，此表單就會建立並設定您的環境。 若您不需要兩個子網路，您可以刪除預設子網路。 

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>為受控執行個體修改現有的虛擬網路 

本節中的問題和答案說明如何將受控執行個體新增至現有的虛擬網路。 

**您要對現有的虛擬網路使用傳統或 Resource Manager 部署模型？** 

您只能在 Resource Manager 虛擬網路中建立受控執行個體。 

**您要為 SQL 受控執行個體建立新的子網路或使用現有的子網路？**

如果您想要建立新的子網路： 

- 藉由遵循[決定受控執行個體的子網路大小](#determine-the-size-of-subnet-for-managed-instances)一節中的指導方針來計算子網路大小。
- 請遵循[新增、變更或刪除虛擬網路子網路](../virtual-network/virtual-network-manage-subnet.md)中的步驟。 
- 建立路由表，其中包含單一項目 **0.0.0.0/0** 作為下一個躍點網際網路，並且讓它與受控執行個體的子網路相關聯。  

如果您想要在現有子網路內建立受控執行個體，我們建議使用下列 PowerShell 指令碼來準備子網路。
```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```
子網路準備可以在 3 個簡單步驟中完成：

- 驗證 - 針對受控執行個體網路需求，驗證所選虛擬網路和子網路
- 確認 - 使用者會看到需要進行的一組變更，以便為受控執行個體部署準備子網路，並且由系統要求同意
- 準備 - 虛擬網路和子網路已正確設定

**您是否已設定自訂 DNS 伺服器？** 

如果是的話，請參閱[設定自訂 DNS](sql-database-managed-instance-custom-dns.md)。 

- 建立必要的路由表並產生關聯：請參閱[建立必要的路由表並產生關聯](#create-the-required-route-table-and-associate-it)

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱[受控執行個體是什麼](sql-database-managed-instance.md)。
- 如需示範如何建立 VNet、建立受控執行個體，以及從資料庫備份還原資料庫的教學課程，請參閱[建立 Azure SQL Database 受控執行個體](sql-database-managed-instance-create-tutorial-portal.md)。
- 針對 DNS 問題，請參閱[設定自訂 DNS](sql-database-managed-instance-custom-dns.md)
