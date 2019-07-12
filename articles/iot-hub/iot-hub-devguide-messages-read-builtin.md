---
title: 了解 Azure IoT 中樞內建端點 | Microsoft Docs
description: 開發人員指南：說明如何使用內建的事件中樞相容端點來讀取裝置到雲端訊息。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 827d7d9a3d584342703a84dd2a42e5cda9b3a656
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61363957"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>從內建端點讀取裝置對雲端訊息

根據預設，訊息會路由到與[事件中樞](https://azure.microsoft.com/documentation/services/event-hubs/)相容的內建服務對應端點 (**訊息/事件**)。 此端點目前只連接埠 5671 上使用 [AMQP](https://www.amqp.org/) 通訊協定公開。 IoT 中樞會公開下列屬性，讓您控制與事件中樞相容的內建訊息端點 **messages/events**。

| 屬性            | 描述 |
| ------------------- | ----------- |
| **分割計數** | 在建立時設定此屬性，以定義裝置到雲端事件擷取的[分割區](../event-hubs/event-hubs-features.md#partitions)數目。 |
| **保留時間**  | 此屬性可指定 IoT 中樞保留訊息的天數。 預設值是一天，但它可以增加到七天。 |

IoT 中樞允許最多 7 天內建的事件中樞中的資料保留期。 您可以設定您的 IoT 中樞的建立期間的保留時間。 在 IoT 中樞的資料保留時間取決於您的 IoT 中樞層和單位類型。 大小、 內建的事件中樞可以保留的最大訊息大小，至少 24 小時的配額上限的訊息。 比方說，針對 IoT 中樞提供足夠的儲存體來保留至少 1 個 S1 單位為 4k 的 400 萬訊息大小每個。 如果您的裝置傳送較小的訊息，他們可能會保留時間 （最多 7 天） 取決於取用多少儲存體。 我們保證至少在指定的保留時間保留資料。

IoT 中樞也可讓您管理內建裝置對雲端接收端點上的取用者群組。

如果您使用[訊息路由](iot-hub-devguide-messages-d2c.md)並[後援路由](iot-hub-devguide-messages-d2c.md#fallback-route)已啟用，所有的訊息不符合任何路由上的查詢，請移至內建的端點。 如果您停用此後援路由，會卸除不符合任何查詢的訊息。

您可以使用 [IoT 中樞資源提供者 REST API](/rest/api/iothub/iothubresource) 採取程式設計方式，或透過 [Azure 入口網站](https://portal.azure.com)來修改保留期時間。

IoT 中樞會公開您後端服務的 **messages/events** 內建端點，以讀取您的中樞收到的裝置到雲端訊息。 此端點與事件中樞相容，可讓您使用事件中樞服務支援的任何機制讀取訊息。

## <a name="read-from-the-built-in-endpoint"></a>從內建端點讀取

某些產品整合和事件中樞 Sdk 已了解 IoT 中樞，並可讓您使用您的 IoT 中樞服務連接字串以連線至內建的端點。

當您使用事件中樞 Sdk 或產品整合的未能察覺 IoT 中樞時，您需要的事件中樞相容端點和事件中樞相容名稱。 您可以從入口網站擷取這些值，如下所示：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。

2. 按一下 [內建端點]  。

3. **事件**區段包含下列值：**資料分割**，**事件中樞相容名稱**，**事件中樞相容端點**，**保留時間**，以及**的取用者群組**.

    ![裝置到雲端設定](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

在入口網站中，事件中樞相容端點欄位會包含完整的事件中樞連接字串看起來像：**Endpoint=sb://abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=keykeykeykeykeykey=;EntityPath=iothub-ehub-abcd-1234-123456**. 如果您使用的 SDK 需要其他值，則它們會：

| 名稱 | 值 |
| ---- | ----- |
| 端點 | sb://abcd1234namespace.servicebus.windows.net/ |
| 主機名稱 | abcd1234namespace.servicebus.windows.net |
| 命名空間 | abcd1234namespace |

然後，您可以使用具有 **ServiceConnect** 權限的任何共用存取原則，連接至指定的事件中樞。

您可以使用來連接到 IoT 中樞會公開內建事件中樞相容端點，Sdk 包含：

| 語言 | SDK | 範例 | 注意 |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [快速入門](quickstart-send-telemetry-dotnet.md) | 使用事件中樞相容的資訊 |
 Java | https://github.com/Azure/azure-event-hubs-java | [快速入門](quickstart-send-telemetry-java.md) | 使用事件中樞相容的資訊 |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [快速入門](quickstart-send-telemetry-node.md) | 使用 IoT 中樞連接字串 |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | 使用 IoT 中樞連接字串 |

產品整合，您可以使用 IoT 中樞會公開內建事件中樞相容端點包括：

* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/)。 請參閱[處理來自 IoT 中樞與 Azure Functions 資料](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/)。
* [Azure 串流分析](https://docs.microsoft.com/azure/stream-analytics/)。 請參閱[Stream 資料做為輸入到 Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)。
* [Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/)。 請參閱[將 「 IoT 中樞事件來源新增至 Time Series Insights 環境](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md)。
* [Apache Storm Spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md)。 您可以檢視 GitHub 上的 [Spout 原始檔](https://github.com/apache/storm/tree/master/external/storm-eventhubs) 。
* [Apache Spark 整合](../hdinsight/spark/apache-spark-eventhub-streaming.md)。
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/)。

## <a name="next-steps"></a>後續步驟

* 如需 IoT 中樞端點的詳細資訊，請參閱 [IoT 中樞端點](iot-hub-devguide-endpoints.md)。

* [快速入門](quickstart-send-telemetry-node.md)示範如何從模擬裝置傳送裝置到雲端的訊息，以及從內建端點讀取訊息。 

如需詳細資料，請參閱[使用路由處理 IoT 中樞的裝置到雲端訊息](tutorial-routing.md)教學課程。

* 如果您想要將裝置到雲端的訊息路由至自訂端點，請參閱[針對裝置到雲端訊息使用訊息路由與自訂端點](iot-hub-devguide-messages-read-custom.md)。
