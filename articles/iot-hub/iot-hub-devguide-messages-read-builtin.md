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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61363957"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>從內建端點讀取裝置對雲端訊息

根據預設，訊息會路由到與[事件中樞](https://azure.microsoft.com/documentation/services/event-hubs/)相容的內建服務對應端點 (**訊息/事件**)。 此端點目前只連接埠 5671 上使用 [AMQP](https://www.amqp.org/) 通訊協定公開。 IoT 中樞會公開下列屬性，讓您控制與事件中樞相容的內建訊息端點 **messages/events**。

| 屬性            | 描述 |
| ------------------- | ----------- |
| **分割計數** | 在建立時設定此屬性，以定義裝置到雲端事件擷取的[分割區](../event-hubs/event-hubs-features.md#partitions)數目。 |
| **保留時間**  | 此屬性可指定 IoT 中樞保留訊息的天數。 預設值是一天，但它可以增加到七天。 |

IoT 中心允许将数据保留在内置的事件中心，最长保留期为 7 天。 可以在创建 IoT 中心期间设置保留时间。 在 IoT 中樞的資料保留時間取決於您的 IoT 中樞層和單位類型。 就大小来说，内置事件中心能够保留的消息的最大大小为至少 24 小时的配额。 例如，使用 1 个 S1 单元时，在每条消息的大小为 4k 的情况下，IoT 中心提供的存储足以保留至少 40 万条消息。 如果设备发送的消息较小，则这些消息可能会保留更长的时间（最多 7 天），具体取决于使用的存储。 我们保证至少将数据保留指定的保留时间。

IoT 中樞也可讓您管理內建裝置對雲端接收端點上的取用者群組。

如果使用[消息路由](iot-hub-devguide-messages-d2c.md)，并启用了[回退路由](iot-hub-devguide-messages-d2c.md#fallback-route)，则与任何路由上的查询不匹配的所有消息都会进入内置终结点。 如果禁用此回退路由，将删除与任何查询都不匹配的消息。

您可以使用 [IoT 中樞資源提供者 REST API](/rest/api/iothub/iothubresource) 採取程式設計方式，或透過 [Azure 入口網站](https://portal.azure.com)來修改保留期時間。

IoT 中樞會公開您後端服務的 **messages/events** 內建端點，以讀取您的中樞收到的裝置到雲端訊息。 此端點與事件中樞相容，可讓您使用事件中樞服務支援的任何機制讀取訊息。

## <a name="read-from-the-built-in-endpoint"></a>從內建端點讀取

某些产品集成和事件中心 SDK 可以感知 IoT 中心，并让你使用 IoT 中心服务连接字符串连接到内置的终结点。

使用无法感知 IoT 中心的事件中心 SDK 或产品集成时，需要一个与事件中心兼容的终结点以及与事件中心兼容的名称。 可以从门户检索这些值，如下所示：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。

2. 按一下 [內建端點]。

3. “事件”部分包含以下值：“分区”、“与事件中心兼容的名称”、“与事件中心兼容的终结点”、“保留时间”、“使用者组”。

    ![裝置到雲端設定](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

在门户中，“与事件中心兼容的终结点”字段包含完整的事件中心连接字符串，如下所示：**Endpoint=sb://abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=keykeykeykeykeykey=;EntityPath=iothub-ehub-abcd-1234-123456**. 如果所用 SDK 需求其他值，则这些值将会是：

| 名稱 | Value |
| ---- | ----- |
| 端點 | sb://abcd1234namespace.servicebus.windows.net/ |
| 主機名稱 | abcd1234namespace.servicebus.windows.net |
| 命名空間 | abcd1234namespace |

然後，您可以使用具有 **ServiceConnect** 權限的任何共用存取原則，連接至指定的事件中樞。

可以用来连接到内置的、与事件中心兼容的且由 IoT 中心公开的终结点的 SDK 包括：

| 語言 | SDK | 範例 | 注意 |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [快速入門](quickstart-send-telemetry-dotnet.md) | 使用与事件中心兼容的信息 |
 Java | https://github.com/Azure/azure-event-hubs-java | [快速入門](quickstart-send-telemetry-java.md) | 使用与事件中心兼容的信息 |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [快速入門](quickstart-send-telemetry-node.md) | 使用 IoT 中心连接字符串 |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | 使用 IoT 中心连接字符串 |

可以与内置的、与事件中心兼容的且由 IoT 中心公开的终结点配合使用的产品集成包括：

* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/)。 請參閱[處理來自 IoT 中樞與 Azure Functions 資料](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/)。
* [Azure 串流分析](https://docs.microsoft.com/azure/stream-analytics/)。 请参阅[将数据作为流分析的输入进行流式传输](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)。
* [Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/)。 請參閱[將 「 IoT 中樞事件來源新增至 Time Series Insights 環境](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md)。
* [Apache Storm Spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md)。 可以在 GitHub 上查看 [Spout 源代码](https://github.com/apache/storm/tree/master/external/storm-eventhubs) 。
* [Apache Spark 整合](../hdinsight/spark/apache-spark-eventhub-streaming.md)。
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/)。

## <a name="next-steps"></a>後續步驟

* 如需 IoT 中樞端點的詳細資訊，請參閱 [IoT 中樞端點](iot-hub-devguide-endpoints.md)。

* [快速入門](quickstart-send-telemetry-node.md)示範如何從模擬裝置傳送裝置到雲端的訊息，以及從內建端點讀取訊息。 

如需詳細資料，請參閱[使用路由處理 IoT 中樞的裝置到雲端訊息](tutorial-routing.md)教學課程。

* 如果您想要將裝置到雲端的訊息路由至自訂端點，請參閱[針對裝置到雲端訊息使用訊息路由與自訂端點](iot-hub-devguide-messages-read-custom.md)。
