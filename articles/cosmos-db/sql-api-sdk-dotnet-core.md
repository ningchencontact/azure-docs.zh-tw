---
title: Azure Cosmos DB：SQL .NET Core API、SDK 與資源
description: 全面了解 SQL .NET Core API 和 SDK，包括發行日期、停用日期及 Azure Cosmos DB .NET Core SDK 每個版本之間的變更。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: bae180e2ceae6fe0768a5f7951c18dc5147870fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626820"
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>適用於 SQL API 的 Azure Cosmos DB .NET SDK：版本資訊和資源
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
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK 下載**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)|
|**API 文件**|[.NET API 參考文件](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**範例**|[.NET 程式碼範例](sql-api-dotnet-samples.md)|
|**開始使用**|[開始使用 Azure Cosmos DB .NET Core SDK 教學課程](sql-api-dotnet-core-get-started-preview.md)|
|**Web 應用程式教學課程**|[使用 Azure Cosmos DB 進行 Web 應用程式開發](sql-api-dotnet-application.md)|
|**目前支援的架構**|[.NET Standard 1.6 和 .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>版本資訊

Azure Cosmos DB .NET Core SDK 有與最新版 [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet.md) 類似的功能。

### <a name="a-name3001-preview3001-preview"></a><a name="3.0.0.1-preview"/>3.0.0.1-preview
* 用於公開預覽之 .NET SDK [3.0.0 版](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) \(英文\) 的 Preview 1。
* 目標 .NET Standard (支援 .NET Framework 4.6.1+ 和 .NET Core 2.0+)
* 新的物件模型，其最上層 CosmosClient 和方法分佈在相關 CosmosDatabases、CosmosContainers 與 CosmosItems 類別之間。
* 支援資料流。
* 更新來自伺服器的 CosmosResponseMessage 以傳回狀態碼，並只會在沒有傳回回應時擲回例外狀況。

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3

* 診斷改良功能

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2

* 新增的環境變數設定「POCOSerializationOnly」。

* 已移除 DocumentDB.Spatial.Sql.dll 而且現在包含在 Microsoft.Azure.Documents.ServiceInterop.dll 中

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1

* 針對 StoredProcedure 執行呼叫改善容錯移轉期間的重試邏輯。

* 建立 Made DocumentClientEventSource singleton。 

* 修正不接受 ConnectionPolicy RequestTimeout 的 GatewayAddressCache 逾時。

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* 針對直接/TCP 傳輸診斷新增 TransportException，此為 SDK 的內部例外狀況類型。 當此類型存在於例外狀況訊息中時會列印其他資訊，以針對用戶端連線問題進行疑難排解。

* 新增新的建構函式多載，它接受 HttpMessageHandler，此為用來傳送 HttpClient 要求 (例如 HttpClientHandler) 的 HTTP 處理常式堆疊。

* 修正沒有正確處理具 Null 值之標頭的錯誤 (bug)。

* 改善集合快取驗證。

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3

* 已將 System.Net.Security 更新至 4.3.2。

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* 診斷追蹤的改進。

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1

* 對多區域要求暫時性失敗新增更多復原能力。

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* 已新增多區域寫入支援。
* 透過 TOP 和 MaxBufferedItemCount 改善了跨分割區查詢效能。

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* 已新增要求取消支援。
* 已將 SetCurrentLocation 新增至 ConnectionPolicy，以根據區域自動填入所要的位置。
* 已修正具有 Min/Max 和篩選條件且不符合個別分割區上任何文件的跨分割區查詢所發生的錯誤。
* DocumentClient 方法現在會與 IDocumentClient 對應。
* 已更新直接 TCP 傳輸堆疊來減少所建立的連線數。
* 已新增對於非 Windows 用戶端的直接模式 TCP 支援。

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* 已新增要求取消支援。
* 已將 SetCurrentLocation 新增至 ConnectionPolicy，以根據區域自動填入所要的位置。
* 已修正具有 Min/Max 和篩選條件且不符合個別分割區上任何文件的跨分割區查詢所發生的錯誤。

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* DocumentClient 方法現在會與 IDocumentClient 對應。
* 已更新直接 TCP 傳輸堆疊來減少所建立的連線數。
* 已新增對於非 Windows 用戶端的直接模式 TCP 支援。

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0

* 已將 ConsistencyLevel 屬性新增至 FeedOptions。
* 已將 JsonSerializerSettings 新增至 RequestOptions 和 FeedOptions。
* 已將 EnableReadRequestsFallback 新增至 ConnectionPolicy。

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1

* 修正跨分割區 Order By 查詢在邊角案例中的 KeyNotFoundException。
* 修正在 LINQ 查詢的 select 子句中，JsonProperty 屬性未被接受的錯誤。

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* 修正使用工作階段一致性層級時，會在特定競爭條件下觸發，並導致間歇「Microsoft.Azure.Documents.NotFoundException:The read session is not available for the input session token」錯誤的錯誤 (bug)。

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1

* 修正 FeedOptions.MaxItemCount = -1 擲回 System.ArithmeticException 的迴歸：頁面大小為負數。
* 在 QueryMetrics 加入了新的 ToString() 函式。
* 公開關於讀取收集的分割區統計資料。
* 在 ChangeFeedOptions 加入了 PartitionKey 屬性。
* 次要錯誤修正。

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
 
 * 新增能力以使用 DocumentCollection 上的 UniqueKeyPolicy 屬性，指定文件的唯一索引。
 * 修正自訂 JsonSerializer 設定針對一些查詢和預存程序執行不被接受的錯誤。

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
 
 * 在 API 參考文件、組件中的中繼資料資訊，以及 NuGet 封裝中，將商標從 Azure DocumentDB 變更為 Azure Cosmos DB。
 * 將以直接連線模式傳送之請求的回應之診斷資訊和延遲加以公開。 屬性名稱是 ResourceResponse 類別上的 RequestDiagnosticsString 與 RequestLatency。
 * 此 SDK 版本需要使用從 https://aka.ms/cosmosdb-emulator 下載之最新版本的 Azure Cosmos DB 模擬器。
 
### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* 新增的數個可靠性修正和改進。

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1 

* 與支援 [Microsoft.Azure.Graphs](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet) 有關的內部變更

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* 已新增將 PartitionKeyRangeId 做為 FeedOption 的支援，以供對特定分割區索引鍵範圍值限制查詢結果範圍。
* 已新增將 StartTime 做為 ChangeFeedOption 的支援，以開始尋找該時間之後的變更。

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   修正 JsonSerializable 類別中可能會造成堆疊溢位例外狀況的問題。

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   已新增對 [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet) 執行個體具現化時指定自訂 JsonSerializerSettings 的支援。

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   支援 .NET Standard 1.5 作為其中一個目標架構。

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   已修正當執行 Azure Cosmos DB 查詢時，受影響的 x64 機器不支援 SSE4 指令並且擲回 SEHException 的問題。

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   已新增對名為 ConsistentPrefix 的新一致性層級的支援。
*   已新增對個別資料分割之查詢計量的支援。
*   已新增對限制查詢之接續權杖大小的支援。
*   已新增對失敗要求進行更詳細追蹤的支援。
*   SDK 中已有一些效能改進。

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* 修正將在彙總查詢之 FeedOptions 中提供的 PartitionKey 值忽略的問題。
* 修正在執行移動途中跨資料分割 Order By 查詢期間，進行資料分割管理的透明處理時發生的問題。

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* 已修正在 ASP.NET 內容內部使用時會在某些非同步 API 中造成死結的問題。

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* 修正來讓 SDK 在某些情況下能夠更有彈性地進行自動容錯移轉。

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* 修正偶爾會造成「WebException:The remote name could not be resolved」的問題。
* 透過針對 ReadDocumentAsync API 新增多載，以新增直接讀取具類型文件的支援。

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* 添加了对聚合查询（COUNT、MIN、MAX、SUM 和 AVG）的 LINQ 支持。
* 針對使用事件處理常式所造成的 ConnectionPolicy 物件，修正記憶體流失問題。
* 修正使用 ETag 時 UpsertAttachmentAsync 無法運作的問題。
* 修正根據字串欄位排序時交叉資料分割排序依據查詢接續無法運作的問題。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* 新增彙總查詢的支援 (COUNT、MIN、MAX、SUM 和 AVG)。 請參閱[彙總支援](how-to-sql-query.md#Aggregates)。
* 已將分割區集合的最小輸送量從 10,100 RU/s 降低為 2500 RU/s。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

Azure Cosmos DB .NET Core SDK 可讓您建置快速、跨平台的 [ASP.NET Core](https://www.asp.net/core) 和 [.NET Core](https://www.microsoft.com/net/core#windows) 應用程式，以在 Windows、Mac 和 Linux 上執行。 最新版 Azure Cosmos DB .NET Core SDK 與 [Xamarin](https://www.xamarin.com) 完全相容，可用來建置以 iOS、Android 及 Mono (Linux) 為目標的應用程式。  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview

Azure Cosmos DB .NET Core Preview SDK 可讓您建置快速、跨平台的 [ASP.NET Core](https://www.asp.net/core) 和 [.NET Core](https://www.microsoft.com/net/core#windows) 應用程式，以在 Windows、Mac 和 Linux 上執行。

Azure Cosmos DB .NET Core Preview SDK 有與最新版本 [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet.md) 的功能類似的功能，並且支援下列項目︰
* 所有[連接模式](performance-tips.md#networking)：閘道器模式、直接 TCP 和 Direct HTTPs。
* 所有[一致性層級](consistency-levels.md)：強式、工作階段，限定過期和最終。
* [資料分割的集合](partition-data.md)。
* [多重區域資料庫帳戶和異地複寫](distribute-data-globally.md)。

如果您有與此 SDK 相關的問題，請張貼至 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-documentdb)，或是在 [GitHub 儲存機制](https://github.com/Azure/azure-documentdb-dotnet/issues)中提出問題。

## <a name="release--retirement-dates"></a>發行和停用日期

| Version | 發行日期 | 停用日期 |
| --- | --- | --- |
| [2.2.3](#2.2.3) |2019 年 3 月 11日日 |--- |
| [2.2.2](#2.2.2) |2019 年 2 月 6日日 |--- |
| [2.2.1](#2.2.1) |2018 年 12 月 24 日 |--- |
| [2.2.0](#2.2.0) |2018 年 12 月 7 日 |--- |
| [2.1.3](#2.1.3) |2018 年 10 月 15 日 |--- |
| [2.1.2](#2.1.2) |2018 年 10 月 4 日 |--- |
| [2.1.1](#2.1.1) |2018 年 9 月 27 日 |--- |
| [2.1.0](#2.1.0) |2018 年 9 月 21 日 |--- |
| [2.0.0](#2.0.0) |2018 年 9 月 7 日 |--- |
| [1.9.1](#1.9.1) |2018 年 3 月 9 日 |--- |
| [1.8.2](#1.8.2) |2018 年 2 月 21 日 |--- |
| [1.8.1](#1.8.1) |2018 年 2 月 5 日 |--- |
| [1.7.1](#1.7.1) |2017 年 11 月 16 日 |--- |
| [1.7.0](#1.7.0) |2017 年 11 月 10 日 |--- |
| [1.6.0](#1.6.0) |2017 年 10 月 17 日 |--- |
| [1.5.1](#1.5.1) |2017 年 10 月 2 日 |--- |
| [1.5.0](#1.5.0) |2017 年 8 月 10 日 |--- | 
| [1.4.1](#1.4.1) |2017 年 8 月 7 日 |--- |
| [1.4.0](#1.4.0) |2017 年 8 月 2 日 |--- |
| [1.3.2](#1.3.2) |2017 年 6 月 12 日 |--- |
| [1.3.1](#1.3.1) |2017 年 5 月 23 日 |--- |
| [1.3.0](#1.3.0) |2017 年 5 月 10 日 |--- |
| [1.2.2](#1.2.2) |2017 年 4 月 19 日 |--- |
| [1.2.1](#1.2.1) |2017 年 3 月 29 日 |--- |
| [1.2.0](#1.2.0) |2017 年 3 月 25 日 |--- |
| [1.1.2](#1.1.2) |2017 年 3 月 20 日 |--- |
| [1.1.1](#1.1.1) |2017 年 3 月 14 日 |--- |
| [1.1.0](#1.1.0) |2017 年 2 月 16 日 |--- |
| [1.0.0](#1.0.0) |2016 年 12 月 21 日 |--- |
| [0.1.0-preview](#0.1.0-preview) |2016 年 11 月 15 日 |2016 年 12 月 31 日 |

## <a name="see-also"></a>另请参阅
若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。

