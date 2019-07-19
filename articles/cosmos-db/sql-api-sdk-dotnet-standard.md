---
title: Azure Cosmos DB：SQL .NET Standard API、SDK & 資源
description: 深入瞭解 SQL API 和 .NET SDK, 包括發行日期、停用日期, 以及 Azure Cosmos DB .NET SDK 每個版本之間所做的變更。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 07/12/2019
ms.author: dech
ms.openlocfilehash: 01645e66907e01b2e8fcb771976b50e5bbc8abf4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229020"
---
# <a name="azure-cosmos-db-net-standard-sdk-for-sql-api-download-and-release-notes"></a>適用于 SQL API 的 Azure Cosmos DB .NET Standard SDK:下載和版本資訊
> [!div class="op_single_selector"]
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
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
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK 下載**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)|
|**API 文件**|[.NET API 參考文件](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**範例**|[.NET 程式碼範例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)|
|**開始使用**|[開始使用 Azure Cosmos DB .NET SDK 教學課程](sql-api-get-started.md)|
|**Web 應用程式教學課程**|[使用 Azure Cosmos DB 進行 Web 應用程式開發](sql-api-dotnet-application.md)|
|**目前支援的架構**|[Microsoft .NET Standard 2.0](/dotnet/standard/net-standard)|

## <a name="release-notes"></a>版本資訊

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0 
* .NET SDK[版本 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)的正式推出
* 目標 .NET Standard 2.0, 支援 .NET framework 4.6.1 + 和 .NET Core 2.0 +
* 新的物件模型, 其中包含最上層的 CosmosClient 和方法, 會分割到相關的資料庫和容器類別
* 新的高效能串流 Api
* 變更摘要處理器 Api 的內建支援
* 適用于 CosmosClient、容器和變更摘要處理器的流暢 builder Api
* 慣用輸送量管理 Api
* 資料庫、容器、專案、查詢和輸送量要求的細微 RequestOptions 和 ResponseTypes
* 調整非資料分割容器的能力 
* 可擴充且可自訂的序列化程式
* 支援自訂處理常式的可擴充要求管線


## <a name="release--retirement-dates"></a>發行和停用日期
Microsoft 至少會在停用 SDK 的 **12 個月** 之前提供通知，以供順利轉換至較新/支援的版本。

新的功能與最佳化項目只會新增至目前的 SDK，因此建議您一律盡早升級至最新的 SDK 版本。 

服務會拒絕使用已停用 SDK 的任何 Azure Cosmos DB 要求。

<br/>

| Version | 發行日期 | 停用日期 |
| --- | --- | --- |
| [3.0.0](#3.0.0) |2019年7月15日 |--- |

## <a name="faq"></a>常見問題集
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另請參閱
若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。 

