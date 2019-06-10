---
title: Azure 應用程式設定索引鍵-值事件回應 |Microsoft Docs
description: 使用 Azure Event Grid 以訂閱應用程式設定的事件。
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 05/30/2019
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 601124aef37d2b285db71130f5c63b3620c7768f
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735642"
---
# <a name="reacting-to-azure-app-configuration-events"></a>對 Azure 應用程式組態事件做出反應

Azure 應用程式設定的事件讓應用程式回應索引鍵值的變更。 這是不需要複雜的程式碼或昂貴且效率不彰的輪詢服務。 而是改為透過 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) 將事件推送給訂閱者，例如 [Azure Functions](https://azure.microsoft.com/services/functions/)、[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)，甚至推送到您自己的自訂 http 接聽程式，且只需要支付有使用的項目的費用。

Azure 應用程式設定的事件會傳送至 Azure Event Grid 提供可靠的傳遞服務，透過豐富的應用程式重試原則 」 和 「 寄不出信件傳遞。 若要進一步了解，請參閱[Event Grid 訊息傳遞與重試](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)。

常見的應用程式組態事件案例包括重新整理應用程式組態，觸發部署或任何組態導向工作流程。 在變更不常見但情況需要立即回應的情況下，以事件為基礎的架構可能特別有效。

看看[路由 Azure 應用程式組態事件至自訂 web 端點-CLI](./howto-app-configuration-event.md)如需快速範例。 

![Event Grid 模型](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>可用的 Azure 應用程式設定事件
Event Grid 使用[事件訂閱](../event-grid/concepts.md#event-subscriptions)將事件訊息路由至訂閱者。 Azure 應用程式組態事件訂用帳戶可以包含兩種類型的事件：  

> |活動名稱|描述|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|當建立或取代的索引鍵-值時引發|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|刪除索引鍵-值時引發|

## <a name="event-schema"></a>結構描述
Azure 應用程式設定的事件包含您要回應您的資料變更的所有資訊。 因為 eventType 屬性開頭為"Microsoft.AppConfiguration 」，您可以識別應用程式組態事件。 Event Grid 事件屬性之使用方式的其他資訊列於[Event Grid 事件結構描述](../event-grid/event-schema.md)。  

> |屬性|類型|描述|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |topic|字串|會發出此事件的應用程式組態的完整 Azure Resource Manager 識別碼。|
> |subject|字串|為事件主體之索引鍵-值的 URI。|
> |eventTime|字串|日期/時間所產生的事件，採用 ISO 8601 格式。|
> |eventType|字串|「 Microsoft.AppConfiguration.KeyValueModified"或者"Microsoft.AppConfiguration.KeyValueDeleted 」。|
> |Id|string|此事件的唯一識別碼。|
> |dataVersion|string|資料物件的結構描述版本。|
> |metadataVersion|字串|最上層屬性的結構描述版本。|
> |data|物件|Azure 應用程式設定特定的事件資料集合|
> |data.key|string|已修改或刪除機碼值的索引鍵。|
> |data.label|字串|標籤，如果有的話，索引鍵-值的已修改或刪除。|
> |data.etag|字串|針對`KeyValueModified`新的索引鍵 / 值的 etag。 針對`KeyValueDeleted`已刪除的金鑰值的 etag。|

KeyValueModified 事件的範例如下：
```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

如需詳細資訊，請參閱 < [Azure 應用程式組態事件結構描述](../event-grid/event-schema-app-configuration.md)。

## <a name="practices-for-consuming-events"></a>消費事件做法
處理應用程式設定事件的應用程式應該遵循幾個建議的做法：
> [!div class="checklist"]
> * 事件路由傳送至相同的事件處理常式，可以設定多個訂用帳戶，請務必不要假設事件來自特定來源，而若要檢查以確定其來自您預期的應用程式設定為訊息的主題。
> * 同樣地，檢查 eventType 也是必須進行的步驟之一，而且不要假設您收到的所有事件都是您預期的類型。
> * 由於訊息可能會在延遲一段時間後以錯誤順序送達，請使用 [etag] 欄位以了解您的物件資訊是否仍是最新狀態。  使用 [排序器] 欄位以了解任何特定物件上的事件順序。
> * 您可以使用 [主體] 欄位來存取已修改機碼值。


## <a name="next-steps"></a>後續步驟

深入了解 Event Grid 和試用 Azure 應用程式組態事件：

- [關於 Event Grid](../event-grid/overview.md)
- [將 Azure 應用程式設定的事件路由至自訂的 web 端點](./howto-app-configuration-event.md)