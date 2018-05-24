---
title: Azure Event Grid 資源群組事件結構描述
description: 描述 Azure Event Grid 中資源群組事件的屬性
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 163c32bdb8a3fdc278404b9e26fdc3097797d16c
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/18/2018
---
# <a name="azure-event-grid-event-schema-for-resource-groups"></a>Azure Event Grid 資源群組事件結構描述

本文提供資源群組事件的屬性與結構描述。 如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。

Azure 訂用帳戶和資源群組會發出相同的事件類型。 事件類型與資源中的變更相關聯。 主要的差異在於資源群組所發出的事件是針對資源群組內的資源，而 Azure 訂用帳戶發出的事件是針對整個訂用帳戶上的資源。 

## <a name="available-event-types"></a>可用的事件類型

資源群組從 Azure Resource Manager 發出管理事件，像是在虛擬機器建立時或儲存體帳戶刪除時皆可使用此功能。

| 事件類型 | 說明 |
| ---------- | ----------- |
| Microsoft.Resources.ResourceWriteSuccess | 在資源建立或是更新作業成功時引發。 |
| Microsoft.Resources.ResourceWriteFailure | 在資源建立或是更新作業失敗時引發。 |
| Microsoft.Resources.ResourceWriteCancel | 在資源建立或是更新作業取消時引發。 |
| Microsoft.Resources.ResourceDeleteSuccess | 在資源刪除作業成功時引發。 |
| Microsoft.Resources.ResourceDeleteFailure | 在資源刪除作業失敗時引發。 |
| Microsoft.Resources.ResourceDeleteCancel | 在資源刪除作業取消時引發。 此事件會於範本部署取消時發生。 |

## <a name="example-event"></a>事件範例

下列範例顯示資源已建立事件的結構描述： 

```json
[
  {
    "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"{request-operation}",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

資源已刪除事件的結構描述也相當類似：

```json
[{
  "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicApp0ecd6c02-2296-4d7c-9865-01532dc99c93",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2017-11-07T21:24:19.6959483Z",
  "id": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
  "data": {
    "authorization": "{azure_resource_manager_authorizations}",
    "claims": "{azure_resource_manager_claims}",
    "correlationId": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
    "httpRequest": "{request-operation}",
    "resourceProvider": "Microsoft.EventGrid",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "operationName": "Microsoft.EventGrid/eventSubscriptions/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>事件屬性

事件具有下列的最高層級資料：

| 屬性 | 類型 | 說明 |
| -------- | ---- | ----------- |
| 主題 | 字串 | 事件來源的完整資源路徑。 此欄位不可寫入。 Event Grid 提供此值。 |
| 主旨 | 字串 | 發行者定義事件主體的路徑。 |
| eventType | 字串 | 此事件來源已註冊的事件類型之一。 |
| eventTime | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| id | 字串 | 事件的唯一識別碼。 |
| data | 物件 | 資源群組事件資料。 |
| dataVersion | 字串 | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | 字串 | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |

資料物件具有下列屬性：

| 屬性 | 類型 | 說明 |
| -------- | ---- | ----------- |
| 授權 | 字串 | 作業的所要求授權。 |
| claims | 字串 | 宣告的屬性。 |
| correlationId | 字串 | 用於疑難排解的作業識別碼。 |
| httpRequest | 字串 | 作業的詳細資料。 |
| resourceProvider | 字串 | 執行作業的資源提供者。 |
| resourceUri | 字串 | 作業中資源的 URI。 |
| operationName | 字串 | 已執行的作業。 |
| status | 字串 | 作業狀態。 |
| subscriptionId | 字串 | 資源的訂用帳戶識別碼。 |
| tenantId | 字串 | 資源的租用戶識別碼。 |

## <a name="next-steps"></a>後續步驟

* 如需 Azure Event Grid 的簡介，請參閱[什麼是 Event Grid？](overview.md)
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
