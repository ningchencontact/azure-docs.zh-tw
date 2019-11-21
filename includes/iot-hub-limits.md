---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 34de38e91d47457d215c7ebf65d04ed2dbae5324
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224535"
---
The following table lists the limits associated with the different service tiers S1, S2, S3, and F1. For information about the cost of each *unit* in each tier, see [Azure IoT Hub pricing](https://azure.microsoft.com/pricing/details/iot-hub/).

| 資源 | S1 標準 | S2 標準 | S3 標準 | F1 免費 |
| --- | --- | --- | --- | --- |
| 訊息/天 |400,000 |6,000,000 |300,000,000 |8,000 |
| 單位上限 |200 |200 |10 |1 |

> [!NOTE]
> If you anticipate using more than 200 units with an S1 or S2 tier hub or 10 units with an S3 tier hub, contact Microsoft Support.
> 
> 

The following table lists the limits that apply to IoT Hub resources.

| 資源 | 限制 |
| --- | --- |
| 每個 Azure 訂用帳戶的付費 IoT 中樞上限 |100 |
| 每個 Azure 訂用帳戶的免費 IoT 中樞上限 |1 |
| Maximum number of characters in a device ID | 128 |
| 單一呼叫中傳回的裝置身分識別<br/> 數目上限 |1,000 |
| 裝置到雲端訊息的 IoT 中樞訊息保留上限 |7 天 |
| 裝置到雲端訊息的大小上限 |256 KB |
| 裝置到雲端批次的大小上限 |AMQP 和 HTTP：整個批次 256 KB <br/>MQTT：每則訊息 256 KB |
| 裝置到雲端批次的訊息上限 |500 |
| 雲端到裝置訊息的大小上限 |64 KB |
| 雲端到裝置訊息的 TTL 上限 |2 天 |
| 雲端到裝置訊息的傳遞計數 <br/> 上限 |100 |
| Maximum cloud-to-device queue depth per device |50 |
| 回應雲端到裝置訊息之反應訊息的 <br/> 傳遞計數上限 |100 |
| 回應雲端到裝置訊息之反應訊息的 <br/> TTL 上限 |2 天 |
| [Maximum size of device twin](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB for tags section, and 32 KB for desired and reported properties sections each |
| Maximum length of device twin string key | 1 KB |
| Maximum length of device twin string value | 4 KB |
| [Maximum depth of object in device twin](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| 直接方法承載的大小上限 | 128 KB |
| 作業歷程記錄的保留期上限 | 30 天 |
| 並行作業數上限 | 10 (適用於 S3)、5 (適用於 S2)、1 (適用於 S1) |
| 額外端點上限 | 10 (for S1, S2, and S3) |
| 訊息路由規則數上限 | 100 (for S1, S2, and S3) |
| 同時連線之裝置串流的數目上限 | 50 (僅適用於 S1、S2、S3 和 F1) |
| 裝置串流資料傳輸上限 | 每日 300 MB (僅適用於 S1、S2、S3 和 F1) |

> [!NOTE]
> If you need more than 100 paid IoT hubs in an Azure subscription, contact Microsoft Support.

> [!NOTE]
> Currently, the total number of devices plus modules that can be registered to a single IoT hub is capped at 1,000,000. 如果您想要上調此限制，請連絡 [Microsoft 支援服務](https://azure.microsoft.com/support/options/)。

IoT Hub throttles requests when the following quotas are exceeded.

| 節流 | 每個中心的值 |
| --- | --- |
| 身分識別登錄作業 <br/> (create, retrieve, list, update, and delete), <br/> 個別或大量匯入/匯出 |83.33/sec/unit (5,000/min/unit) (for S3). <br/> 1.67/秒/單位 (100/分鐘/單位) (適用於 S1 和 S2)。 |
| 裝置連線 |6,000/sec/unit (for S3), 120/sec/unit (for S2), 12/sec/unit (for S1). <br/>最小值為 100/秒 |
| 裝置到雲端傳送 |6,000/sec/unit (for S3), 120/sec/unit (for S2), 12/sec/unit (for S1). <br/>最小值為 100/秒 |
| 雲端到裝置的傳送 | 83.33/sec/unit (5,000/min/unit) (for S3), 1.67/sec/unit (100/min/unit) (for S1 and S2). |
| 雲端到裝置的接收 |833.33/sec/unit (50,000/min/unit) (for S3), 16.67/sec/unit (1,000/min/unit) (for S1 and S2). |
| 檔案上傳作業 |83.33 file upload initiations/sec/unit (5,000/min/unit) (for S3), 1.67 file upload initiations/sec/unit (100/min/unit) (for S1 and S2). <br/> 10,000 SAS URIs can be out for an Azure Storage account at one time.<br/> 10 個 SAS URI/裝置可以一次用盡。 |
| 直接方法 | 24 MB/sec/unit (for S3), 480 KB/sec/unit (for S2), 160 KB/sec/unit (for S1).<br/> Based on 8-KB throttling meter size. |
| 裝置對應項讀取 | 500/sec/unit (for S3), Maximum of 100/sec or 10/sec/unit (for S2), 100/sec (for S1) |
| 裝置對應項更新 | 250/sec/unit (for S3), Maximum of 50/sec or 5/sec/unit (for S2), 50/sec (for S1) |
| 作業的操作 <br/> (create, update, list, and delete) | 83.33/sec/unit (5,000/min/unit) (for S3), 1.67/sec/unit (100/min/unit) (for S2), 1.67/sec/unit (100/min/unit) (for S1). |
| 作業的每一裝置操作輸送量 | 50/sec/unit (for S3), maximum of 10/sec or 1/sec/unit (for S2), 10/sec (for S1). |
| 裝置串流初始速率 | 5 new streams/sec (for S1, S2, S3, and F1 only). |
