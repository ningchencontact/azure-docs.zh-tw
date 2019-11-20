---
title: 回應 Azure 應用程式組態索引鍵/值事件 |Microsoft Docs
description: 使用 Azure 事件方格來訂閱應用程式組態事件。
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 05/30/2019
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 5da64155f2823712eee7a60427b1c1e80abec068
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185290"
---
# <a name="reacting-to-azure-app-configuration-events"></a>回應 Azure 應用程式組態事件

Azure 應用程式組態事件可讓應用程式對索引鍵/值的變更做出反應。 這樣做不需要複雜的程式碼或昂貴且無效率的輪詢服務。 而是改為透過 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) 將事件推送給訂閱者，例如 [Azure Functions](https://azure.microsoft.com/services/functions/)、[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)，甚至推送到您自己的自訂 http 接聽程式，且只需要支付有使用的項目的費用。

Azure 應用程式組態事件會傳送至 Azure 事件方格，透過豐富的重試原則和寄不出的信件傳遞，為您的應用程式提供可靠的傳遞服務。 若要深入瞭解，請參閱[事件方格訊息傳遞和重試](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)。

常見的應用程式組態事件案例包括重新整理應用程式設定、觸發部署或任何設定導向的工作流程。 在變更不常見但情況需要立即回應的情況下，以事件為基礎的架構可能特別有效。

如需快速範例，請參閱[Route Azure 應用程式組態事件路由至自訂 web 端點-CLI](./howto-app-configuration-event.md) 。 

![Event Grid 模型](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>可用的 Azure 應用程式組態事件
Event Grid 使用[事件訂閱](../event-grid/concepts.md#event-subscriptions)將事件訊息路由至訂閱者。 Azure 應用程式組態事件訂閱可以包含兩種類型的事件：  

> |活動名稱|描述|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|建立或取代索引鍵/值時引發|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|刪除索引鍵/值時引發|

## <a name="event-schema"></a>結構描述
Azure 應用程式組態事件包含回應資料變更所需的所有資訊。 您可以識別應用程式組態事件，因為「事件識別碼」屬性會以 "AppConfiguration" 開頭。 Event Grid 事件屬性之使用方式的其他資訊列於[Event Grid 事件結構描述](../event-grid/event-schema.md)。  

> |屬性|在系統提示您進行確認時，輸入|描述|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |主題|字串|發出事件之應用程式組態的完整 Azure Resource Manager 識別碼。|
> |主旨|字串|索引鍵/值的 URI，此為事件的主旨。|
> |eventTime|字串|產生事件的日期/時間，以 ISO 8601 格式表示。|
> |eventType|字串|"AppConfiguration. KeyValueModified" 或 "AppConfiguration. KeyValueDeleted"。|
> |id|字串|這個事件的唯一識別碼。|
> |dataVersion|字串|資料物件的結構描述版本。|
> |metadataVersion|字串|最上層屬性的結構描述版本。|
> |data|物件|Azure 應用程式組態特定事件資料的集合|
> |資料。索引鍵|字串|已修改或刪除之索引鍵/值的索引鍵。|
> |資料。標籤|字串|已修改或刪除之索引鍵/值的標籤（如果有的話）。|
> |資料. etag|字串|適用于 `KeyValueModified` 新索引鍵/值的 etag。 針對 `KeyValueDeleted` 已刪除之索引鍵/值的 etag。|

以下是 KeyValueModified 事件的範例：
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

如需詳細資訊，請參閱[Azure 應用程式組態事件架構](../event-grid/event-schema-app-configuration.md)。

## <a name="practices-for-consuming-events"></a>消費事件做法
處理應用程式組態事件的應用程式應該遵循幾個建議做法：
> [!div class="checklist"]
> * 因為有多個訂用帳戶可以設定為將事件路由傳送至相同的事件處理常式，所以請務必假設事件來自特定來源，但要檢查訊息主題，以確保其來自您預期的應用程式組態。
> * 同樣地，檢查 eventType 也是必須進行的步驟之一，而且不要假設您收到的所有事件都是您預期的類型。
> * 由於訊息可能會在延遲一段時間後以錯誤順序送達，請使用 [etag] 欄位以了解您的物件資訊是否仍是最新狀態。  使用 [排序器] 欄位以了解任何特定物件上的事件順序。
> * 使用 [主旨] 欄位存取已修改的索引鍵/值。


## <a name="next-steps"></a>後續步驟

深入瞭解 Event Grid 並提供 Azure 應用程式組態的事件嘗試：

- [關於 Event Grid](../event-grid/overview.md)
- [將 Azure 應用程式組態事件路由至自訂 web 端點](./howto-app-configuration-event.md)