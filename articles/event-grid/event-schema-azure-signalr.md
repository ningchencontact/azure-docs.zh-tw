---
title: Azure 事件格線 Azure SignalR 事件結構描述
description: 描述 Azure Event grid 的 Azure SignalR 事件的屬性
services: event-grid
author: chenyl
ms.service: event-grid
ms.topic: reference
ms.date: 06/11/2019
ms.author: chenyl
ms.openlocfilehash: 3b072ff2b680ad6d144c7441190ab2df9870f5d0
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789068"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Azure SignalR 服務的 Event Grid 事件結構描述

這篇文章提供 SignalR 服務事件的屬性和結構描述。 如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。


## <a name="available-event-types"></a>可用的事件類型

SignalR 服務會發出下列事件類型：

| 事件類型 | 描述 |
| ---------- | ----------- |
| Microsoft.SignalRService.ClientConnectionConnected | 當用戶端連線時，就會引發。 |
| Microsoft.SignalRService.ClientConnectionDisconnected | 當用戶端連接已中斷連線時，就會引發。 |

## <a name="example-event"></a>事件範例

下列範例會示範用戶端的結構描述已連線的連接事件： 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

用戶端連線已中斷連線事件結構描述如下： 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>事件屬性

事件具有下列的最高層級資料：

| 屬性 | type | 描述 |
| -------- | ---- | ----------- |
| topic | string | 事件來源的完整資源路徑。 此欄位不可寫入。 Event Grid 提供此值。 |
| subject | string | 發行者定義事件主體的路徑。 |
| eventType | string | 此事件來源已註冊的事件類型之一。 |
| eventTime | string | 事件產生的時間，以提供者之 UTC 時間為準。 |
| id | string | 事件的唯一識別碼。 |
| data | object | SignalR 服務的事件資料。 |
| dataVersion | string | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | string | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |

資料物件具有下列屬性：

| 屬性 | type | 描述 |
| -------- | ---- | ----------- |
| timestamp | string | 事件產生的時間，以提供者之 UTC 時間為準。 |
| hubName | string | 用戶端連線所屬的中樞。 |
| connectionId | string | 用戶端連接的唯一識別碼。 |
| userId | string | 在宣告中定義的使用者識別碼。 |
| errorMessage | string | 會導致連線中斷連線時發生錯誤。 |

## <a name="next-steps"></a>後續步驟

* 如需 Azure Event Grid 的簡介，請參閱[什麼是 Event Grid？](overview.md)
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
