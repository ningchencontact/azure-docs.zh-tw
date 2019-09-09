---
title: IoT 中樞的 Azure Event Grid 結構描述 | Microsoft Docs
description: IoT 中樞之事件結構描述格式與屬性的參考頁面
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: kgremban
ms.openlocfilehash: 4e96276a862844cea1d0800eafb952d4a0df97ab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076361"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>IoT 中樞的 Azure Event Grid 事件結構描述

本文提供 Azure IoT 中樞事件的屬性和結構描述。 如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。 

如需範例指令碼和教學課程的清單，請參閱 [IoT 中樞事件來源](event-sources.md#iot-hub)。

## <a name="available-event-types"></a>可用的事件類型

Azure IoT 中樞會發出下列事件類型：

| 事件類型 | 描述 |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | 向 IoT 中樞註冊裝置時發佈。 |
| Microsoft.Devices.DeviceDeleted | 從 IoT 中樞刪除裝置時發佈。 | 
| Microsoft.Devices.DeviceConnected | 在裝置連線至 IoT 中樞時發佈。 |
| Microsoft.Devices.DeviceDisconnected | 在裝置從 IoT 中樞中斷連線時發佈。 | 
| Microsoft.Devices.DeviceTelemetry | 遙測訊息傳送至 IoT 中樞時，就會發行。 |

Event Grid 所支援的所有區域中，所有的裝置事件，但裝置遙測事件是正式推出。 裝置遙測事件處於公開預覽狀態，並會在美國東部、 美國西部、 西歐、 以外的所有區域內上市[Azure Government](../azure-government/documentation-government-welcome.md)， [Azure 中國 21Vianet](/azure/china/china-welcome)，和[Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="example-event"></a>事件範例

DeviceConnected 和 DeviceDisconnected 事件的結構描述具有相同的結構。 此事件範例顯示裝置連線至 IoT 中樞註冊時引發的事件結構描述：

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

遙測事件傳送到 IoT 中樞時，會引發 DeviceTelemetry 事件。 此事件的範例結構描述如下所示。

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

DeviceCreated 和 DeviceDeleted 事件的結構描述具有相同的結構。 此事件範例顯示向 IoT 中樞註冊裝置時引發之事件的結構描述：

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>事件屬性

所有事件都包含相同的最高層級資料： 

| 屬性 | type | 描述 |
| -------- | ---- | ----------- |
| id | string | 事件的唯一識別碼。 |
| topic | string | 事件來源的完整資源路徑。 此欄位不可寫入。 Event Grid 提供此值。 |
| subject | string | 發行者定義事件主體的路徑。 |
| eventType | string | 此事件來源已註冊的事件類型之一。 |
| eventTime | string | 事件產生的時間，以提供者之 UTC 時間為準。 |
| data | object | IoT 中樞事件資料。  |
| dataVersion | string | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | string | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |

對於所有 IoT 中樞事件，資料物件都會包含下列屬性：

| 屬性 | type | 描述 |
| -------- | ---- | ----------- |
| hubName | string | 已建立或刪除裝置的 IoT 中樞名稱。 |
| deviceId | string | 裝置的唯一識別碼。 此區分大小寫的字串最長為 128 個字元，並支援 ASCII 7 位元英數字元和下列特殊字元：`- : . + % _ # * ? ! ( ) , = @ ; $ '`。 |

每個事件發行者有不同的資料物件內容。 

對於**裝置連線**和**裝置中斷連線** IoT 中樞事件，資料物件會包含下列屬性：

| 屬性 | type | 描述 |
| -------- | ---- | ----------- |
| moduleId | string | 模組的唯一識別碼。 針對模組裝置才會輸出此欄位。 此區分大小寫的字串最長為 128 個字元，並支援 ASCII 7 位元英數字元和下列特殊字元：`- : . + % _ # * ? ! ( ) , = @ ; $ '`。 |
| deviceConnectionStateEventInfo | object | 裝置連線狀態事件資訊
| sequenceNumber | string | 一個號碼，有助於指出裝置連線或裝置中斷連線事件的順序。 最新的事件會有高於前一個事件的序號。 此號碼的變動有可能超過 1，但只會增加不會減少。 請參閱[如何使用序號](../iot-hub/iot-hub-how-to-order-connection-state-events.md)。 |

針對**裝置遙測**IoT 中樞事件資料物件包含中的裝置到雲端訊息[IoT 中樞訊息格式](../iot-hub/iot-hub-devguide-messages-construct.md)並具有下列屬性：

| 屬性 | type | 描述 |
| -------- | ---- | ----------- |
| body | string | 從裝置訊息的內容。 |
| properties | string | 應用程式屬性為可新增至訊息的使用者定義字串。 這些欄位為選擇性。 |
| system properties | string | [系統屬性](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties)有助於識別內容及訊息的來源。 裝置遙測訊息必須是有效的 JSON 格式，與 contentType 設定為 JSON 和 contentEncoding 訊息系統屬性中設定為 utf-8。 如果未設定這個項目，是 IoT 中樞會在 base 64 編碼格式寫入訊息。  |

對於**裝置建立**和**裝置刪除** IoT 中樞事件，資料物件會包含下列屬性：

| 屬性 | type | 描述 |
| -------- | ---- | ----------- |
| twin | object | 裝置對應項，也就是雲端中顯示的應用程式裝置中繼資料的相關資訊。 | 
| deviceID | string | 裝置對應項的唯一識別碼。 | 
| etag | string | 可確保裝置對應項的更新具有一致性的驗證程式。 對每個裝對應項而言，每個 etag 保證都是唯一的。 |  
| deviceEtag| string | 可確保裝置登錄的更新具有一致性的驗證程式。 對每個裝置登錄而言，每個 deviceEtag 保證都是唯一的。 |
| status | string | 已啟用或停用裝置對應項。 | 
| statusUpdateTime | string | 上次更新裝置對應項狀態的 ISO8601 時間戳記。 |
| connectionState | string | 裝置已連線或已中斷連線。 | 
| lastActivityTime | string | 上次活動的 ISO8601 時間戳記。 | 
| cloudToDeviceMessageCount | integer | 傳送到此裝置的雲端到裝置訊息計數。 | 
| authenticationType | string | 用於這個裝置的驗證類型：`SAS`、`SelfSigned` 或 `CertificateAuthority`。 |
| x509Thumbprint | string | 指紋是 x509 憑證的唯一值，通常用來尋找憑證存放區中的特定憑證。 指紋是使用 SHA1 演算法所動態產生的，並未實際存在於憑證中。 | 
| primaryThumbprint | string | x509 憑證的主要指紋。 |
| secondaryThumbprint | string | x509 憑證的次要指紋。 | 
| version | integer | 每次更新裝置對應項時，整數就會遞增 1。 |
| desired | object | 只能由應用程式後端寫入，並可由裝置讀取的屬性部分。 | 
| reported | object | 只能由裝置寫入，並可由應用程式後端讀取的屬性部分。 |
| lastUpdated | string | 上次更新裝置對應項屬性的 ISO8601 時間戳記。 | 

## <a name="next-steps"></a>後續步驟

* 如需 Azure Event Grid 的簡介，請參閱[什麼是 Event Grid？](overview.md)
* 若要深入了解 IoT 中樞與 Event Grid 一起運作的方式，請參閱[使用 Event Grid 觸發動作來回應 IoT 中樞事件](../iot-hub/iot-hub-event-grid.md)。