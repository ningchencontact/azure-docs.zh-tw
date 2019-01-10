---
title: 了解如何在 Azure Cosmos DB 中管理資料庫帳戶
description: 了解如何在 Azure Cosmos DB 中管理資料庫帳戶
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: c27cee4842c0e65e1737f100a215cff82a0fd439
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033089"
---
# <a name="manage-indexing-in-azure-cosmos-db"></a>管理 Azure Cosmos DB 中的索引編製

在 Azure Cosmos DB 中，您可以選擇是否要讓容器自動編製所有項目的索引。 根據預設，Azure Cosmos 容器中的所有項目都會自動編制索引，但您可以關閉自動編制索引的功能。 當編制索引的功能關閉時，您只能透過項目自己的連結或透過使用項目識別碼 (例如文件識別碼) 進行查詢來存取項目。您可以明確要求提供沒有索引的結果，方法是在 REST API 中傳入 **x-ms-documentdb-enable-scan**  標頭，或是使用 .NET SDK 傳入 **EnableScanInQuery** 要求選項。

當自動編制索引的功能關閉時，您仍可選擇將特定項目新增到索引中。 相反地，您也可以讓自動編製索引的功能保持開啟，並選擇排除特定項目。 當您需要查詢項目的子集時，索引編製功能開/關組態相當有用。  

寫入輸送量與要求單位會與需要編製索引的值數目成正比，此數目則由索引編製原則中所包含的集合來指定。 如果您非常了解查詢模式，則可明確地選擇路徑的包含/排除子集，以改善寫入輸送量。

## <a name="manage-indexing-using-azure-portal"></a>使用 Azure 入口網站管理索引編製

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 建立新的 Azure Cosmos 帳戶，或選取現有帳戶。

3. 開啟 [資料總管] 窗格。

4. 選取現有容器，加以展開並修改下列值：

   * 開啟 [規模與設定] 視窗。
   * 將 **indexingMode** 從 *consistent* 變更為 *none*，或在索引編製中包含/排除特定路徑。
   * 按一下 [確定]  儲存變更。

   ![使用 Azure 入口網站管理索引編製](./media/how-to-manage-indexing/how-to-manage-indexing-portal.png)

## <a name="manage-indexing-using-azure-sdks"></a>使用 Azure SDK 管理索引編製

### <a id="dotnet"></a>.NET SDK

下列範例示範如何使用 [SQL API .NET SDK](sql-api-sdk-dotnet.md) 和 [RequestOptions.IndexingDirective](/dotnet/api/microsoft.azure.documents.client.requestoptions.indexingdirective) 屬性來明確地包含項目。

```csharp
// To override the default behavior to exclude (or include) a document in indexing,
// use the RequestOptions.IndexingDirective property.
client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new { id = "myDocumentId", isRegistered = true },
    new RequestOptions { IndexingDirective = IndexingDirective.Include });
```

## <a name="next-steps"></a>後續步驟

在下列文章中深入了解編製索引：

* [索引編製概觀](index-overview.md)
* [索引編製原則](index-policy.md)
* [索引類型](index-types.md)
* [索引路徑](index-paths.md)
