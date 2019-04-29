---
title: 了解 Azure IoT 中樞訊息路由 | Microsoft Docs
description: 開發人員指南 - 如何使用訊息路由來傳送裝置到雲端訊息。 包含關於傳送遙測和非遙測資料的資訊。
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: dc5bfe6b431659b7b99140eb29a0e64922a42275
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61364474"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>使用 IoT 中樞訊息路由來傳送裝置到雲端訊息至不同的端點

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

訊息路由可讓您以自動化、可調整且可靠的方式，將裝置的訊息傳送到雲端服務。 訊息路由可用於： 

* **傳送裝置遙測訊息以及事件** (也就是裝置生命週期事件和裝置對應項變更事件) 到內建端點和自訂端點。 深入了解[路由端點](#routing-endpoints)。

* 套用多種查詢**篩選資料再將資料傳送到眾多端點**。 訊息路由可讓您對訊息屬性和訊息本文，以及裝置對應項標記和裝置對應項屬性進行查詢。 進一步了解使用[訊息路由中的查詢](iot-hub-devguide-routing-query-syntax.md)。

IoT 中樞需要這些服務端點的寫入權限，才能將訊息路由傳送至工作。 如果您透過 Azure 入口網站設定您的端點，則會為您新增必要的權限。 請務必設定您的服務，以支援預期的輸送量。 當您第一次設定您的 IoT 解決方案時，您可能需要監視其他端點，然後對實際負載進行必要的調整。

IoT 中樞會針對所有裝置到雲端訊息定義[常見格式](iot-hub-devguide-messages-construct.md)，以在通訊協定之間提供互通性。 如果訊息符合多個指向相同端點的路由，則 IoT 中樞只會將訊息傳遞至該端點一次。 因此，您不需要在您的服務匯流排佇列或主題上設定重複資料刪除。 在分割佇列中，分割區同質性可保證訊息排序。 本教學課程可用來了解如何[設定訊息路由](tutorial-routing.md)。

## <a name="routing-endpoints"></a>路由端點

IoT 中樞都有與事件中樞相容的預設內建端點 (**訊息/事件**)。 您可以將訂用帳戶中的其他服務連結到 IoT 中樞，來建立要路由傳送訊息的目標[自訂端點](iot-hub-devguide-endpoints.md#custom-endpoints)。 IoT 中樞目前支援下列服務做為自訂端點︰

### <a name="built-in-endpoint"></a>內建端點

您可以使用標準[事件中樞整合和 SDK](iot-hub-devguide-messages-read-builtin.md) 接收內建端點 (**訊息/事件**) 的裝置到雲端訊息。 在创建一个路由后，数据将停止流向内置终结点，除非创建了到该终结点的路由。

### <a name="azure-blob-storage"></a>Azure Blob 儲存體

IoT 中心支持将数据以 [Apache Avro](https://avro.apache.org/) 和 JSON 格式写入 Azure Blob 存储。 將 JSON 格式編碼的功能，在所有可使用 IoT 中樞的區域中均為預覽狀態，但美國東部、美國西部和歐洲西部除外。 預設值為 AVRO。 只有在配置 Blob 存储终结点时才能设置编码格式。 不能编辑现有终结点的格式。 使用 JSON 编码时，必须在消息[系统属性](iot-hub-devguide-routing-query-syntax.md#system-properties)中将 contentType 设置为 JSON，将 contentEncoding 设置为 UTF-8。 可以使用 IoT 中心的创建或更新 REST API（具体说来就是 [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties)、Azure 门户、[Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest) 或 [Azure Powershell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0)）选择编码格式。 下图说明如何在 Azure 门户中选择编码格式。

![Blob 存储终结点编码](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

「IoT 中樞」會批次處理訊息，然後在批次達到特定大小或經過一段特定時間之後，就將資料寫入至 Blob。 「IoT 中樞」預設會採用下列檔案命名慣例：

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

您可以使用任何檔案命名慣例，不過，必須使用所有列出的語彙基元。 如果沒有資料可供寫入，IoT 中樞會寫入空的 Blob。

路由傳送至 Blob 儲存體時，建議您登錄 Blob，然後逐一查看它們，以確保會讀取所有容器，而不需進行任何分割假設。 分割範圍可能會在 [Microsoft 起始的容錯移轉](iot-hub-ha-dr.md#microsoft-initiated-failover)或 IoT 中樞[手動容錯移轉](iot-hub-ha-dr.md#manual-failover-preview)期間變更。 可以使用 [List Blobs API](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 来枚举 Blob 的列表。 请将以下示例作为指南来查看。

   ```csharp
        public void ListBlobsInContainer(string containerName, string iothub)
        {
            var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
            var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
            if (cloudBlobContainer.Exists())
            {
                var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
                foreach (IListBlobItem item in results)
                {
                    Console.WriteLine(item.Uri);
                }
            }
        }
   ```

### <a name="service-bus-queues-and-service-bus-topics"></a>服務匯流排佇列和服務匯流排主題

做為 IoT 中樞端點的服務匯流排佇列和主題不能啟用 [工作階段] 或 [重複偵測]。 如果已啟用其中一個選項，端點會在 Azure 入口網站中顯示為 [無法連線]。

### <a name="event-hubs"></a>事件中樞

除了內建事件中樞相容端點之外，您也可以將資料傳送至事件中樞類型的自訂端點。 

## <a name="reading-data-that-has-been-routed"></a>讀取已傳送的資料

您可以遵循此[教學課程](tutorial-routing.md)設定路由。

下列教學課程可用來了解如何從端點讀取訊息。

* 從[內建端點](quickstart-send-telemetry-node.md)讀取

* 從 [Blob 儲存體](../storage/blobs/storage-blob-event-quickstart.md)讀取

* 從[事件中樞](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)讀取

* 從[服務匯流排佇列](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)讀取

* 從[服務匯流排主題](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)讀取

## <a name="fallback-route"></a>後援路由

後援路由會將與任何現有路由查詢條件不符的所有訊息，傳送至與[事件中樞](/azure/event-hubs/)相容的內建事件中樞 (**訊息/事件**)。 如果訊息路由已開啟，您即可啟用後援路由功能。 在创建一个路由后，数据将停止流向内置终结点，除非创建了到该终结点的路由。 如果內建端點沒有路由，而且後援路由已啟用，則與路由的任何查詢條件不符的訊息會傳送至內建端點。 此外，如果刪除所有的現有路由，必須啟用後援路由接收內建端點的所有資料。 

在 Azure 入口網站 -> [訊息路由] 刀鋒視窗 中，您可以啟用/停用後援路由。 您也可以對於 [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) 使用 Azure 資源管理員來使用後援路由的自訂端點。

## <a name="non-telemetry-events"></a>非遙測事件

除了裝置遙測之外，訊息路由也可啟用傳送裝置對應項變更事件和裝置生命週期事件。 例如，如果隨同設定為**裝置對應項變更事件**的資料來源建立路由，IoT 中樞會將訊息傳送至包含裝置對應項變更的端點。 同樣地，如果隨同設定為**裝置生命週期事件**的資料來源建立路由，IoT 中樞會傳送訊息，指出是否已刪除或建立裝置。 

[IoT 中樞也與 Azure Event Grid 整合](iot-hub-event-grid.md)發佈裝置事件，以根據這些事件支援即時整合和工作流程自動化。 請參閱[訊息路由與事件格線之間的關鍵差異](iot-hub-event-grid-routing-comparison.md)了解何者最適合您的情況。

## <a name="testing-routes"></a>測試路由

您建立新的路由或編輯現有的路由時，應該使用範例訊息測試路由查詢。 您可以測試個別的路由，也可以一次測試所有路由，而且沒有任何訊息在測試期間傳送至端點。 Azure 入口網站、Azure Resource Manager、Azure PowerShell 和 Azure CLI 均可用於測試。 結果會協助識別是否符合查詢的範例訊息，訊息不符合查詢中，或無法執行測試，因為範例訊息或查詢語法不正確。 若要深入了解，請參閱[測試路由](/rest/api/iothub/iothubresource/testroute)和[測試所有路由](/rest/api/iothub/iothubresource/testallroutes)。

## <a name="latency"></a>Latency

當您使用內建端點來路由傳送裝置到雲端的遙測訊息時，在建立第一個路由之後，端對端延遲會稍微增加。

在大部分情況下，平均增加的延遲會是小於 500 毫秒。 您可以使用**路由：訊息/事件的訊息延遲**或 **d2c.endpoints.latency.builtIn.events** IoT 中樞計量來監視延遲。 建立或刪除第一個之後的任何路由並不會影響端對端延遲。

## <a name="monitoring-and-troubleshooting"></a>監視與疑難排解

IoT 中樞提供數個路由和端點相關計量，以讓您概略了解中樞的健全狀況和傳送的訊息。 您可以結合多個計量的資訊，以識別問題的根本原因。 比方說，使用計量**路由： 捨棄的遙測訊息**或是**d2c.telemetry.egress.dropped**來識別它們不符合任何路由上的查詢時，雖已捨棄的訊息數目且後援路由已停用。 [IoT 中樞計量](iot-hub-metrics.md)列出對於 IoT 中樞預設啟用的所有計量。

可以使用 REST API [Get Endpoint Health](https://docs.microsoft.com/de-de/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) 获取终结点的[运行状况状态](iot-hub-devguide-endpoints.md#custom-endpoints)。 当终结点运行状况为故障或不正常时，建议使用与路由消息延迟相关的 [IoT 中心指标](iot-hub-metrics.md)来标识并调试错误。 例如，对于终结点类型“事件中心”，可以监视 **d2c.endpoints.latency.eventHubs**。 当 IoT 中心建立了最终一致的运行状况状态以后，系统会将不正常终结点的状态更新为正常。

使用 Azure 監視器[診斷設定](../iot-hub/iot-hub-monitor-resource-health.md)中的**路由**診斷記錄，即可追蹤評估路由查詢期間發生的錯誤，以及 IoT 中樞察覺的端點健全狀況，例如端點無作用的喬況。 可以将这些诊断日志发送到 Azure Monitor 日志、事件中心或 Azure 存储进行自定义处理。

## <a name="next-steps"></a>後續步驟

* 如需了解如何建立訊息路由，請參閱[使用路由處理 IoT 中樞的裝置到雲端訊息](tutorial-routing.md)。

* [如何傳送裝置到雲端的訊息](quickstart-send-telemetry-node.md)

* 如需您可以用來傳送裝置對雲端訊息之 SDK 的資訊，請參閱 [Azure IoT SDK](iot-hub-devguide-sdks.md)。
