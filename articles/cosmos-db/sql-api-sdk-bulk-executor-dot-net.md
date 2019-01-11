---
title: Azure Cosmos DB：大量執行程式 .NET API、SDK 和資源
description: 了解所有大量執行程式 .NET API 和 SDK 相關資訊，包括發行日期、停用日期及 Azure Cosmos DB 大量執行程式 .NET SDK 每個版本之間所作出的變更。
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/19/2018
ms.author: ramkris
ms.openlocfilehash: 8675c5ff90f7be2dc0b3ac80eb593adb613ba860
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039753"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET 大量執行程式程式庫：下載資訊 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 變更摘要](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [非同步 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 資源提供者](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [大量執行程式 - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [大量執行程式 - Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**說明**</td><td>大量執行程式程式庫可讓用戶端應用程式執行大量的 Azure Cosmos DB 帳戶作業。 大量執行程式程式庫提供 BulkImport、BulkUpdate 和 BulkDelete 命名空間。 BulkImport 模組能夠以最佳化方式大量內嵌文件，如此就能充分取用為集合佈建的輸送量。 BulkUpdate 模組能夠以修補程式的形式，大量更新 Azure Cosmos DB 容器中的現有資料。 BulkDelete 模組能夠以最佳化方式大量刪除文件，如此就能充分取用為集合佈建的輸送量。</td></tr>

<tr><td>**SDK 下載**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/)</td></tr>

<tr><td>**GitHub 中的 BulkExecutor 程式庫**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)</td></tr>

<tr><td>**API 文件**</td><td>[.Net API 參考文件](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)</td></tr>

<tr><td>**開始使用**</td><td>[開始使用大量執行程式程式庫 .NET SDK](bulk-executor-dot-net.md)</td></tr>

<tr><td>**目前支援的架構**</td><td>Microsoft .NET Framework 4.5.2, 4.6.1 與 .NET Standard 2.0 </td></tr>
</table></br>

## <a name="release-notes"></a>版本資訊

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* 包括 MongoBulkExecutor 以支援 .NET Standard 2.0。 這項功能使其功能相當於 1.3.0 版本，加上支援 .NET Standard 2.0 目標架構。

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* 已新增 .NET Standard 2.0 為其中一個支援的目標架構，以讓 BulkExecutor 程式庫可以使用 .NET Core 應用程式。

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* 已新增 BulkDelete 作業的多載，以便 SQL API 帳戶接受資料分割索引鍵 (要刪除的文件識別碼 Tuple)。
* 已新增 BulkDelete 作業的多載，以便 SQL API 帳戶使用 RequestOptions 作為輸入查詢的篩選條件來指定要刪除的文件，並接受 RequestOptions (其包含指定資料分割索引鍵值的資料分割索引鍵)。
* 已修正導致 BulkExecutor 所用之使用者代理程式的格式問題。

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* 改善 BulkExecutor 匯入及更新 API，以在儲存體超過目前容量時順暢地適應 Cosmos DB 容器的彈性調整，而不擲回例外狀況。

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* 將 DocumentDB.NET SDK 相依性提高至版本 2.1.3。

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* 已修正導致 BulkExecutor 匯入固定集合時擲回 JSRT 錯誤的問題。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* 新增對 Azure Cosmos DB SQL API 帳戶之 BulkDelete 作業的支援。
* 若帳戶有適用於 MongoDB 的 Azure Cosmos DB API，可對其進行 BulkImport 作業。
* 將 DocumentDB.NET SDK 相依性提高至版本 2.0.0。 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* 新增對 Azure Cosmos DB Gremlin API 帳戶之 BulkImport 作業的支援。

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* 修正 Azure Cosmos DB SQL API 帳戶之 BulkImport 作業的次要錯誤。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* 新增對 Azure Cosmos DB SQL API 帳戶之 BulkImport 和 BulkUpdate 作業的支援。

## <a name="next-steps"></a>後續步驟

若要深入了解大量執行程式的 Java 程式庫，請參閱下列文章：

[Java 大量執行程式程式庫 SDK 和版本資訊](sql-api-sdk-bulk-executor-java.md)
