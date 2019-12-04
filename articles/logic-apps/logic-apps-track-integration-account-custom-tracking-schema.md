---
title: B2B 訊息的自訂追蹤架構
description: 建立自訂追蹤結構描述，以針對採用 Enterprise Integration Pack 的 Azure Logic Apps 監視整合帳戶中的 B2B 訊息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 7d7c5ef9e9a86c8b061a56fe41c0c8bbfc5ddbb3
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792791"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>建立自訂追蹤結構描述，以監視 Azure Logic Apps 中的端對端工作流程

有內建的追蹤可供您啟用您不同部分的企業對企業工作流程，例如追蹤 AS2 或 X12 訊息。 當您建立包含邏輯應用程式、BizTalk Server、SQL Server 或任何其他層級的工作流程時，您可以啟用自訂追蹤，記錄從您工作流程開始到結尾的事件。 

本文提供您可以在邏輯應用程式外部層級中使用的自訂程式碼。 

## <a name="custom-tracking-schema"></a>自訂追蹤結構描述

```json
{
   "sourceType": "",
   "source": {
      "workflow": {
         "systemId": ""
      },
      "runInstance": {
         "runId": ""
      },
      "operation": {
         "operationName": "",
         "repeatItemScopeName": "",
         "repeatItemIndex": "",
         "trackingId": "",
         "correlationId": "",
         "clientRequestId": ""
      }
   },
   "events": [
      {
         "eventLevel": "",
         "eventTime": "",
         "recordType": "",
         "record": {                
         }
      }
   ]
}
```

| 屬性 | 必要項 | Type | 描述 |
| --- | --- | --- | --- |
| sourceType | 是 |   | 執行來源的類型。 允許的值為 **Microsoft.Logic/workflows** 和 **custom**。 |
| source | 是 |   | 如果來源類型是 **Microsoft.Logic/workflows**，則來源資訊必須遵循此結構描述。 如果來源類型為 **custom**，則結構描述為 JToken。 |
| systemId | 是 | String | 邏輯應用程式系統識別碼。 |
| runId | 是 | String | 邏輯應用程式執行識別碼。 |
| operationName | 是 | String | 作業 (例如動作或觸發程序) 的名稱。 |
| repeatItemScopeName | 是 | String | 如果動作在 `foreach`/`until` 迴圈內，重複項目名稱。 |
| repeatItemIndex | 是 | 整數 | 動作是否在 `foreach`/`until` 迴圈內。 指出重複的項目索引。 |
| trackingId | 否 | String | 追蹤識別碼，使訊息相互關聯。 |
| correlationId | 否 | String | 相互關連識別碼，使訊息相互關聯。 |
| clientRequestId | 否 | String | 用戶端可以填入此值，使訊息相互關聯。 |
| eventLevel | 是 |   | 事件的層級。 |
| eventTime | 是 |   | 事件的時間，以 UTC 格式 YYYY-MM-DDTHH:MM:SS.00000Z。 |
| recordType | 是 |   | 追蹤記錄的類型。 允許的值為 **custom**。 |
| record | 是 |   | 自訂記錄類型。 允許的格式為 JToken。 |
||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 通訊協定追蹤結構描述

如需 B2B 通訊協定追蹤結構描述的相關資訊，請參閱︰

* [AS2 追蹤結構描述](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [X12 追蹤結構描述](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>後續步驟

* [深入了解監視 B2B 訊息](logic-apps-monitor-b2b-message.md)
* 瞭解如何[在 Azure 監視器記錄中追蹤 B2B 訊息](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
