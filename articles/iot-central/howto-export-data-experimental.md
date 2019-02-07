---
title: 匯出 Azure IoT Central 中的資料 | Microsoft Docs
description: 如何從 Azure IoT Central 應用程式匯出資料
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/05/2017
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 15697f97a3b223e0a946795ce0dc5e9d3d86ccb8
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55771984"
---
# <a name="export-your-data-in-azure-iot-central"></a>匯出 Azure IoT Central 中的資料

*此主題適用於系統管理員。*

本文說明如何使用 Azure IoT Central 中的連續資料匯出功能，將資料匯出到您自己的 **Azure Blob 儲存體**、**Azure 事件中樞**及 **Azure 服務匯流排**執行個體。 您可以將**度量**、**裝置**及**裝置範本**匯出到您自己的目的地，以進行暖路徑和冷路徑分析。 您可以將資料匯出至 Blob 儲存體，以在 Microsoft Power BI 中執行長期趨勢分析，或將資料匯出至「事件中樞」和「服務匯流排」，以使用 Azure Logic Apps 或 Azure Functions 以近乎即時的方式轉換及擴增資料。

> [!Note]
> 當您開啟「連續資料匯出」時，只會取得從該時刻的資料。 目前，無法擷取「連續資料匯出」時的資料。 若要保留更多歷史資料，請儘早開啟「連續資料匯出」。

## <a name="prerequisites"></a>必要條件

- 您必須是 IoT Central 應用程式中的系統管理員

## <a name="export-to-blob-storage"></a>匯出至 Blob 儲存體

度量、裝置及裝置範本資料會以每分鐘一次的頻率，匯出到您的儲存體帳戶，其中每個檔案都會包含自上次匯出檔案之後的一批變更。 匯出的資料會採用 [Apache AVRO](https://avro.apache.org/docs/current/index.html) 格式。

深入了解[匯出至 Azure Blob 儲存體](howto-export-data-blob-storage.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)。

## <a name="export-to-event-hubs-and-service-bus"></a>匯出至事件中樞和服務匯流排

度量、裝置及裝置範本資料會匯出至事件中樞或「服務匯流排」佇列或主題。 匯出的度量資料會以近乎即時的方式送達，且包含您裝置傳送給 IoT Central 的訊息整體，而不僅限於度量本身的值。 匯出的裝置資料會以每分鐘一次的方式分批送達，且包含對所有裝置之屬性與設定進行的變更，而匯出的裝置範本則包含對所有裝置範本進行的變更。


深入了解[匯出至事件中樞和服務匯流排](howto-export-data-event-hubs-service-bus.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)。

## <a name="set-up-export-destination"></a>設定匯出目的地

如果您沒有現有的「儲存體」/「事件中樞」/「服務匯流排」可供作為匯出目的地，請依照下列步驟進行操作：

### <a name="create-storage-account"></a>建立儲存體帳戶

1. [在 Azure 入口網站中建立新儲存體帳戶](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 您可以透過 [Azure 儲存體文件](https://aka.ms/blobdocscreatestorageaccount)深入了解。
2. 針對帳戶類型，請選擇 [一般用途] 或 [Blob 儲存體]。
3. 選擇訂用帳戶。 

    > [!Note] 
    > 您現在可以將資料匯出至與預付型方案 IoT Central 應用程式之訂用帳戶**不同**的其他訂用帳戶。 在此情況下，您將使用連接字串來進行連線。

4. 在您的儲存體帳戶中建立容器。 移至您的儲存體帳戶。 在 [Blob 服務] 下，選取 [瀏覽 Blob]。 選取頂端的 [+ 容器] 以建立新的容器。

### <a name="create-event-hubs-namespace"></a>建立事件中樞命名空間

1. [在 Azure 入口網站中建立新事件中樞命名空間](https://ms.portal.azure.com/#create/Microsoft.EventHub)。 您可以透過 [Azure 事件中樞文件](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)深入了解。
2. 選擇訂用帳戶。 

    > [!Note] 
    > 您現在可以將資料匯出至與預付型方案 IoT Central 應用程式之訂用帳戶**不同**的其他訂用帳戶。 在此情況下，您將使用連接字串來進行連線。
3. 在「事件中樞」命名空間中建立一個事件中樞。 移至您的命名空間，然後選取頂端的 [+ 事件中樞] 以建立事件中樞執行個體。

### <a name="create-service-bus-namespace"></a>建立服務匯流排命名空間

1. [在 Azure 入口網站中建立新的服務匯流排命名空間](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)。 您可以透過 [Azure 服務匯流排文件](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal)深入了解。
2. 選擇訂用帳戶。 

    > [!Note] 
    > 您現在可以將資料匯出至與預付型方案 IoT Central 應用程式之訂用帳戶**不同**的其他訂用帳戶。 在此情況下，您將使用連接字串來進行連線。

3. 移至您的「服務匯流排」命名空間，然後選取頂端的 [+ 佇列] 或 [+ 主題] 以建立要作為匯出目的地的佇列或主題。

## <a name="set-up-continuous-data-export"></a>設定連續資料匯出

既然您已有可作為資料匯出目的地的「儲存體」/「事件中樞」/「服務匯流排」，現在即可依照下列步驟來設定連續資料匯出。 

1. 登入您的 IoT Central 應用程式。

2. 在左側功能表中，按一下 [連續資料匯出]。

    > [!Note]
    > 如果在左側功能表中沒有看到 [連續資料匯出]，即表示您不是應用程式的系統管理員。 請連絡系統管理員來設定資料匯出。

    ![建立新的 cde 事件中樞](media/howto-export-data-experimental/export_menu.png)

3. 按一下右上方的 [+ 新增] 按鈕。 選擇 [Azure Blob 儲存體]、[Azure 事件中樞]或 [Azure 服務匯流排] 其中一個作為您的匯出目的地。 

    > [!NOTE] 
    > 每一應用程式的匯出數目上限是 5。 

    ![建立新的連續資料匯出](media/howto-export-data-experimental/export_new.png)

4. 在下拉式清單中，選取您的 [儲存體帳戶]/[事件中樞命名空間]/[服務匯流排命名空間]。 您也可以挑選清單中的最後一個選項，也就是 [輸入連接字串]。 

    > [!NOTE] 
    > 您只會看到**與 IoT Central 應用程式位於相同訂用帳戶中**的「儲存體帳戶」/「事件中樞命名空間」/「服務匯流排命名空間」。 如果您想要匯出到此訂用帳戶外的目的地，請選擇 [輸入連接字串]，然後參閱步驟 5。

    > [!NOTE] 
    > 如果是 7 天試用版應用程式，則設定連續資料匯出的唯一方式是透過連接字串。 這是因為 7 天試用版應用程式並沒有相關聯的 Azure 訂用帳戶。

    ![建立新的 cde 事件中樞](media/howto-export-data-experimental/export_create.png)

5. (選擇性) 如果您選擇 [輸入連接字串]，就會顯示一個可供您貼上連接字串的新方塊。 取得下列項目的連接字串：
    - 儲存體帳戶：在 Azure 入口網站中移至儲存體帳戶。
        - 在 [設定] 底下，按一下 [存取金鑰]
        - 複製 key1 連接字串或 key2 連接字串
    - 事件中樞或服務匯流排：在 Azure 入口網站中移至命名空間。
        - 在 [設定] 底下，按一下 [共用存取原則]
        - 選擇預設的 [RootManageSharedAccessKey]，或建立新的原則
        - 複製主要連接字串或次要連接字串
 
6. 從下拉式清單方塊中選擇 [容器]/[事件中樞]/[佇列或主題]。

7. 在 [要匯出的資料] 下，透過將類型設定為 [開啟] 以指定每種要匯出的資料類型。

6. 若要開啟連續資料匯出，請務必將 [資料匯出] 設定為 [開啟]。 選取 [ **儲存**]。

  ![設定連續資料匯出](media/howto-export-data-experimental/export_list.png)

7. 幾分鐘後，您的資料就會出現在所選擇的目的地中。

## <a name="next-steps"></a>後續步驟

現在您已經知道如何匯出資料，請繼續下一個步驟：

> [!div class="nextstepaction"]
> [將資料匯出至 Azure Blob 儲存體](howto-export-data-blob-storage.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

> [!div class="nextstepaction"]
> [將資料匯出至 Azure 事件中樞和 Azure 服務匯流排](howto-export-data-event-hubs-service-bus.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

> [!div class="nextstepaction"]
> [如何在 Power BI 中將資料視覺化](howto-connect-powerbi.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
