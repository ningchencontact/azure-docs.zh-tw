---
title: 查詢 Azure Cosmos DB 中的容器
description: 了解如何查詢 Azure Cosmos DB 中的容器
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 799fa43ad6ff12e5fa84326cbb41842e76daff12
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092974"
---
# <a name="query-an-azure-cosmos-container"></a>查詢 Azure Cosmos 容器

本文說明如何查詢 Azure Cosmos DB 中的容器 (集合、圖表或資料表) 的輸送量。

## <a name="in-partition-query"></a>分割區內查詢

當您查詢容器中的資料時，如果查詢已指定分割區索引鍵篩選，則 Azure Cosmos DB 會自動處理查詢。 它會將查詢路由傳送到與篩選條件中指定的分割區索引鍵值對應的分割區。 例如，下列查詢會路由傳送到 `DeviceId` 分割區，其中包含對應至分割區索引鍵值 `XMS-0001` 的所有文件。

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>跨分割區查詢

下列查詢沒有根據資料分割索引鍵 (`DeviceId`) 的篩選，且已展開至對資料分割索引執行它的所有資料分割。 若要跨分割區執行查詢，請將 `EnableCrossPartitionQuery` 設為 true (或在 REST API 中設為 `x-ms-documentdb-query-enablecrosspartition` )。

EnableCrossPartitionQuery 屬性接受布林值。 在設定為 true 的狀況下，如果查詢沒有分割區索引鍵，Azure Cosmos DB 會將查詢分散到各個分割區。 分散方式是將個別查詢發給所有分割區。 若要讀取查詢結果，用戶端應用程式應該從 FeedResponse 取用結果，並查看 ContinuationToken 屬性。 若要讀取所有結果，請逐一查看資料，直到 ContinuationToken 為 null。 

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Azure Cosmos DB 支援使用 SQL 對容器執行彙總函式 COUNT、MIN、MAX 和 AVG。 對容器執行彙總函式的功能始於 SDK 1.12.0 版。 查詢必須包含單一彙總運算子，而且在投射中必須包含單一值。

## <a name="parallel-cross-partition-query"></a>跨分割區的平行查詢

Azure Cosmos DB SDK 1.9.0 和更新版本支援平行查詢執行選項。 跨分割區的平行查詢可讓您執行低延遲的跨分割區查詢。 例如，下列查詢設定為跨資料分割平行執行。

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

若要管理平行執行查詢，您可以調整下列參數︰

- **MaxDegreeOfParallelism**：設定同時連往容器分割區的網路連線數上限。 如果您將此屬性設定為 -1，則 SDK 會管理平行處理的程度。 如果  `MaxDegreeOfParallelism`  未指定或設為 0 (這是預設值)，則有連往容器資料分割的單一網路連線。

- **MaxBufferedItemCount**：權衡取捨查詢延遲性和用戶端記憶體使用量。 如果省略此選項或將其設定為 -1，則 SDK 會管理在平行查詢執行期間緩衝處理的項目數。

在相同的集合狀態下，平行查詢會以和序列執行相同的順序傳回結果。 執行包含排序運算子 (ORDER BY、TOP) 的跨分割區查詢時，Azure Cosmos DB SDK 會跨分割區發出平行查詢。 它會合併用戶端中已部分排序的結果來產生全域排序的結果。

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以了解 Azure Cosmos DB 中的資料分割：

- [Azure Cosmos DB 中的資料分割](partitioning-overview.md)
- [Azure Cosmos DB 中的綜合分割區索引鍵](synthetic-partition-keys.md)
