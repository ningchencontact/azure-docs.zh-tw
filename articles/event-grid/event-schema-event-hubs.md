---
title: Azure Event Grid 事件中樞事件結構描述
description: 描述 Azure Event Grid 中事件中樞事件的屬性
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 9c0113687d27bf43375f298057129a5594ec0a06
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561823"
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Azure Event Grid 事件中樞事件結構描述

本文提供事件中樞事件的屬性與結構描述。 如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。

如需範例指令碼和教學課程的清單，請參閱[事件中樞事件來源](event-sources.md#event-hubs)。

### <a name="available-event-types"></a>可用的事件類型

當擷取檔案建立時，事件中樞會引發 **Microsoft.EventHub.CaptureFileCreated** 事件類型。

## <a name="example-event"></a>事件範例

此範例顯示在擷取功能儲存檔案時引發之事件中樞事件的結構描述： 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
```

## <a name="event-properties"></a>事件屬性

事件具有下列的最高層級資料：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| topic | string | 事件來源的完整資源路徑。 此欄位不可寫入。 Event Grid 提供此值。 |
| subject | string | 發行者定義事件主體的路徑。 |
| eventType | string | 此事件來源已註冊的事件類型之一。 |
| eventTime | string | 事件產生的時間，以提供者之 UTC 時間為準。 |
| id | string | 事件的唯一識別碼。 |
| data | 物件 | 事件中樞事件資料。 |
| dataVersion | string | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | string | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| fileUrl | string | 擷取檔案的路徑。 |
| fileType | string | 擷取檔案的檔案類型。 |
| partitionId | string | 分區識別碼。 |
| sizeInBytes | integer | 檔案大小。 |
| eventCount | integer | 檔案中的事件數目。 |
| firstSequenceNumber | integer | 來自佇列的最小序號。 |
| lastSequenceNumber | integer | 來自佇列的最後一個序號。 |
| firstEnqueueTime | string | 來自佇列的第一個時間。 |
| lastEnqueueTime | string | 來自佇列的最後一個時間。 |

## <a name="next-steps"></a>後續步驟

* 如需 Azure Event Grid 的簡介，請參閱[什麼是 Event Grid？](overview.md)
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
* 如需處理事件中樞事件的詳細資訊，請參閱[將巨量資料串流至資料倉儲](event-grid-event-hubs-integration.md)。