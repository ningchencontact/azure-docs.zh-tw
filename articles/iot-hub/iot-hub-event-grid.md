---
title: Azure IoT 中樞和事件格線 | Microsoft Docs
description: 使用「Azure 事件格線」以根據「IoT 中樞」中發生的動作來觸發程序。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: eb521ed0951999fadbfae5e0eac1f0ea275e0d48
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391699"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>使用事件方格來觸發動作以回應 IoT 中樞事件

「Azure IoT 中樞」與「Azure 事件格線」整合，讓您能夠將事件通知傳送給其他服務並觸發下游程序。 請設定您的商務應用程式來接聽「IoT 中樞」事件，以便以可靠、彈性且安全的方式回應重大事件。 比方說，建置應用程式，更新資料庫、 建立工作票證，及傳遞電子郵件通知每次新的 IoT 裝置註冊至 IoT 中樞。 

[Azure 事件方格](../event-grid/overview.md)是一個完全受控的事件路由服務，其使用發佈-訂閱模型。 「事件格線」針對 Azure 服務 (例如 [Azure Functions](../azure-functions/functions-overview.md) 和 [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)) 內建支援，並可使用 Webhook 將事件警示傳遞給非 Azure 服務。 如需事件方格所支援的事件處理常式完整清單，請參閱 [Azure 事件方格簡介](../event-grid/overview.md)。 

![Azure 事件格線架構](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>區域可用性

位於支援「事件格線」之區域中的 IoT 中樞都可使用「事件格線」整合。 所有的裝置事件，但裝置遙測事件是正式推出。 裝置遙測事件處於公開預覽狀態，並會在美國東部、 美國西部、 西歐、 以外的所有區域內上市[Azure Government](/azure-government/documentation-government-welcome.md)， [Azure 中國 21Vianet](/azure/china/china-welcome.md)，和[Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/). 如需最新的區域清單，請參閱 [Azure 事件方格簡介](../event-grid/overview.md)。 

## <a name="event-types"></a>事件類型

「IoT 中樞」會發佈下列事件類型： 

| 事件類型 | 描述 |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | 向 IoT 中樞註冊裝置時發佈。 |
| Microsoft.Devices.DeviceDeleted | 從 IoT 中樞刪除裝置時發佈。 |
| Microsoft.Devices.DeviceConnected | 在裝置連線至 IoT 中樞時發佈。 |
| Microsoft.Devices.DeviceDisconnected | 在裝置從 IoT 中樞中斷連線時發佈。 |
| Microsoft.Devices.DeviceTelemetry | 裝置遙測訊息傳送至 IoT 中樞時，發行 |

請使用 Azure 入口網站或 Azure CLI 來設定要從每個 IoT 中樞發佈哪些事件。 例如，請試試[使用 Logic Apps 來傳送 Azure IoT 中樞事件的相關電子郵件通知](../event-grid/publish-iot-hub-events-to-logic-apps.md)教學課程。

## <a name="event-schema"></a>結構描述

「IoT 中樞」事件包含了回應裝置生命週期變更時所需的所有資訊。 您可以透過檢查 eventType 屬性的開頭是否為 **Microsoft.Devices** 來識別「IoT 中樞」事件。 如需有關如何使用「事件格線」事件屬性的詳細資訊，請參閱[事件格線事件結構描述](../event-grid/event-schema.md)。

### <a name="device-connected-schema"></a>裝置連線結構描述

下列範例說明裝置連線事件的結構描述： 

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
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

### <a name="device-telemetry-schema"></a>裝置遙測結構描述

裝置遙測訊息必須是有效的 JSON 格式設為 JSON 的 contentType 與訊息中設定為 utf-8 的 contentEncoding[系統屬性](iot-hub-devguide-routing-query-syntax.md#system-properties)。 如果未設定這個項目，是 IoT 中樞會在 base 64 編碼格式寫入訊息。 下列範例顯示裝置的遙測事件的結構描述： 

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

### <a name="device-created-schema"></a>已建立裝置結構描述

以下範例說明已建立裝置事件的結構描述： 

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
      "deviceEtag":"null",
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

如需每個屬性的詳細說明，請參閱[IoT 中樞的 Azure Event Grid 事件結構描述](../event-grid/event-schema-iot-hub.md)。

## <a name="filter-events"></a>篩選事件

IoT 中樞 」 事件訂閱可以篩選根據事件類型、 資料內容和主題，也就是裝置名稱的事件。

Event Grid 可讓[篩選](../event-grid/event-filtering.md)事件類型、 主題和資料內容。 在建立時的 Event Grid 訂用帳戶，您可以選擇選取 IoT 事件訂閱。 事件方格中的主旨篩選會根據**開始於** (前置詞) 和**結尾是** (尾碼) 的相符項而運作。 篩選會使用 `AND` 運算子，讓主旨同時符合前置詞和尾碼的事件會傳遞給訂閱者。 

「IoT 事件」的主旨使用以下格式：

```json
devices/{deviceId}
```

事件格線還可讓每個事件，包括資料內容的屬性篩選。 這可讓您選擇何種事件傳遞的遙測訊息的基礎的內容。 請參閱[進階篩選](../event-grid/event-filtering.md#advanced-filtering)若要檢視範例。 

DeviceConnected、 DeviceDisconnected、 DeviceCreated 和 DeviceDeleted 等的非遙測事件，Event Grid 篩選可用來建立訂閱時。 遙測事件，除了 Event Grid 中篩選使用者也可以篩選在裝置對應項、 訊息屬性和透過訊息路由查詢的主體。 我們會建立預設值[路由](iot-hub-devguide-messages-d2c.md)在 IoT 中樞中，根據 Event Grid 訂用帳戶，裝置將遙測資料。 此單一路由可處理所有的 Event Grid 訂用帳戶。 若要篩選訊息，傳送遙測資料之前，您可以更新您[路由查詢](iot-hub-devguide-routing-query-syntax.md)。 請注意該路由的查詢可以套用至訊息主體，只有當主體為 JSON。


## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>裝置連線和裝置中斷連線事件的限制

若要接收裝置連線和裝置中斷連線事件，您必須為裝置開啟 D2C 連結或 C2D 連結。 如果您的裝置使用 MQTT 通訊協定，則 IoT 中樞會讓 C2D 連結保持開啟。 AMQP，您可以開啟 C2D 連結藉由呼叫[接收非同步 API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)。 

如果您要傳送遙測資料，應開啟 D2C 連結。 如果裝置連線會閃爍，裝置連線，並中斷連線經常，這表示我們將不會傳送每個單一連接狀態 中，但仍會將發佈為快照每隔一分鐘的連接狀態。 IoT 中樞運作中斷時，我們將在中斷狀況結束後隨即發佈裝置連線狀態。 如果裝置在這段停止運作期間中斷連線，裝置中斷連線事件將在 10 分鐘內發佈。

## <a name="tips-for-consuming-events"></a>取用事件的秘訣

處理「IoT 中樞」事件的應用程式應該依循下列建議做法：

* 事件路由傳送至同一個事件處理常式，因此不要假設事件是來自特定來源，可以設定多個訂用帳戶。 請一律檢查訊息主題，以確保訊息來自您預期的 IoT 中樞。 

* 請勿假設您收到的所有事件都是您預期的類型。 處理訊息之前，請一律先檢查 eventType。

* 訊息的抵達可能會不按順序或有延遲。 使用 etag 欄位來了解是否是最新的裝置建立或刪除的裝置事件物件的相關資訊。

## <a name="next-steps"></a>後續步驟

* [試試 IoT 中樞事件教學課程](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* [了解如何排序裝置連線和中斷連線事件](iot-hub-how-to-order-connection-state-events.md)
* [深入了解 Event Grid](../event-grid/overview.md)
* [比較路由傳送 IoT 中樞事件與訊息之間的差異](iot-hub-event-grid-routing-comparison.md)
