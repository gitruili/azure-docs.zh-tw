---
title: 使用適用於 VM 的 Azure 監視器來監視虛擬機器健康情況 | Microsoft Docs
description: 本文說明您如何使用適用於 VM 的 Azure 監視器，來了解虛擬機器和基礎作業系統的健康情況。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: c8a8598640e31f59476b5b3351fdb2eab7b66a6c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952914"
---
# <a name="understand-the-health-of-your-azure-virtual-machines-with-azure-monitor-for-vms"></a>使用適用於 VM 的 Azure 監視器來了解 Azure 虛擬機器的健康情況
Azure 包含多個服務，它們可在監視空間中分別執行特定的角色或工作，但無法用來為裝載於 Azure 虛擬機器的作業系統提供深入的健康情況檢視方塊。  雖然您可以使用 Log Analytics 或 Azure 監視器來監視不同的情況，但它們並不是設計來呈現核心元件的健康情況或虛擬機器的整體健康情況或為其設定模型。  透過適用於 VM 的 Azure 監視器健康情況功能，它會利用一個模型主動監視 Windows 或 Linux 客體 OS 的可用性和效能，該模型代表重要元件及其關聯性、指定如何測量那些元件健康情況的準則，並在偵測到狀況不良的情況時向您發出警示。  

檢視 Azure VM 和基礎作業系統的整體健康狀態，可以從含有適用於 VM 的 Azure 監視器健康情況的兩個檢視方塊、直接從虛擬機器，或者跨 Azure 監視器中某個資源群組的所有 VM 來觀測。

本文將協助您了解如何快速地評估、調查和解決所偵測到的健康情況問題。

如需設定適用於 VM 的 Azure 監視器相關資訊，請參閱[啟用適用於 VM 的 Azure 監視器](monitoring-vminsights-onboard.md)。

## <a name="monitoring-configuration-details"></a>監視設定詳細資料
本節將概述定義來監視 Azure Windows 和 Linux 虛擬機器的預設健康情況準則。

### <a name="windows-vms"></a>Windows VM

- 記憶體的可用 MB 
- 每次寫入 (邏輯磁碟) 的平均磁碟秒數
- 每次寫入 (磁碟) 的平均磁碟秒數
- 每次讀取的平均邏輯磁碟秒數
- 每次傳輸的平均邏輯磁碟秒數
- 每次讀取的平均磁碟秒數
- 每次傳輸的平均磁碟秒數
- 目前磁碟佇列長度 (邏輯磁碟)
- 目前磁碟佇列長度 (磁碟)
- 磁碟閒置時間百分比
- 檔案系統錯誤或損毀
- 邏輯磁碟可用空間 (%) 低
- 邏輯磁碟可用空間 (MB) 低
- 邏輯磁碟閒置時間百分比
- 每秒記憶體分頁數
- 已用來讀取的頻寬百分比
- 已使用的頻寬百分比總計
- 已用來寫入的頻寬百分比
- 使用中認可的記憶體百分比
- 實體磁碟閒置時間百分比
- DHCP 用戶端服務健康狀態
- DNS 用戶端服務健康狀態
- RPC 服務健康狀態
- 伺服器服務健康狀態
- CPU 使用率百分比總計
- Windows 事件記錄服務健康狀態
- Windows 防火牆服務健康狀態
- Windows 遠端管理服務健康狀態

### <a name="linux-vms"></a>Linux VM
- 磁碟平均Disk sec/Transfer 
- 磁碟平均Disk sec/Read 
- 磁碟平均Disk sec/Write 
- 磁碟健康情況
- 邏輯磁碟可用空間
- 邏輯磁碟 % 可用空間
- 邏輯磁碟 % 可用閒置空間
- 網路介面卡健康情況
- 處理器 DPC 時間百分比
- 處理器的處理器時間百分比
- 處理器時間百分比總計
- DPC 時間百分比總計
- 作業系統記憶體的可用 MB

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站
登入 [Azure 入口網站](https://portal.azure.com)。 

## <a name="introduction-to-health-experience"></a>健康情況體驗簡介
深入了解如何針對單一虛擬機器或 VM 群組使用健康情況功能之前，重要的是我們將提供一段簡介，讓您能夠了解資訊的呈現方式，以及視覺效果所呈現的內容。  

## <a name="view-health-directly-from-a-virtual-machine"></a>直接從虛擬機器檢視健康情況 
若要檢視 Azure VM 的健康情況，從虛擬機器的左側窗格選取 [Insights (預覽)]。 在 [VM Insights] 頁面中，預設會開啟 [健康情況] 並顯示 VM 的健康情況檢視。  

![所選取 Azure 虛擬機器之適用於 VM 的 Azure 監視器健康情況概觀](./media/monitoring-vminsights-health/vminsights-directvm-health.png)

在 [健康情況] 索引標籤的 [客體 VM 健康情況] 區段下方，有個表格會顯示您虛擬機器目前的健康狀態，以及由狀況不良元件所引發的 VM 健康情況警示總數。 如需更多詳細資訊，請參閱[警示和警示管理](#alerting-and-alert-management)。  

以下為針對 VM 所定義的健康狀態： 

* **狀況良好**：針對 VM 並未偵測到任何問題，而其會視需要運作。  
* **重大**：已偵測到一或多個重大問題，您必須解決這類問題，才能如預期般地還原正常的功能。 
* **警告**：已偵測到一或多個問題，您必須解決這類問題，或者健康情況可能會變成「重大」。  
* **未知**：如果服務無法建立與 VM 的連線，狀態就會變更為未知狀態。  

選取 [檢視健康情況診斷]，即會開啟一個頁面來顯示 VM 的所有元件、相關聯的健康情況準則、狀態變更，以及與 VM 相關之監視元件所遇到的其他重大問題。 如需更多詳細資訊，請參閱[健康情況診斷](#health-diagnostics)。 

在 [元件健康情況] 區段下方，有個表格會顯示適用於那些區域之健康情況準則所監視的主要效能分類健康情況彙總狀態，特別是 **CPU**、**記憶體**、**磁碟**及**網路**。  選取其中任一個元件，即會開啟一個頁面，列出該元件的所有個別健康情況準則監視層面，以及每個層面各自的健康狀態。  

從執行 Windows 作業系統的 Azure VM 中存取健康情況時，前 5 個核心 Windows 服務的健康狀態會顯示於 [核心服務健康情況] 區域下方。  選取其中任一個服務，即會開啟一個頁面，列出監視該元件的健康情況準則及其健康狀態。  按一下健康情況準則的名稱將會開啟屬性窗格，而您可以從這裡檢閱設定詳細資料，包括健康情況準則是否已定義對應的 Azure 監視器警示。 若要深入了解此情況，請參閱[健康情況診斷和健康情況的使用方式](#health-diagnostics)。  

## <a name="aggregate-virtual-machine-perspective"></a>彙總虛擬機器檢視方塊
若要檢視資源群組中所有虛擬機器的健康情況集合，從入口網站的瀏覽清單中選取 [Azure 監視器]，然後選取 [虛擬機器 (預覽)]。  

![Azure 監視器中的 VM Insights 監視檢視](./media/monitoring-vminsights-health/vminsights-aggregate-health.png)

從 [訂用帳戶] 和 [資源群組] 下拉式清單中，適當地選取一個包含已上線之目標 VM 的選項以檢視其健康狀態。 

在 [健康情況] 索引標籤上，您可以了解下列內容：

* 有多少部 VM 處於重大或狀況不良狀態，以及有多少部 VM 的狀況良好或未提交資料 (稱為未知狀態)？
* 作業系統 (OS) 回報哪些 VM 處於狀況不良的狀態，且數量有多少？
* 有多少部 VM 因為處理器、磁碟、記憶體或網路介面卡偵測到問題 (依健康狀態分類) 而處於狀況不良的狀態？  
* 有多少部 VM 因為核心作業系統服務偵測到問題 (依健康狀態分類) 而處於狀況不良的狀態？

您可以在這裡快速地識別由主動監視 VM 的健康情況準則所偵測到的前幾個重大問題，並檢閱 VM 健康情況警示的詳細資料，以及旨在協助診斷並修復問題的相關知識文章。  選取任何嚴重性以開啟依照該嚴重性篩選的 [所有警示](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md#all-alerts-page) 頁面。

[依作業系統的 VM 分佈] 清單會顯示依 Windows 版本或 Linux 發行版本列出的 VM 及其版本。 在每個作業系統分類中，都會根據 VM 的健康情況進一步細分 VM。 

![VM Insights 虛擬機器分佈檢視方塊](./media/monitoring-vminsights-health/vminsights-vmdistribution-by-os.png)

以下為針對 VM 所定義的健康狀態： 

* **狀況良好**：針對 VM 並未偵測到任何問題，而其會視需要運作。  
* **重大**：已偵測到一或多個重大問題，您必須解決這類問題，才能如預期般地還原正常的功能。 
* **警告**：已偵測到一或多個問題，您必須解決這類問題，或者健康情況可能會變成「重大」。  
* **未知**：如果服務無法建立與 VM 的連線，狀態就會變更為未知狀態。  

您可以按一下任一個資料行項目：[VM 計數]、[重大]、[警告]、[狀況良好] 或 [未知]，向下切入到 [虛擬機器] 頁面以查看符合所選取資料行的篩選結果清單。 例如，如果我們想要檢閱執行 **Red Hat Enterprise Linux 7.5 版**的所有 VM，請按一下該 OS 的 [VM 計數] 值，它將會開啟下列頁面，列出符合該篩選條件的虛擬機器及其目前已知的健康狀態。  

![Red Hat Linux VM 的範例彙總](./media/monitoring-vminsights-health/vminsights-rollup-vm-rehl-01.png)
 
在 [虛擬機器] 頁面上，如果您選取 [VM 名稱] 資料行下方的 VM 名稱，系統就會將您導向到 VM 執行個體頁面，其中包含更多警示的詳細資料，以及已識別出會影響所選取 VM 的健康情況準則問題。  您可以從這裡按一下頁面左上角的 [健康狀態] 圖示來篩選健康狀態詳細資料，以查看哪些元件狀況不良，或者您可以檢視由依警示嚴重性分類且狀況不良之元件所引發的 VM 健康情況警示。    

從 VM 清單檢視中按一下 VM 的名稱，即會開啟所選取 VM 的 [健康情況] 頁面，就像您直接從 VM 選取 [Insights (預覽)] 一樣。

![所選取 Azure 虛擬機器的 VM Insights](./media/monitoring-vminsights-health/vminsights-directvm-health.png)

這裡會顯示虛擬機器的彙總**健康狀態**和**警示** (依嚴重性分類)，其會呈現當健康情況準則的健康狀態從狀況良好變更為狀況不良時所引發的 VM 健康情況警示。  選取 [處於重大情況的 VM]，將會開啟一個頁面，其中具有一或多部處於重大健康情況狀態的 VM 清單。  在清單中按一下其中一部 VM 的健康狀態，將會顯示該 VM 的 [健康情況診斷] 檢視。  您可以在這裡找出哪些健康情況準則會反映健康狀態問題。 在 [健康情況診斷] 頁面開啟時，它會顯示 VM 的所有元件，以及其具有目前健康狀態的相關聯健康情況準則。  如需更多詳細資訊，請參閱[健康情況診斷](#health-diagnostics)一節。  

選取 [檢視所有健康情況準則]，即會開啟一個頁面，顯示適用於此功能的所有健康情況準則清單。  您可以根據下列選項進一步篩選資訊：

* **類型**：有三種健康情況準則類型可用來評估情況，並彙總受監視 VM 的整體健康狀態。  
    a. **單位**：測量虛擬機器的某些層面。 這個健康情況準則類型可能會檢查效能計數器來判斷元件的效能、執行指令碼來執行綜合交易，或監看表示發生錯誤的事件。  預設會將篩選條件設為「單位」。  
    b. **相依性**：提供不同實體之間的健康情況彙總。 這個健康情況準則可讓實體的健康情況相依於另一種相依於成功作業之實體的健康情況。  
    c. **彙總**：提供類似健康情況準則的健康狀態組合。 單位和相依性健康情況準則通常將設定於彙總健康情況準則下方。 除了可針對目標設為實體的許多不同健康情況準則提供更好的一般組織方式，彙總健康情況準則還能針對不同的實體分類提供唯一的健康狀態。

* **分類**：健康情況準則的類型，可基於報告目的用來群組類似類型的準則。  它們可以是**可用性**或**效能**。

您可以按一下 [狀況不良的元件] 資料行下方的值，進一步向下切入來查看哪些執行個體的狀況不良。  在頁面上，有個表格會列出處於重大健康情況狀態的元件。    

## <a name="health-diagnostics"></a>健康情況診斷
[健康情況診斷] 頁面讓您能夠檢視 VM 的所有元件、相關聯的健康情況準則、狀態變更，以及與 VM 相關之監視物件所遇到的其他重大問題。 

![適用於 VM 的健康情況診斷頁面範例](./media/monitoring-vminsights-health/health-diagnostics-page-01.png)

您可以利用下列方式啟動健康情況診斷。

* 在 Azure 監視器中來自彙總 VM 檢視方塊之所有 VM 的彙總健康狀態。  在 [健康情況] 頁面的 [客體 VM 健康情況] 區段下方，按一下 [重大]、[警告]、[狀況良好] 或 [未知] 健康狀態，並向下切入至會列出所有符合已篩選分類之 VM 的頁面。  按一下 [健康狀態] 資料行，將會開啟已將範圍設為該特定 VM 的健康情況診斷。      

* 來自 Azure 監視器中彙總 VM 檢視方塊的作業系統。 在 [VM 分佈] 下方，選取任一個資料行值將會開啟 [虛擬機器] 頁面，並在表格中傳回符合已篩選分類的清單。  按一下 [健康狀態] 資料行下方的值，即會開啟所選取 VM 的健康情況診斷。    
 
* 從客體 VM 中，在適用於 VM 的 Azure 監視器 [健康情況] 索引標籤上，選取 [檢視健康情況診斷] 

健康情況診斷會將健康情況資訊組織成下列分類： 

* 可用性
* 效能
 
針對所選取目標定義的所有健康情況準則均會顯示於適當的分類中。 

適用於健康情況準則的健康狀態會由下列三種狀態之一來定義：「重大」、「警告」及「狀況良好」。 另外還有一個狀態「未知」，其與健康狀態無關，但會依功能呈現它的已知監視狀態。  

下表提供健康情況診斷中所呈現之健康狀態的詳細資料。

|圖示 |健康情況狀態 |意義 |
|-----|-------------|------------|
| |Healthy |如果健康狀態位於已定義的健康情況內，則它是狀況良好的。 在父代彙總監視器的案例中，健康情況會彙總，而其會反映子系的最佳或最差狀態。|
| |重要 |如果健康狀態不在已定義的健康情況內，則其為重大的。 在父代彙總監視器的案例中，健康情況會彙總，而其會反映子系的最佳或最差狀態。|
| |警告 |如果健康情況介於已定義健康情況的兩個閾值之間 (一個表示「警告」狀態，另一個表示「重大」狀態)，則其為警告。 在父代彙總監視器的案例中，如果有一個以上的子系處於警告狀態，則父代將反映「警告」狀態。 如果有一個子系處於「重大」狀態且另一個子系處於「警告」狀態，則父代彙總將顯示「重大」的健康狀態。|
| |不明 |當健康狀態基於數種因素 (例如無法收集資料、服務未初始化等等) 而無法計算時，其將會處於「未知」狀態。| 

[健康情況診斷] 頁面具有三個主要區段：

* 元件模型 
* 健康情況準則
* 狀態變更 

![[健康情況診斷] 頁面的區段](./media/monitoring-vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>元件模型
[健康情況診斷] 頁面中最左邊的資料行是元件模型。 所有元件及其探索到的執行個體 (與 VM 相關聯) 均會顯示於此資料行中。 

在下列範例中，探索到的元件為磁碟、邏輯磁碟、處理器、記憶體和作業系統。 已探索到這些元件的多個執行個體並顯示於此資料行中，其中包含邏輯磁碟 **/**、**/boot** 和 **/mnt/resource** 的兩個執行個體、網路介面卡 **eth0** 的一個執行個體、磁碟 **sda** 和 **sdb** 的兩個執行個體、處理器 **0 和 1** 的兩個執行個體，以及 **Red Hat Enterprise Linux Server 7.4 版 (Maipo) (作業系統)**。 

![[健康情況診斷] 中所呈現的範例元件模型](./media/monitoring-vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>健康情況準則
[健康情況診斷] 頁面上的中間資料行為 [健康情況] 資料行。 針對 VM 定義的健康情況模型會以階層樹狀結構來顯示。 VM 的健康情況模型會由單位、相依性和彙總健康情況準則所組成。  

![[健康情況診斷] 中所呈現的範例健康情況準則](./media/monitoring-vminsights-health/health-diagnostics-page-healthcriteria.png)

健康情況準則會使用一些準則來測量受監視執行個體的健康情況，準則可以是閾值或實體狀態等。如上一節所述，健康情況準則具有兩或三個健康狀態。 在任何指定的時間點，健康情況準則都只能處於它的其中一個可能狀態。 

目標的整體健康情況會從其定義於健康情況模型中每個健康情況準則的健康情況來判斷。 這將由在目標上直接設為目標的健康情況準則，以及在透過相依性健康情況準則彙總到目標之元件上設為目標的健康情況準則所組成。 [健康情況診斷] 頁面的 [健康情況準則] 區段中會說明這個階層。 適用於健康情況彙總方式的原則，是彙總與相依性健康情況準則設定的一部分。 您可以在[監視設定詳細資料](#monitoring-configuration-details)一節中，找到預設要當成此功能一部分來執行的健康情況準則組合清單。  

在下列範例中，適用於 Windows 型 VM 的彙總健康情況準則**核心 Windows 服務彙總**會根據個別的服務健康情況準則來評估最重要 Windows 服務的健康情況。 每個服務 (例如 DNS、DHCP 等) 的狀態均會進行評估，並將健康情況彙總到對應的彙總健康情況準則 (如下所示)。  

![健康情況彙總範例](./media/monitoring-vminsights-health/health-diagnostics-windows-svc-rollup.png)

**核心 Windows 服務彙總**的健康情況會彙總到**作業系統可用性**的健康情況，其最終會彙總到 VM 的**可用性**。 

健康情況準則**單位**類型可藉由按一下最右邊的省略符號連結，然後選取 [顯示詳細資料] 以開啟設定窗格，來修改它們的設定。 

![設定健康情況準則範例](./media/monitoring-vminsights-health/health-diagnostics-linuxvm-example-03.png)

在所選取健康情況準則的設定窗格中，在此範例中，可針對其閾值使用不同的數值來設定**邏輯磁碟 %可用空間**，因為它是具有兩個狀態的監視器，這表示它只會從狀況良好變更為重大。  其他的健康情況準則可能是三個狀態，您可以在其中設定警告和重大健康情況狀態閾值的值。  

>[!NOTE]
>將健康情況準則設定變更套用到一個執行個體，適用於所有受監視的執行個體。  例如，如果您選取 **/mnt /mnt/resource** 並修改**邏輯磁碟 %可用空間**的閾值，它不只會套用到該執行個體，還會套用到所有在 VM 上探索到且受監視的其他邏輯磁碟執行個體。
>

![設定單位監視器範例的健康情況準則](./media/monitoring-vminsights-health/health-diagnostics-linuxvm-example-04.png)


### <a name="state-changes"></a>狀態變更
[健康情況診斷] 頁面中最右邊的資料行是**狀態變更**。 它會列出與在 [健康情況準則] 區段中所選取健康狀態準則相關聯的所有狀態變更，或者，如果 VM 選取自資料表的 [元件模型] 或 [健康情況準則] 資料行，則會列出該 VM 的狀態變更。 

![[健康情況診斷] 中所呈現的範例狀態變更](./media/monitoring-vminsights-health/health-diagnostics-page-statechanges.png)

此區段會由健康情況準則狀態，以及由最上方之最新狀態所儲存的相關聯時間所組成。   

### <a name="association-of-component-model-health-criteria-and-state-change-columns"></a>元件模型、健康情況準則和狀態變更資料行的關聯 
這三個資料行彼此交互連結。 當使用者在元件模型中選取探索到的執行個體時，會將 [健康情況準則] 區段篩選到該元件檢視，並根據所選取的健康情況準則相應更新 [狀態變更]。 

![選取受監視的執行個體和結果範例](./media/monitoring-vminsights-health/health-diagnostics-linuxvm-example-02.png)

在上述範例中，當其中一個選取 [/mnt (邏輯磁碟)] 時，即會將 [健康情況準則] 樹狀目錄篩選為 [/mnt (邏輯磁碟)]。 同時也會據以篩選 [可用性] 和 [效能] 索引標籤。 [狀態變更] 資料行會根據 [/mnt (邏輯磁碟)] 的可用性顯示狀態變更。 

若要查看更新的健康狀態，您可以按一下 [重新整理] 連結，重新整理 [健康情況診斷] 頁面。  如果要基於預先定義的輪詢間隔來更新健康情況準則的健康狀態，此工作可讓您避免等候，並反映最新的健康狀態。  [健康情況準則狀態] 是一個篩選條件，可允許您根據所選取的健康情況狀態 (狀況良好、警告、重大、未知及全部) 來設定結果範圍。  右上角的**上次更新**時間表示上次重新整理 [健康情況診斷] 頁面時的時間。  

## <a name="alerting-and-alert-management"></a>警示和警示管理 
適用於 VM 的 Azure 監視器健康情況功能會與 [Azure 警示](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)整合，並在預先定義的健康情況準則於偵測到狀況時從狀況良好變更為狀況不良狀態時引發警示。 警示會依嚴重性 (嚴重性 0 到 4，嚴重性 0 表示最高的嚴重性層級) 分類。  

依嚴重性分類的 VM 健康情況警示總數可在 [健康情況] 儀表板的 [警示] 區段下方取得。 當您選取警示總數或對應到嚴重性層級的數字時，[警示] 頁面隨即開啟並列出所有符合您選取項目的警示。  例如，如果您選取了對應至**嚴重性層級 1** 的資料列，則會看到下列檢視：

![所有嚴重性層級 1 的警示範例](./media/monitoring-vminsights-health/vminsights-sev1-alerts-01.png)

您可以選取頁面頂端下拉式功能表中的值來篩選此檢視。

|欄 |說明 | 
|-------|------------| 
|訂用帳戶 |選取 Azure 訂用帳戶。 檢視僅會包含所選訂用帳戶中出現的警示。 | 
|資源群組 |選取單一資源群組。 檢視僅會包含所選資源群組中具有目標的警示。 | 
|資源類型 |選取一個或多個資源類型。 檢視僅會包含所選類型目標之具目標的警示。 指定資源群組之後，才可使用此欄。 | 
|資源 |選取資源。 只有以該資源作為目標的警示才會包含在檢視中。 指定資源類型之後，才可使用此欄。 | 
|嚴重性 |選取警示嚴重性，或選取 [全部] 以包含所有嚴重性的警示。 | 
|監視條件 |選取監視條件來篩選警示，但前提是系統已「引發」它們，或者如果該條件不再使用中，則系統已「解決」它們。 或者，選取 [全部] 來包含所有條件的警示。 | 
|警示狀態 |選取警示狀態 (「新的」、「認可」、「已關閉」)，或選取 [全部] 以包含所有狀態的警示。 | 
|監視器服務 |選取服務，或選取 [所有] 以包含所有服務。 此功能僅支援來自基礎結構深入解析的警示。 | 
|時間範圍| 只有在所選時間範圍內引發的警示才會包含在檢視中。 支援的值為過去 1 小時、過去 24 小時、過去 7 天和過去 30 天。 | 

[警示詳細資料] 頁面會在您選取警示時顯示，以提供警示的詳細資料並讓您變更其狀態。 若要深入了解如何使用警示規則和管理警示，請參閱[使用 Azure 監視器來建立、檢視及管理警示](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)。

![所選取警示的 [警示詳細資料] 窗格](./media/monitoring-vminsights-health/alert-details-pane-01.png)

您也可以藉由選取警示，然後從 [所有警示] 頁面的左上角選取 [變更狀態]，針對一或多個警示變更警示狀態。 在 [變更警示狀態] 窗格中，您會選取其中一種狀態、在 [註解] 欄位中新增變更的說明，然後按一下 [確定] 來認可變更。 在驗證資訊並套用變更之後，您可以在功能表的 [通知] 底下追蹤其進度。  

## <a name="next-steps"></a>後續步驟
若要找出 VM 效能的瓶頸和整體使用率，請參閱[檢視 Azure VM 效能](monitoring-vminsights-performance.md)，或者，若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](monitoring-vminsights-maps.md)。 