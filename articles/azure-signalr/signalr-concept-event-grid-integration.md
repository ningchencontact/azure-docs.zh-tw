---
title: 回應 Azure SignalR 服務事件
description: 使用 Azure Event Grid 訂閱 Azure SignalR 服務事件。
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 06/12/2019
ms.topic: conceptual
ms.service: azure-signalr
ms.openlocfilehash: 02f88c5953d499b30f2ea3408318f70a72f42b0f
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789185"
---
# <a name="reacting-to-azure-signalr-service-events"></a>回應 Azure SignalR 服務事件

Azure SignalR 服務事件讓回應連線或中斷連線使用新式無伺服器架構的用戶端連線的應用程式。 它不需要複雜的程式碼或昂貴且無效率的輪詢服務來執行此動作。  而是改為透過 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) 將事件推送給訂閱者，例如 [Azure Functions](https://azure.microsoft.com/services/functions/)、[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)，甚至推送到您自己的自訂 http 接聽程式，且只需要支付有使用的項目的費用。

Azure SignalR 服務事件會可靠地傳送至 Event Grid 服務可提供可靠的傳遞服務，透過豐富的應用程式重試原則 」 和 「 寄不出信件傳遞。 若要進一步了解，請參閱[Event Grid 訊息傳遞與重試](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)。

![Event Grid 模型](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>無伺服器的狀態
用戶端連接都在無伺服器的狀態時，才會作用中 azure SignalR 服務事件。 一般而言，如果用戶端不會路由傳送至中樞伺服器，它會進入無伺服器的狀態。 傳統模式工作中樞，用戶端連線連接，不需要集線器伺服器時，才。 不過，建議無伺服器的模式，避免發生問題。 如需有關服務模式的詳細資訊，請參閱[如何選擇 Service 模式](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)。

## <a name="available-azure-signalr-service-events"></a>可用的 Azure SignalR 服務事件
Event Grid 使用[事件訂閱](../event-grid/concepts.md#event-subscriptions)將事件訊息路由至訂閱者。 Azure SignalR 服務事件訂用帳戶支援兩種類型的事件：  

|活動名稱|描述|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|當用戶端連線時，就會引發。|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|當用戶端連線已中斷連接時引發。|

## <a name="event-schema"></a>結構描述
Azure SignalR 服務事件會包含您要回應您的資料變更的所有資訊。 您可以識別 Azure SignalR 服務事件與 eventType 屬性開始使用 「 Microsoft.SignalRService"。 Event Grid 事件屬性的使用方式的詳細資訊記錄在[Event Grid 事件結構描述](../event-grid/event-schema.md)。  

用戶端連接的連接事件的範例如下：
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

如需詳細資訊，請參閱 < [SignalR 服務事件結構描述](../event-grid/event-schema-azure-signalr.md)。

## <a name="next-steps"></a>後續步驟

深入了解 Event Grid 和試用 Azure SignalR 服務事件：

> [!div class="nextstepaction"]
> [請嘗試使用 Azure SignalR Service 範例 Event Grid 的整合](./signalr-howto-event-grid-integration.md)
> [關於 Event Grid](../event-grid/overview.md)
