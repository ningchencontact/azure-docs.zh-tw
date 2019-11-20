---
title: 變更 Azure Cosmos DB 的 MongoDB API 中的資料流程
description: 瞭解如何使用變更串流 n Azure Cosmos DB 適用于 MongoDB 的 API 來取得對您的資料所做的變更。
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: fbfce1c107fcf4b6f7d0b5f590a8ddfa64e69190
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184729"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>變更 Azure Cosmos DB 的 MongoDB API 中的資料流程

您可以使用變更資料流程 API，取得 Azure Cosmos DB 的 MongoDB API 中的[變更](change-feed.md)摘要支援。 藉由使用變更資料流程 API，您的應用程式可以取得對集合或單一分區中的專案所做的變更。 稍後您可以根據結果採取進一步的動作。 集合中的專案變更會依照其修改時間順序來捕捉，而排序次序則是依據分區索引鍵來保證。

下列範例顯示如何取得集合中所有專案的變更資料流程。 這個範例會建立一個資料指標，以便在插入、更新或取代專案時加以監看。 若要取得變更資料流程，必須要有 $match 階段、$project 階段和 fullDocument 選項。 目前不支援使用變更資料流程來監看刪除作業。 因應措施是，您可以在要刪除的專案上新增軟標記。 例如，您可以在名為「已刪除」的專案中加入屬性，並將它設定為 "true"，並在專案上設定 TTL，讓您可以自動將它刪除並加以追蹤。

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

下列範例顯示如何在單一分區中取得專案的變更。 這個範例會取得分區索引鍵等於 "a" 且分區索引鍵值等於 "1" 之專案的變更。

```javascript
var cursor = db.coll.watch(
    [
        { 
            $match: { 
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="current-limitations"></a>目前的限制

使用變更資料流程時，適用下列限制：

* 輸出檔案中尚未支援 `operationType` 和 `updateDescription` 屬性。
* 目前支援 `insert`、`update`和 `replace` 作業類型。 尚未支援刪除作業或其他事件。

由於這些限制，因此需要 $match 階段、$project 階段和 fullDocument 選項，如先前範例所示。

## <a name="error-handling"></a>錯誤處理

使用變更資料流程時，支援下列錯誤碼和訊息：

* **HTTP 錯誤碼 429** -當變更資料流程受到節流處理時，它會傳回空白頁面。

* **NamespaceNotFound （OperationType 無效）** -如果您在不存在的集合上執行變更資料流程，或卸載集合，則會傳回 `NamespaceNotFound` 錯誤。 因為 `operationType` 屬性無法在輸出檔案中傳回，而是傳回 `NamespaceNotFound` 錯誤，而不是 `operationType Invalidate` 錯誤。

## <a name="next-steps"></a>後續步驟

* [使用存留時間以在 Azure Cosmos DB 的 MongoDB API 中自動使資料過期](mongodb-time-to-live.md)
* [在 Azure Cosmos DB 的 MongoDB API 中編制索引](mongodb-indexing.md)