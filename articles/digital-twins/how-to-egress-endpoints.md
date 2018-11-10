---
title: Azure Digital Twins 中的輸出和端點 | Microsoft Docs
description: 有關如何使用 Azure Digital Twins 建立端點的指導方針
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: c09ee84cda5f0a9747d3ee1f8f1b37d1323f2cc2
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212245"
---
# <a name="egress-and-endpoints"></a>輸出和端點

Azure Digital Twins 支援「端點」的概念，而在使用者的 Azure 訂用帳戶中，每個端點都代表訊息/事件的訊息代理程式。 事件和訊息可以傳送至**事件中樞**、**事件方格**和**服務匯流排主題**。

事件會根據預先定義的路由喜好設定來傳送至端點：使用者可以指定應接收下列任一事件的端點：**TopologyOperation**、**UdfCustom**、**SensorChange**、**SpaceChange** 或 **DeviceMessage**。

若要對事件路由和事件類型有基本了解，請參閱[路由事件和訊息](concepts-events-routing.md)。

## <a name="event-types-description"></a>事件類型描述

以下是每個事件類型的事件格式：

- **TopologyOperation**

  適用於圖表變更。 subject 屬性會指定受影響的物件類型。 可觸發此事件的物件類型為：**Device**、**DeviceBlobMetadata**、**DeviceExtendedProperty**、**ExtendedPropertyKey**、**ExtendedType**、**KeyStore**、**Report**、**RoleDefinition**、**Sensor**、**SensorBlobMetadata**、**SensorExtendedProperty**、**Space**、**SpaceBlobMetadata**、**SpaceExtendedProperty**、**SpaceResource**、**SpaceRoleAssignment**、**System**、**User**、**UserBlobMetadata**、**UserExtendedProperty**。

  範例：

  ```JSON
  {
    "id": "00000000-0000-0000-0000-000000000000",
    "subject": "ExtendedPropertyKey",
    "data": {
      "SpacesToNotify": [
        "3a16d146-ca39-49ee-b803-17a18a12ba36"
      ],
      "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
      "AccessType": "Create"
    },
    "eventType": "TopologyOperation",
    "eventTime": "2018-04-17T17:41:54.9400177Z",
    "dataVersion": "1",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | 自訂屬性名稱 | 取代為 |
    | --- | --- |
    | yourTopicName | 自訂主題的名稱 |

- **UdfCustom**

  由使用者定義函式 (UDF) 傳送的事件。 
  
  > [!IMPORTANT]
  > 此事件必須以明確地從 UDF 本身傳送。

  範例：

  ```JSON
  {
    "id": "568fd394-380b-46fa-925a-ebb96f658cce",
    "subject": "UdfCustom",
    "data": {
      "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
      "ResourceType": "Space",
      "Payload": "\"Room is not available or air quality is poor\"",
      "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
    },
    "eventType": "UdfCustom",
    "eventTime": "2018-10-02T06:50:15.198Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | 自訂屬性名稱 | 取代為 |
    | --- | --- |
    | yourTopicName | 自訂主題的名稱 |

- **SensorChange**

  以遙測變更為基礎的感應器狀態更新。

  範例：

  ```JSON
  {
    "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "subject": "SensorChange",
    "data": {
      "Type": "Classic",
      "DataType": "Motion",
      "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
      "Value": "False",
      "PreviousValue": "True",
      "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
      "MessageType": "sensor",
      "Properties": {
        "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
        "ms-activity-id": "ct22YwXEGJ5u.605.0"
      }
    },
    "eventType": "SensorChange",
    "eventTime": "2018-04-17T17:46:18.5452993Z",
    "dataVersion": "1",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | 自訂屬性名稱 | 取代為 |
    | --- | --- |
    | yourTopicName | 自訂主題的名稱 |

- **SpaceChange**

  以遙測變更為基礎的空間狀態更新。

  範例：

  ```JSON
  {
    "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
    "subject": "SpaceChange",
    "data": {
      "Type": null,
      "DataType": "AvailableAndFresh",
      "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
      "Value": "Room is not available or air quality is poor",
      "PreviousValue": null,
      "RawData": null,
      "transactionId": null,
      "EventTimestamp": null,
      "MessageType": null,
      "Properties": null,
      "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
    },
    "eventType": "SpaceChange",
    "eventTime": "2018-10-02T06:50:20.128Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | 自訂屬性名稱 | 取代為 |
    | --- | --- |
    | yourTopicName | 自訂主題的名稱 |

- **DeviceMessage**

  可讓您指定 **EventHub** 連線，原始遙測事件也可從 Azure Digital Twins 路由至此。

> [!NOTE]
> - **DeviceMessage** 只能與 **EventHub** 結合；您無法讓 **DeviceMessage** 與任何其他事件類型結合。
> - 您只能指定一個結合 **EventHub** 或 **DeviceMessage** 類型的端點。

## <a name="configuring-endpoints"></a>設定端點

端點管理可透過端點 API 來執行。 以下是如何設定不同支援端點的一些範例。 請特別注意事件類型陣列，因為此陣列會定義端點的路由：

```plaintext
POST https://endpoints-demo.azuresmartspaces.net/management/api/v1.0/endpoints
```

- 路由傳送至**服務匯流排**事件類型：**SensorChange**、**SpaceChange**、**TopologyOperation**

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourTopicName"
  }
  ```

    | 自訂屬性名稱 | 取代為 |
    | --- | --- |
    | yourNamespace | 端點的命名空間 |
    | yourPrimaryKey | 用於驗證的主要連接字串 |
    | yourSecondaryKey | 用於驗證的次要連接字串 |
    | yourTopicName | 自訂主題的名稱 |

- 路由傳送至**事件方格**事件類型：**SensorChange**、**SpaceChange**、**TopologyOperation**

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "yourPrimaryKey",
    "secondaryConnectionString": "yourSecondaryKey",
    "path": "yourTopicName.westus-1.eventgrid.azure.net"
  }
  ```

    | 自訂屬性名稱 | 取代為 |
    | --- | --- |
    | yourPrimaryKey | 用於驗證的主要連接字串|
    | yourSecondaryKey | 用於驗證的次要連接字串 |
    | yourTopicName | 自訂主題的名稱 |

- 路由傳送至**事件中樞**事件類型：**SensorChange**、**SpaceChange**、**TopologyOperation**

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourEventHubName"
  }
  ```

    | 自訂屬性名稱 | 取代為 |
    | --- | --- |
    | yourNamespace | 端點的命名空間 |
    | yourPrimaryKey | 用於驗證的主要連接字串 |
    | yourSecondaryKey | 用於驗證的次要連接字串 |
    | yourEventHubName | **事件中樞**的名稱 |

- 路由傳送至**事件中樞**事件類型：**DeviceMessage**。 包含在 **connectionString** 中包含 `EntityPath`。

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey;EntityPath=yourEventHubName",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey;EntityPath=yourEventHubName",
    "path": "yourEventHubName"
  }
  ```

    | 自訂屬性名稱 | 取代為 |
    | --- | --- |
    | yourNamespace | 端點的命名空間 |
    | yourPrimaryKey | 用於驗證的主要連接字串 |
    | yourSecondaryKey | 用於驗證的次要連接字串 |
    | yourEventHubName | **事件中樞**的名稱 |

> [!NOTE]
> 建立新端點時，可能需要 5 到 10 分鐘的時間，才能開始在端點上接收事件。

## <a name="primary-and-secondary-connection-keys"></a>主要和次要連線金鑰

如果主要連線金鑰變成未授權，則系統會自動嘗試次要連線金鑰。 這可提供備份，並提高透過端點 API 依正常程序進行驗證及更新主要金鑰的機會。

如果主要和次要連線金鑰都變成未授權，則系統就會進入指數退避機制，並等候最多 30 分鐘的時間。 在每個觸發的退避等候時間上，事件會遭到捨棄。

只要系統處於退避等候狀態，透過端點 API 進行的連線金鑰更新可能要 30 分鐘後才會生效。

## <a name="unreachable-endpoints"></a>無法連線的端點

當端點變成無法連線時，系統會進入指數退避機制，並等候最多 30 分鐘的時間。 在每個觸發的退避等候時間上，事件會遭到捨棄。

## <a name="next-steps"></a>後續步驟

若要了解如何使用 Azure Digital Twins Swagger，請參閱 [Azure Digital Twins Swagger](how-to-use-swagger.md)。

若要深入了解 Azure Digital Twins 中的路由事件與訊息，請參閱[路由事件和訊息](concepts-events-routing.md)。
