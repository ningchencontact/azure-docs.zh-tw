---
title: 查詢 Azure Cosmos DB 中的容器
description: 了解如何查詢 Azure Cosmos DB 中的容器
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 08d9978134ce214a468691ec367fb1797f6e86fc
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457746"
---
# <a name="query-an-azure-cosmos-container"></a>查詢 Azure Cosmos 容器

本文說明如何查詢 Azure Cosmos DB 中的容器 (集合、圖表、資料表) 的輸送量。

## <a name="in-partition-query"></a>分割區內查詢

當您查詢容器中的資料時，如果查詢已指定分割區索引鍵篩選，則 Azure Cosmos DB 會自動將查詢路由到與篩選中指定的分割區索引鍵值對應的分割區。 例如，下列查詢會路由到 DeviceId 分割區，其中包含對應至分割區索引鍵值 "XMS-0001" 的所有文件。

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>跨分割區查詢

下列查詢沒有根據資料分割索引鍵 (DeviceId) 的篩選，且已展開至對資料分割索引執行它的所有資料分割。 若要跨分割區執行查詢，請將 **EnableCrossPartitionQuery** 設為 true (在 REST API 中則設為 x-ms-documentdb-query-enablecrosspartition)。

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Cosmos DB 支援使用 SQL 對容器執行彙總函式 COUNT、MIN、MAX 和 AVG。 對容器執行彙總函式的功能始於 SDK 1.12.0 版。 查詢必須包含單一彙總運算子，而且在投射中必須包含單一值。

## <a name="parallel-cross-partition-query"></a>跨分割區的平行查詢

Cosmos DB SDK 1.9.0 和更新版本支援平行查詢執行選項。  跨分割區的平行查詢可讓您執行低延遲的跨分割區查詢。 例如，下列查詢設定為跨資料分割平行執行。

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

若要管理平行執行查詢，您可以調整下列參數︰

- **MaxDegreeOfParallelism**：設定同時連往容器分割區的網路連線數上限。 如果您將此屬性設定為 -1，平行處理原則的程度會由 SDK 管理。 如果 MaxDegreeOfParallelism 未指定或設為 0 (預設值)，將會有連往容器分割區的單一網路連線。

- **MaxBufferedItemCount**：權衡取捨查詢延遲性和用戶端記憶體使用量。 如果省略此選項或將其設定為 -1，平行查詢執行期間緩衝處理的項目數將由 SDK 管理。

在相同的集合狀態下，平行查詢會以和序列執行相同的順序傳回結果。 執行包含排序運算子 (ORDER BY 和/或 TOP) 的跨分割區查詢時，Azure Cosmos DB SDK 會跨分割區發出平行查詢，並合併用戶端中已部分排序的結果來產生全域排序的結果。

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以了解 Cosmos DB 中的資料分割：

- [Azure Cosmos DB 中的資料分割](partitioning-overview.md)
- [Azure Cosmos DB 中的綜合分割區索引鍵](synthetic-partition-keys.md)
