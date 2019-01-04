---
title: 將資料匯出至 Azure 事件中樞和 Azure 服務匯流排 | Microsoft Docs
description: 如何將資料從 Azure IoT Central 應用程式匯出至 Azure 事件中樞和 Azure 服務匯流排
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 14b51f109ca76661ac10c99d42002dda45bc0500
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318381"
---
# <a name="export-your-data-in-azure-iot-central"></a>匯出 Azure IoT Central 中的資料

*此主題適用於系統管理員。*

本文深入探討如何使用 Azure IoT Central 中的連續資料匯出功能，將資料匯出到您自己的 **Azure 事件中樞**和 **Azure 服務匯流排**執行個體。 您可以將**度量**、**裝置**及**裝置範本**匯出到您自己的目的地，以取得暖路徑見解和分析。 這包括觸發「Azure 串流分析」中的自訂規則、觸發 Azure Logic Apps 中的自訂工作流程，或透過 Azure Functions 來轉換及傳遞資料。 

> [!Note]
> 同樣地，當您開啟連續資料匯出時，只會取得從該時刻起的資料。 目前，無法擷取「連續資料匯出」時的資料。 若要保留更多歷史資料，請儘早開啟「連續資料匯出」。


## <a name="prerequisites"></a>必要條件

- 您必須是 IoT Central 應用程式中的系統管理員

## <a name="export-to-azure-event-hubs-and-azure-service-bus"></a>匯出至 Azure 事件中樞和 Azure 服務匯流排

度量、裝置及裝置範本資料會以近乎即時的方式，匯出至事件中樞或「服務匯流排」佇列或主題。 匯出的度量資料包含您裝置傳送給 IoT Central 的訊息整體，而不僅限於度量本身的值。 匯出的裝置資料包含對所有裝置之屬性與設定進行的變更，而匯出的裝置範本則包含對所有裝置範本進行的變更。 匯出的資料會在 "body" 屬性中，且採用 JSON 格式。

> [!NOTE]
> 選擇「服務匯流排」作為匯出目的地時，佇列和主題**不得啟用 [工作階段] 或 [重複資料偵測]**。 如果啟用這其中一個選項，有些訊息就不會送達到您的佇列或主題中。

### <a name="measurements"></a>量測

在 IoT Central 收到來自裝置的訊息之後，就會快速匯出新訊息。 「事件中樞」和「服務匯流排」中的每個匯出訊息都包含裝置以 JSON 格式在 "body" 屬性中傳送的完整訊息。 

> [!NOTE]
> 傳送量測的裝置會以裝置識別碼來表示 (請參閱以下各節)。 若要取得裝置的名稱，請匯出裝置資料，並使用與裝置訊息之 **deviceId** 相符的 **connectionDeviceId** 將每個訊息相互關聯。

下列範例示範事件中樞或「服務匯流排」佇列或主題中所收到度量資料的相關訊息。

```json
{
  "body": {
    "humidity": 29.06963648666288,
    "temp": 8.4503795661685,
    "pressure": 1075.8334910110093,
    "magnetometerX": 408.6966458887116,
    "magnetometerY": -532.8809796603962,
    "magnetometerZ": 174.70653875528205,
    "accelerometerX": 1481.546749013788,
    "accelerometerY": -1139.4316656437406,
    "accelerometerZ": 811.6928695575307,
    "gyroscopeX": 442.19879163299856,
    "gyroscopeY": 123.23710975717177,
    "gyroscopeZ": 708.5397575786151,
    "deviceState": "DANGER"
  },
  "annotations": {
    "iothub-connection-device-id": "<connectionDeviceId>",
    "iothub-connection-auth-method": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "<generationId>",
    "iothub-enqueuedtime": 1539381029965,
    "iothub-message-source": "Telemetry",
    "x-opt-sequence-number": 25325,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539381030200
  },
  "sequenceNumber": 25325,
  "enqueuedTimeUtc": "2018-10-12T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>裝置

包含裝置資料的訊息會以每幾分鐘一次的頻率，傳送到您的事件中樞或「服務匯流排」佇列或主題。 這意謂著每隔幾分鐘就會有一批訊息送達，其中包含下列相關資料
- 已新增的新裝置
- 屬性和設定值已變更的裝置

每個訊息皆代表自上次匯出訊息之後，對裝置進行的一或多項變更。 將在每個訊息中傳送的資訊包括：
- IoT Central 中裝置的 `id`
- 裝置的 `name`
- 來自[裝置佈建服務](https://aka.ms/iotcentraldocsdps) 的 `deviceId`
- 裝置範本資訊
- 屬性值
- 設定值

> [!NOTE]
> 系統不會匯出自上個批次後刪除的裝置。 目前，在匯出的訊息中並沒有任何適用於已刪除之裝置的指標。
>
> 每個裝置所屬的裝置範本會以裝置範本識別碼來表示。 若要取得裝置範本的名稱，請務必一併匯出裝置範本資料。

下列範例示範事件中樞或「服務匯流排」佇列或主題中裝置資料的相關訊息：


```json
{
  "body": {
    "id": "<id>",
    "name": "<deviceName>",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
      "id": "<templateId>",
      "version": "1.0.0"
    },
    "properties": {
      "cloud": {
        "location": "Seattle"
      },
      "device": {
        "dieNumber": 5445.5862873026645
      }
    },
    "settings": {
      "device": {
        "fanSpeed": 0
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "devices",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 39740,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274959654
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 39740,
  "enqueuedTimeUtc": "2018-10-11T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>裝置範本

包含裝置範本資料的訊息會以每幾分鐘一次的頻率，傳送到您的事件中樞或「服務匯流排」佇列或主題。 這意謂著每隔幾分鐘就會有一批訊息送達，其中包含下列相關資料
- 已新增的新裝置範本
- 度量、屬性及設定定義已變更的裝置範本

每個訊息皆代表自上次匯出訊息之後，對裝置範本進行的一或多項變更。 將在每個訊息中傳送的資訊包括：
- 裝置範本的 `id`
- 裝置範本的 `name`
- 裝置範本的 `version`
- 量測資料類型和最小值/最大值
- 屬性資料類型和預設值
- 設定資料類型和預設值

> [!NOTE]
> 系統不會匯出自上個批次後刪除的裝置範本。 目前，在匯出的訊息中並沒有任何適用於已刪除之裝置範本的指標。

下列範例示範事件中樞或「服務匯流排」佇列或主題中裝置範本資料的相關訊息：

```json
{
  "body": {
    "id": "<id>",
    "version": "1.0.0",
    "name": "<templateName>",
    "measurements": {
      "telemetry": {
        "humidity": {
          "dataType": "double",
          "name": "humidity"
        },
        "pressure": {
          "dataType": "double",
          "name": "pressure"
        },
        "temp": {
          "dataType": "double",
          "name": "temperature"
        }
      }
    },
    "properties": {
      "cloud": {
        "location": {
          "dataType": "string",
          "name": "Location"
        }
      },
      "device": {
        "dieNumber": {
          "dataType": "double",
          "name": "Die Number"
        }
      }
    },
    "settings": {
      "device": {
        "fanSpeed": {
          "dataType": "double",
          "name": "Fan Speed",
          "initialValue": 0
        }
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "deviceTemplates",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 25315,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274985085
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 25315,
  "enqueuedTimeUtc": "2018-10-11T16:23:05.085Z",
  "offset": "<offset>",
}
```

## <a name="next-steps"></a>後續步驟

既然您已知道如何將資料匯出至「Azure 事件中樞」和「Azure 服務匯流排」，請繼續進行下一個步驟：

> [!div class="nextstepaction"]
> [如何觸發 Azure Functions](howto-trigger-azure-functions.md)
