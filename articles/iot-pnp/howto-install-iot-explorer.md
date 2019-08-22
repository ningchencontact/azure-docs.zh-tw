---
title: 安裝和使用 Azure IoT explorer |Microsoft Docs
description: 安裝 Azure IoT explorer 工具, 並使用它來與連線到 IoT 中樞的 IoT 隨插即用預覽裝置互動。
author: miagdp
ms.author: miag
ms.date: 07/02/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: c7d8b946fc587d58f13e16c1d7d806390e712104
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879548"
---
# <a name="install-and-use-azure-iot-explorer"></a>安裝和使用 Azure IoT explorer

Azure IoT explorer 是一種圖形化工具, 可與您的 IoT 隨插即用預覽裝置互動和測試。 在本機電腦上安裝此工具之後, 您就可以使用它來連線到裝置。 您可以使用此工具來查看裝置正在傳送的遙測資料、使用裝置屬性, 以及呼叫命令。

本文將說明如何：

- 安裝和設定 Azure IoT explorer 工具。
- 使用此工具來與您的裝置互動並加以測試。

## <a name="prerequisites"></a>必要條件

若要使用 Azure IoT explorer 工具, 您需要:

- Azure IoT 中樞。 有許多方法可將 IoT 中樞新增至您的 Azure 訂用帳戶, 例如[使用 Azure CLI 建立 iot 中樞](../iot-hub/iot-hub-create-using-cli.md)。 您需要 IoT 中樞連接字串, 才能執行 Azure IoT explorer 工具。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。
- 在 IoT 中樞註冊的裝置。 您可以使用下列 Azure CLI 命令來註冊裝置。 請務必以您的`{YourIoTHubName}`值`{YourDeviceID}`取代和預留位置:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>安裝 Azure IoT explorer

移至[Azure IoT explorer [發行](https://github.com/Azure/azure-iot-explorer/releases)], 並展開最新版本的資產清單。 下載並安裝最新版本的應用程式。

## <a name="use-azure-iot-explorer"></a>使用 Azure IoT explorer

針對裝置, 您可以連接您自己的裝置, 或使用我們的其中一個範例模擬裝置。 請遵循[這些指示](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples)來執行模擬裝置範例。

### <a name="connect-to-your-hub"></a>連接到您的中樞

第一次執行 Azure IoT explorer 時, 系統會提示您輸入 IoT 中樞的連接字串。 新增連接字串之後, 請選取 **[連接]** 。 藉由更新連接字串, 您可以使用此工具的設定來切換至另一個 IoT 中樞。

IoT 隨插即用裝置的模型定義會儲存在公用儲存機制、公司存放庫或已連線的裝置中。 根據預設, 此工具會在公用模型存放庫和已連線的裝置中尋找您的模型定義。 您可以新增和移除來源, 或在 [**設定**] 中設定來源的優先順序:

若要新增來源:

1. 移至 [設定]。
1. 選取 [**新增**], 然後選擇您的來源。
1. 如果您要新增公司模型存放庫, 請提供連接字串。

若要移除來源:

1. 移至 [設定]。
1. 尋找您要移除的來源。
1. 選取**X**以將它移除。 因為通用介面定義來自此存放庫, 所以您無法移除該公用模型存放庫。

變更來源優先順序:

您可以將其中一個模型定義來源拖放到清單中的不同等級。 如果發生衝突, 則具有較高排名的定義來源會覆寫具有較低排名的來源。

### <a name="overview-page"></a>概觀分頁

#### <a name="device-overview"></a>裝置總覽

在此工具連接到您的 IoT 中樞之後, 它會顯示一個 [總覽] 頁面, 其中列出向 Azure IoT 中樞註冊的所有裝置身分識別。 選取裝置以查看更多詳細資料。

#### <a name="device-management"></a>裝置管理

- 若要向您的中樞註冊新的裝置, 請選取 [新增]。 輸入 [裝置識別碼]。 使用預設設定來自動產生驗證金鑰, 並啟用與中樞的連線。
- 若要刪除裝置身分識別, 請選取 [**刪除**]。 完成此動作之前, 請先檢查裝置詳細資料, 以確定您正在刪除正確的裝置身分識別。
- 此工具支援`capabilityID`和`interfaceID`查詢。 新增您`capabilityID`的或`interfaceID`做為參數, 以查詢您的裝置。

## <a name="interact-with-a-device"></a>與裝置互動

按兩下 [總覽] 頁面上的裝置, 以查看下一個層級的詳細資料。 有兩個區段:**裝置**和**數位**對應項。

### <a name="device"></a>裝置

本節包含裝置身分**識別**、**遙測**和**裝置**對應項索引標籤。

- 您可以在 [**裝置身分識別**] 索引標籤上, 查看並更新裝置身分識別資訊。
- 如果裝置已連線並主動傳送資料, 您可以在 [**遙測**] 索引標籤上查看遙測。
- 您可以在 [**裝置**對應項] 索引標籤上存取裝置對應項資訊。

### <a name="digital-twin"></a>數位對應項

您可以使用此工具來查看裝置的數位對應項實例。 針對 IoT 隨插即用裝置, 與裝置功能模型相關聯的所有介面都會顯示在本文中。 選取介面, 以展開其對應的[IoT 隨插即用基本](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)專案。

#### <a name="properties"></a>屬性

您可以在 [**屬性**] 頁面上, 查看介面中定義的唯讀屬性。 您可以在 [可**寫入屬性**] 頁面上, 更新介面中定義的可寫入屬性。

1. 移至 [**可寫入屬性**] 頁面。
1. 按一下您要更新的屬性。
1. 輸入屬性的新值。
1. 預覽要傳送至裝置的承載。
1. 提交變更。

提交變更之後, 您可以追蹤更新狀態: [**同步**]、[**成功**] 或 [**錯誤**]。 當同步完成時, 您會在 [**回報的屬性**] 資料行中看到屬性的新值。 如果您在同步完成之前流覽至其他頁面, 此工具仍會在更新完成時通知您。 您也可以使用工具的通知中心查看通知歷程記錄。

#### <a name="commands"></a>命令

若要將命令傳送至裝置, 請移至 [**命令**] 頁面:

1. 在命令清單中, 展開您要觸發的命令。
1. 輸入命令所需的任何值。
1. 預覽要傳送至裝置的承載。
1. 提交命令。

#### <a name="telemetry"></a>遙測

若要查看所選介面的遙測, 請移至其 [**遙測**] 頁面。

## <a name="next-steps"></a>後續步驟

在此操作說明文章中, 您已瞭解如何安裝和使用 Azure IoT explorer, 以與您的 IoT 隨插即用裝置互動。 建議的下一個步驟是瞭解如何[管理模型](./howto-manage-models.md)。
