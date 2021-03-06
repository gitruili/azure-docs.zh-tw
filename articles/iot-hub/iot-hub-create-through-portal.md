---
title: 使用 Azure 入口網站建立 IoT 中樞 | Microsoft Docs
description: 如何透過 Azure 入口網站建立、管理和刪除 Azure IoT 中樞。 其中包括定價層、調整、安全性和傳訊組態的相關資訊。
author: dominicbetts
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: dobett
ms.openlocfilehash: 0b03ae434e93dbab45235fe67c499497e1257064
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2018
ms.locfileid: "42141288"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>使用 Azure 入口網站建立 IoT 中樞

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

本文章說明：

* 如何在 Azure 入口網站中找到 IoT 中樞服務。
* 如何建立和管理 IoT 中樞。

## <a name="where-to-find-the-iot-hub-service"></a>IoT 中樞服務的所在位置

您可以在入口網站的下列位置中找到 IoT 中樞服務：

* 選擇 [+ 新增]，然後選擇 [物聯網]。
* 在 Marketplace 中，選擇 [物聯網]。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

您可以使用下列方法建立 IoT 中樞：

* [+ 新增] 選項會開啟下列螢幕擷取畫面中顯示的刀鋒視窗。 透過這個方法以及透過 Marketplace 建立 IoT 中樞的步驟完全相同。

* 在 Marketplace 中，選擇 [建立] 以開啟下列螢幕擷取畫面中顯示的刀鋒視窗。

下列幾節說明建立 IoT 中樞的幾個步驟。

### <a name="choose-the-name-of-the-iot-hub"></a>選擇 IoT 中樞的名稱

若要建立 IoT 中樞，您必須替 IoT 中樞命名。 此名稱不得與任何 IoT 中樞之名稱重複。

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

### <a name="choose-the-pricing-tier"></a>選擇定價層。

您可以依據所需的功能多寡，以及每天透過解決方案傳送的訊息多寡，從數個層級中做選擇。 免費層適用於測試和評估。 它可允許 500 個裝置連接到 IoT 中樞，每天最多可允許 8,000 則訊息。 每個 Azure 訂用帳戶可以在免費層建立一個「IoT 中樞」。 

如需有關其他層級選項的詳細資料，請參閱[選擇適合的 IoT 中樞層](iot-hub-scaling.md)。

### <a name="iot-hub-units"></a>IoT 中樞單位

每天每單位允許的訊息數目取決於您的中樞定價層。 例如，如果您想要 IoT 中樞支援 700,000 封訊息的輸入，您可以選擇 2 個 S1 層單位。

### <a name="device-to-cloud-partitions-and-resource-group"></a>裝置到雲端分割及資源群組

您可以變更 IoT 中樞的分割數目。 分割的預設數目是 4；您可以從下拉式清單中選擇不同的數字。

您不需要明確建立空的資源群組。 您可以在建立資源時，選擇建立新的資源群組，或使用現有的資源群組。

![顯示在 Azure 入口網站中建立中樞的螢幕擷取畫面](./media/iot-hub-create-through-portal/location1.png)

### <a name="choose-subscription"></a>選擇訂用帳戶

Azure IoT 中樞會自動列出使用者帳戶所連結的 Azure 訂用帳戶。 您可以選擇要與 IoT 中樞相關聯的 Azure 訂用帳戶。

### <a name="choose-the-location"></a>選擇位置

[位置] 選項提供可在其中使用 IoT 中樞的區域清單。

### <a name="create-the-iot-hub"></a>建立 IoT 中樞

完成上述的所有步驟之後，您便可以建立 IoT 中樞。 按一下 [建立] 以啟動後端程序，並透過您選擇的選項建立和部署 IoT 中樞。

由於要在適當的位置伺服器上執行後端部署需要時間，因此建立 IoT 中樞會需要幾分鐘的時間。

## <a name="change-the-settings-of-the-iot-hub"></a>變更 IoT 中樞的設定
<!--robinsh these screenshots are out of date -->

從 IoT 中樞刀鋒視窗建立 IoT 中樞後，您可以變更此現有 IoT 中樞的設定。

![顯示 IoT 中樞設定的螢幕擷取畫面](./media/iot-hub-create-through-portal/portal-settings.png)

**共用存取原則**：這些原則定義了裝置與服務連接至 IoT 中樞的權限。 您可以按一下 [一般] 之下的 [共用存取原則] 來存取這些原則。 在這個刀鋒視窗中，您可以修改現有的原則或新增原則。

### <a name="create-a-policy"></a>建立原則

* 按一下 [新增] 開啟刀鋒視窗。 您可以在此輸入新的原則名稱以及您想要與此原則產生關聯的權限，如在下一個圖中所示：

    有許多權限可與這些共用原則產生關聯。 [登錄讀取] 和 [登錄寫入] 原則會授與讀取和寫入存取權給身分識別登錄。 選擇寫入選項就會自動選擇讀取選項。

    [服務連線] 原則會授與存取服務端點的權限，例如**接收裝置到雲端**。 [裝置連線] 原則會授與使用 IoT 中樞裝置端端點傳送和接收訊息的權限。

* 按一下 [建立]  將此新建立的原則新增至現有的清單。

   ![顯示新增共用存取原則的螢幕擷取畫面](./media/iot-hub-create-through-portal/shared-access-policies.png)

## <a name="endpoints"></a>端點

按一下 [端點] 以顯示您正在修改之 IoT 中樞的端點清單。 端點有兩個類型︰IoT 中樞內建的端點，以及您在 IoT 中樞建立後新增的端點。

![顯示新增端點的螢幕擷取畫面](./media/iot-hub-create-through-portal/messaging-settings.png)

### <a name="built-in-endpoints"></a>內建端點

有兩種內建端點︰**雲端到裝置回饋**和**事件**。

* **雲端到裝置回饋**設定：此設定有 2 個子設定：訊息的**雲端到裝置 TTL** (存留時間) 和**保留時間** (以小時為單位)。 當您第一次建立 IoT 中樞時，這兩個設定的預設值都是一個小時。 若要調整這些設定，可使用滑桿或輸入值。

* **事件**設定：這個設定有數個子設定，其中有些是唯讀。 下列清單說明這些設定：

  * **分割**：當 IoT 中樞建立後會設定預設值。 您可以透過此設定變更分割數目。

  * **事件中樞相容的名稱和端點**：建立 IoT 中樞時，事件中樞會在內部建立，而您在某些情況下可能需要其存取權。 您無法自訂事件中樞的相容名稱和端點值，但可以按一下 [複製] 來複製它們。

  * **保留時間**：預設為一天，但可以使用下拉式清單變更。 這是裝置到雲端設定以天為單位的值。

  * **取用者群組**：取用者群組可讓多個讀取器從 IoT 中樞獨立讀取訊息。 每個 IoT 中樞都是使用預設取用者群組建立的。 不過，您可以使用這個設定新增或刪除 IoT 中樞的取用者群組。

  > [!NOTE]
  > 預設的取用者群組無法編輯或刪除。

### <a name="custom-endpoints"></a>自訂端點

您可以使用入口網站在 IoT 中樞上新增自訂端點。 從 [端點] 刀鋒視窗，按一下頂端的 [新增] 以開啟 [新增端點] 刀鋒視窗。 輸入必要資訊，然後按一下 [確定]。 您的自訂端點現在會列在主要 [端點] 刀鋒視窗中。

![顯示建立自訂端點的螢幕擷取畫面](./media/iot-hub-create-through-portal/endpoint-creation.png)

您可以在[參考 - IoT 中樞端點]( iot-hub-devguide-endpoints.md)中深入了解自訂端點。

## <a name="routes"></a>路由

按一下 [路由] 以管理 IoT 中樞分派您的裝置到雲端訊息的方式。

![顯示新增路由的螢幕擷取畫面](./media/iot-hub-create-through-portal/routes-list.png)

您可以將路由新增至 IoT 中樞，方法是按一下 [路由]* 刀鋒視窗頂端的 [新增]、輸入必要資訊，然後按一下 [確定]。 接著您的路由會列在主要 [路由] 刀鋒視窗中。 您可以在路由清單中按一下路由來編輯它。 若要啟用路由，按一下路由清單中的路由，並將 [已啟用] 切換為 [關閉]。 若要儲存變更，請按一下刀鋒視窗底部的 [確定]。

![顯示編輯新路由規則的螢幕擷取畫面](./media/iot-hub-create-through-portal/route-edit.png)

## <a name="delete-the-iot-hub"></a>刪除 IoT 中樞

您可以藉由按一下 [瀏覽] ，然後選擇要刪除的適當中樞，即可瀏覽至您想要刪除的 IoT 中樞。 若要刪除 IoT 中樞，請按一下 IoT 中樞名稱下方的 [刪除] 按鈕。

## <a name="next-steps"></a>後續步驟

遵循下列連結以深入了解如何管理 Azure IoT 中樞：

* [大量管理 IoT 裝置](iot-hub-bulk-identity-mgmt.md)
* [IoT 中樞計量](iot-hub-metrics.md)
* [作業監視](iot-hub-operations-monitoring.md)

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中樞開發人員指南](iot-hub-devguide.md)
* [使用 Azure IoT Edge 將 AI 部署到 Edge 裝置](../iot-edge/tutorial-simulate-device-linux.md)
* [徹底保護您的 IoT 解決方案](../iot-fundamentals/iot-security-ground-up.md)
