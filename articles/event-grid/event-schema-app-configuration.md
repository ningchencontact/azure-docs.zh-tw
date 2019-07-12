---
title: Azure 事件格線 Azure 應用程式組態事件結構描述
description: 描述 Azure Event grid 的 Azure 應用程式設定事件的屬性
services: event-grid
author: jimmyca
ms.service: event-grid
ms.topic: reference
ms.date: 05/30/2019
ms.author: jimmyca
ms.openlocfilehash: fe0274f723692eea3cfd25cc0e9e146b35dce2ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735777"
---
# <a name="azure-event-grid-event-schema-for-azure-app-configuration"></a>Azure 應用程式設定的 azure Event Grid 事件結構描述

這篇文章提供 Azure 應用程式設定事件的屬性和結構描述。 如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。

如需範例指令碼和教學課程的清單，請參閱 < [Azure 應用程式設定事件來源](event-sources.md#app-configuration)。

## <a name="available-event-types"></a>可用的事件類型

Azure 應用程式組態會發出下列事件類型：

| 事件類型 | 描述 |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | 當建立或取代的索引鍵-值時引發。 |
| Microsoft.AppConfiguration.KeyValueDeleted | 刪除索引鍵-值時，就會引發。 |

## <a name="example-event"></a>事件範例

下列範例顯示索引鍵-值已修改的事件結構的描述： 

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

索引鍵-值已刪除的事件結構描述如下： 

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
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
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
| data | object | 應用程式設定事件資料。 |
| dataVersion | string | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | string | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |

資料物件具有下列屬性：

| 屬性 | type | 描述 |
| -------- | ---- | ----------- |
| key | string | 已修改或刪除機碼值的索引鍵。 |
| label | string | 標籤，如果有的話，索引鍵-值的已修改或刪除。 |
| etag | string | 針對`KeyValueModified`新的索引鍵 / 值的 etag。 針對`KeyValueDeleted`已刪除的金鑰值的 etag。 |
 
## <a name="next-steps"></a>後續步驟

* 如需 Azure Event Grid 的簡介，請參閱[什麼是 Event Grid？](overview.md)
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
* 如需如何使用 Azure 應用程式設定事件的簡介，請參閱[路由 Azure 應用程式組態事件-Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json)。 