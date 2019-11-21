---
title: 了解 Azure IoT 中樞配額和節流 | Microsoft Docs
description: 開發人員指南 - 說明適用於 IoT 中樞的配額和預期的節流行為。
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: daeb09acd11d727b11ad8a7b98d97ff90fddc6d8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228254"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>參考 - IoT 中樞配額和節流

This article explains the quotas for an IoT Hub, and provides information to help you understand how throttling works.

## <a name="quotas-and-throttling"></a>配額及節流

每個 Azure 訂用帳戶最多可以有 50 個 IoT 中樞，以及最多 1 個可用中樞。

佈建每個 IoT 中樞時，都會為其佈建特定層級中的特定單位數。 層級和單位數會決定您每天可傳送的訊息配額上限。 對於免費層中樞，用來計算每日配額的訊息大小為 0.5 KB，而對於所有其他層的訊息大小則為 4 KB。 如需詳細資訊，請參閱 [Azure IoT 中樞價格](https://azure.microsoft.com/pricing/details/iot-hub/)。

層級也會決定「IoT 中樞」在所有作業上強制執行的節流限制。

### <a name="iot-plug-and-play"></a>IoT Plug and Play

During public preview, IoT Plug and Play devices will send separate messages per interface, which may increase the number of messages counted towards your message quota.

## <a name="operation-throttles"></a>作業節流

Operation throttles are rate limitations that are applied in minute ranges and are intended to prevent abuse. They're also subject to [traffic shaping](#traffic-shaping).

下表顯示強制執行的節流。 個別中心的值如下。

| 節流 | 免費、B1 及 S1 | B2 和 S2 | B3 和 S3 | 
| -------- | ------- | ------- | ------- |
| [Identity registry operations](#identity-registry-operations-throttle) (create, retrieve, list, update, delete) | 1.67/秒/單位 (100/分鐘/單位) | 1.67/秒/單位 (100/分鐘/單位) | 83.33/sec/unit (5,000/min/unit) |
| [New device connections](#device-connections-throttle) (this limit applies to the rate of _new connections_, not the total number of connections) | 100/秒或 12/秒/單位較高者 <br/> 例如，兩個 S1 單位是 2\*12 = 24 個新連線/秒，但是您在所有單位上至少擁有 100 個新連線/秒。 如果有九個 S1 單位，您的全部單位就會擁有 108 個新連線/秒 (9\*12)。 | 120 個新連線/秒/單位 | 6,000 new connections/sec/unit |
| 裝置到雲端傳送 | Higher of 100 send operations/sec or 12 send operations/sec/unit <br/> For example, two S1 units are 2\*12 = 24/sec, but you have at least 100 send operations/sec across your units. With nine S1 units, you have 108 send operations/sec (9\*12) across your units. | 120 send operations/sec/unit | 6,000 send operations/sec/unit |
| 雲端到裝置的傳送<sup>1</sup> | 1.67 send operations/sec/unit (100 messages/min/unit) | 1.67 send operations/sec/unit (100 send operations/min/unit) | 83.33 send operations/sec/unit (5,000 send operations/min/unit) |
| 雲端到裝置的接收<sup>1</sup> <br/> (僅限裝置使用 HTTPS 時)| 16.67 receive operations/sec/unit (1,000 receive operations/min/unit) | 16.67 receive operations/sec/unit (1,000 receive operations/min/unit) | 833.33 receive operations/sec/unit (50,000 receive operations/min/unit) |
| 檔案上傳 | 1.67 file upload initiations/sec/unit (100/min/unit) | 1.67 file upload initiations/sec/unit (100/min/unit) | 83.33 file upload initiations/sec/unit (5,000/min/unit) |
| 直接方法<sup>1</sup> | 160 KB/秒/單位<sup>2</sup> | 480 KB/秒/單位<sup>2</sup> | 24 MB/秒/單位<sup>2</sup> | 
| 查詢 | 20/min/unit | 20/min/unit | 1,000/min/unit |
| 對應項 (裝置與模組) 讀取<sup>1</sup> | 100/sec | Higher of 100/sec or 10/sec/unit | 500/sec/unit |
| 對應項更新 (裝置與模組)<sup>1</sup> | 50/sec | Higher of 50/sec or 5/sec/unit | 250/sec/unit |
| 作業操作<sup>1</sup> <br/> (建立、更新、列出、刪除) | 1.67/秒/單位 (100/分鐘/單位) | 1.67/秒/單位 (100/分鐘/單位) | 83.33/sec/unit (5,000/min/unit) |
| 作業裝置操作<sup>1</sup> <br/> (更新對應項，叫用直接方法) | 10/秒 | 10/秒或 1/秒/單位較高者 | 50/秒/單位 |
| 設定與 Edge 部署<sup>1</sup> <br/> (建立、更新、列出、刪除) | 0.33/秒/單位 (20/分鐘/單位) | 0.33/秒/單位 (20/分鐘/單位) | 0.33/秒/單位 (20/分鐘/單位) |
| Device stream initiation rate<sup>1</sup> | 5 個新串流/秒 | 5 個新串流/秒 | 5 個新串流/秒 |
| Maximum number of concurrently connected device streams<sup>1</sup> | 50 | 50 | 50 |
| Maximum device stream data transfer<sup>1</sup> (aggregate volume per day) | 300 MB | 300 MB | 300 MB |

<sup>1</sup>「IoT 中樞」的基本層中不提供此功能。 如需詳細資訊，請參閱[如何選擇適合的 IoT 中樞](iot-hub-scaling.md)。 <br/><sup>2</sup>Throttling meter size is 4 KB.

### <a name="throttling-details"></a>Throttling Details

* The meter size determines at what increments your throttling limit is consumed. If your direct call's payload is between 0 and 4 KB, it is counted as 4 KB. You can make up to 40 calls per second per unit before hitting the limit of 160 KB/sec/unit.

   Similarly, if your payload is between 4 KB and 8 KB, each call accounts for 8 KB and you can make up to 20 calls per second per unit before hitting the max limit.

   Finally, if your payload size is between 156KB and 160 KB, you'll be able to make only 1 call per second per unit in your hub before hitting the limit of 160 KB/sec/unit.

*  For *Jobs device operations (update twin, invoke direct method)* for tier S2, 50/sec/unit only applies to when you invoke methods using jobs. If you invoke direct methods directly, the original throttling limit of 24 MB/sec/unit (for S2) applies.

*  **Quota** is the aggregate number of messages you can send in your hub *per day*. You can find your hub's quota limit under the column **Total number of messages /day** on the [IoT Hub pricing page](https://azure.microsoft.com/pricing/details/iot-hub/).

*  Your cloud-to-device and device-to-cloud throttles determine the maximum *rate* at which you can send messages -- number of messages irrespective of 4 KB chunks. Each message can be up to 256 KB which is the [maximum message size](iot-hub-devguide-quotas-throttling.md#other-limits).

*  It's a good practice to throttle your calls so that you don't hit/exceed the throttling limits. If you do hit the limit, IoT Hub responds with error code 429 and the client should back-off and retry. These limits are per hub (or in some cases per hub/unit). For more information, refer to [Manage connectivity and reliable messaging/Retry patterns](iot-hub-reliability-features-in-sdks.md#retry-patterns).

### <a name="traffic-shaping"></a>Traffic shaping

To accommodate burst traffic, IoT Hub accepts requests above the throttle for a limited time. The first few of these requests are processed immediately. However, if the number of requests continues violate the throttle, IoT Hub starts placing the requests in a queue and processed at the limit rate. This effect is called *traffic shaping*. Furthermore, the size of this queue is limited. If the throttle violation continues, eventually the queue fills up, and IoT Hub starts rejecting requests with `429 ThrottlingException`.

For example, you use a simulated device to send 200 device-to-cloud messages per second to your S1 IoT Hub (which has a limit of 100/sec D2C sends). For the first minute or two, the messages are processed immediately. However, since the device continues to send more messages than the throttle limit, IoT Hub begins to only process 100 messages per second and puts the rest in a queue. You start noticing increased latency. Eventually, you start getting `429 ThrottlingException` as the queue fills up, and the "number of throttle errors" in [IoT Hub's metrics](iot-hub-metrics.md) starts increasing.

### <a name="identity-registry-operations-throttle"></a>Identity registry operations throttle

Device identity registry operations are intended for run-time use in device management and provisioning scenarios. 透過[匯入和匯出作業](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)，即可支援讀取或更新大量的裝置身分識別。

### <a name="device-connections-throttle"></a>Device connections throttle

「裝置連線」節流會控制新裝置可與 IoT 中樞建立連線的速率。 「裝置連線」節流不會控制同時連線裝置的數目上限。 「裝置連線」速率節流受制於為 IoT 中樞佈建的單位數。

例如，若您購買單一 S1 單位，則得到每秒 100 個連線的節流。 Therefore, to connect 100,000 devices, it takes at least 1,000 seconds (approximately 16 minutes). 不過，若您已將裝置登錄在您的身分識別登錄中，則可以有任意數量的同時連線裝置。

## <a name="other-limits"></a>其他限制

IoT 中樞會強制執行其他操作限制：

| 作業 | 限制 |
| --------- | ----- |
| 裝置 | The total number of devices plus modules that can be registered to a single IoT hub is capped at 1,000,000. The only way to increase this limit is to contact [Microsoft Support](https://azure.microsoft.com/support/options/).|
| 檔案上傳 | 10 concurrent file uploads per device. |
| 作業<sup>1</sup> | Maximum concurrent jobs is 1 (for Free and S1), 5 (for S2), and 10 (for S3). However, the max concurrent [device import/export jobs](iot-hub-bulk-identity-mgmt.md) is 1 for all tiers. <br/>Job history is retained up to 30 days. |
| 額外端點 | 付費 SKU 中樞包含 10 個額外端點。 免費 SKU 中樞包含 1 個額外端點。 |
| Message routing queries | Paid SKU hubs may have 100 routing queries. Free SKU hubs may have five routing queries. |
| 訊息擴充 | Paid SKU hubs can have up to 10 message enrichments. Free SKU hubs can have up to 2 message enrichments.|
| 裝置到雲端傳訊 | 訊息大小上限為 256 KB |
| 雲端到裝置的傳訊<sup>1</sup> | 訊息大小上限為 64 KB。 Maximum pending messages for delivery is 50 per device. |
| 直接方法<sup>1</sup> | 直接方法承載的大小上限為 128 KB。 |
| 自動裝置組態 <sup>1</sup> | 每個付費 SKU 中樞 100 項設定。 每個免費 SKU 中樞 20 項設定。 |
| IoT Edge automatic deployments<sup>1</sup> | 每個部署 20 個模組。 每個付費中樞 100 個部署。 10 deployments per free SKU hub. |
| 對應項<sup>1</sup> | Maximum size of desired properties and reported properties sections are 32 KB each. Maximum size of tags section is 8 KB. |

<sup>1</sup>「IoT 中樞」的基本層中不提供此功能。 如需詳細資訊，請參閱[如何選擇適合的 IoT 中樞](iot-hub-scaling.md)。

## <a name="increasing-the-quota-or-throttle-limit"></a>Increasing the quota or throttle limit

At any given time, you can increase quotas or throttle limits by [increasing the number of provisioned units in an IoT hub](iot-hub-upgrade.md).

## <a name="latency"></a>延遲

IoT 中樞會努力地為所有作業提供低延遲的服務。 However, due to network conditions and other unpredictable factors it cannot guarantee a certain latency. 設計您的解決方案時，您應該：

* 避免進行有關任何 IoT 中樞作業最大延遲的任何假設。
* 在與您的裝置最靠近的 Azure 區域中佈建 IoT 中樞。
* 考慮使用 Azure IoT Edge 在裝置或靠近裝置的閘道上執行無法容忍延遲的作業。

如先前所述，多個 IoT 中樞單位既會影響節流功能，又不會提供額外的延遲好處或保證。

如果您注意到作業的延遲時間莫名其妙增加，請連絡 [Microsoft 支援服務](https://azure.microsoft.com/support/options/)。

## <a name="next-steps"></a>後續步驟

如需有關 IoT 中樞節流行為的深入討論，請參閱 [IoT Hub throttling and you (IoT 中樞節流和您)](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/) 部落格文章。

此 IoT 中樞開發人員指南中的其他參考主題包括︰

* [IoT 中樞端點](iot-hub-devguide-endpoints.md)
