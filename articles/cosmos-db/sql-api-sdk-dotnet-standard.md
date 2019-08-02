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
ms.openlocfilehash: 0368e99135dd6e377dd2820b3e673c55182319b9
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663801"
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
> * [大量執行程式-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [大量執行程式-JAVA](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK 下載**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)|
|**API 文件**|[.NET API 參考文件](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**範例**|[.NET 程式碼範例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)|
|**開始使用**|[開始使用 Azure Cosmos DB .NET SDK 教學課程](sql-api-get-started.md)|
|**Web 應用程式教學課程**|[使用 Azure Cosmos DB 進行 Web 應用程式開發](sql-api-dotnet-application.md)|
|**目前支援的架構**|[Microsoft .NET Standard 2.0](/dotnet/standard/net-standard)|

## <a name="release-notes"></a>版本資訊
### <a name="a-name310310"></a><a name="3.1.0"/>3.1.0
#### <a name="added"></a>已新增
- [#541](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/541)已將一致性層級新增至用戶端和查詢選項
- [#544](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/544)已新增 LINQ 的接續 token 支援
- [#557](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/557)已將觸發程式選項新增至專案要求選項
- [#571](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/571)已使用選擇性設定新增預設 JSON.net 序列化程式
- [#572](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/572)已在 CreateContainerIfNotExistsAsync 上新增資料分割索引鍵驗證
- [#581](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/581)新增 LINQ to QueryDefinition API
- [#592](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/592)已將 CreateIfNotExistsAsync 新增至容器產生器
- [#597](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/597)已將接續 token 屬性新增至 ResponseMessage
- [#604](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/604)已新增 LINQ ToStreamIterator 擴充方法

#### <a name="fixed"></a>固定
- [#548](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/548)已修正 CosmosException 中的錯誤輸入訊息 ();
- [#558](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/558)LocationCache ConcurrentDict 鎖定爭用修正
- [#561](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/561)GetItemLinqQueryable 現在可搭配 null 查詢使用
- [#567](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/567)查詢正確地處理不同的語言文化特性
- [#574](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/574)已修正查詢剖析因非預期的例外狀況而失敗時的空白錯誤訊息
- [#576](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/576)查詢正確地將輸入序列化為數據流

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
| [3.1.0](#3.1.0) |2019年7月29日 |--- |
| [3.0.0](#3.0.0) |2019年7月15日 |--- |


## <a name="faq"></a>常見問題集
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另請參閱
若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。 

