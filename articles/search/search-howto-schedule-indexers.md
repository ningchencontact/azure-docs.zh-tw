---
title: 如何排程索引子-Azure 搜尋服務
description: Azure 搜尋服務索引子編製內容以定期或排程在特定時間。
ms.date: 05/31/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 764fca8d3cb4cd9c40d7880043637f89ef1a8578
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755376"
---
# <a name="how-to-schedule-indexers-for-azure-search"></a>如何排程 Azure 搜尋服務索引子
索引子正常執行一次，只有在建立後立即。 您可以視需要執行一次使用入口網站、 REST API 或.NET SDK。 您也可以設定索引子以依排程定期執行。

某些情況下，其中索引子排程是非常有用：

* 來源資料會隨著時間變更，以及您想要 Azure 搜尋服務索引子，來自動處理變更的資料。
* 索引會填入多個資料來源，並且您想要確定在不同的時間，以減少衝突執行索引子。
* 來源資料是非常大，而且您想以分散一段時間處理的索引子。 如需編製索引大型資料磁碟區的詳細資訊，請參閱[如何在 Azure 搜尋服務中的大型資料集編製索引](search-howto-large-index.md)。

排程器是 Azure 搜尋服務的內建功能。 您無法使用外部排程器來控制搜尋服務索引子。

## <a name="define-schedule-properties"></a>定義排程屬性

索引子排程有兩個屬性：
* **間隔**，其定義之間的時間排程索引子執行。 允許的最小間隔為 5 分鐘，和最大為 24 小時。
* **開始時間 (UTC)** ，這表示索引子應執行的第一次。

首次建立索引子時，或藉由稍後更新索引子的屬性，您可以指定排程。 您可以使用設定索引子排程[入口網站](#portal)，則[REST API](#restApi)，或有[.NET SDK](#dotNetSdk)。

索引子一次僅能執行一個。 如果已在其下一步 執行排程時，會執行索引子，該執行延遲到下一個排定的時間。

讓我們參考一個範例，讓您更具體了解詳情。 假設我們設定與索引子排程**間隔**的每小時和**開始時間**的 2019 年 6 月 1 日的上午 8:00:00 UTC。 以下是將索引子執行時間超過一小時的時間時，可能發生什麼情況：

* 第一次索引子執行開始時間約於 2019 年 6 月 1 日的上午 8:00 UTC。 假設此執行需要 20 分鐘 (或任何小於 1 小時的時間)。
* 第二次執行開始時間約於 2019 年 6 月 1 日上午 9:00 UTC。 假設此執行需要 70 分鐘的時間-超過一小時，而它將不會完成直到上午 10:10 UTC。
* 第三次執行排定在上午 10:00 UTC 開始，但在該時間前一次的執行仍在執行中。 此排程然後略過執行。 11:00 AM UTC 直到將不會啟動執行下的一個索引子。

<a name="portal"></a>

## <a name="define-a-schedule-in-the-portal"></a>在入口網站中定義的排程

在入口網站中的匯入資料精靈可讓您在建立時定義索引子排程。 預設排程設定為**每小時**，這表示索引子執行一次之後，建立時，會執行一次每隔一小時之後。

您可以將排程設定變更為**一旦**如果您不想要自動重新執行的索引子，或者**每日**要每天執行一次。 將它設定為**自訂**如果您想要指定不同的間隔或特定的未來開始時間。

當您將排程設**自訂**，以讓您指定的欄位會出現**間隔**並**開始時間 (UTC)** 。 允許的間隔是 5 分鐘，且最長的時間最短為 1440 分鐘 （24 小時）。

   ![在匯入資料精靈 的設定索引子排程](media/search-howto-schedule-indexers/schedule-import-data.png "匯入資料精靈 中的設定索引子排程")

在建立索引子之後，您可以變更使用索引子的編輯面板的排程設定。 排程欄位會匯入資料精靈中相同。

   ![在索引子編輯面板中設定的排程](media/search-howto-schedule-indexers/schedule-edit.png "indexer 編輯面板中設定的排程")

<a name="restApi"></a>

## <a name="define-a-schedule-using-the-rest-api"></a>定義排程，使用 REST API

您可以定義使用 REST API 的索引子的排程。 若要這樣做，請包含**排程**時建立或更新索引子的屬性。 下列範例顯示更新現有的索引子的 PUT 要求：

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**間隔** 參數是必需的。 間隔指兩個連續索引子開始執行的時間。 允許的最小間隔為 5 分鐘；最長間隔為一天。 其必須格式化為 XSD "dayTimeDuration" 值 ( [ISO 8601 持續時間](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 值的受限子集)。 間隔的模式為： `P(nD)(T(nH)(nM))`。 範例：`PT15M` 代表每隔 15 分鐘，`PT2H` 代表每隔 2 個小時。

選擇性**startTime**指出何時應該開始的排定的執行數目。 如果省略選用時間，則會使用您目前的 UTC 時間。 此時間可以是在過去，如同已自原始持續執行索引子案例第一次執行已排程**startTime**。

您也可以使用執行索引子呼叫，隨時視需要執行索引子。 如需有關執行索引子，並將設定索引子排程的詳細資訊，請參閱 <<c0> [ 執行索引子](https://docs.microsoft.com/rest/api/searchservice/run-indexer)，[取得索引子](https://docs.microsoft.com/rest/api/searchservice/get-indexer)，並[更新索引子](https://docs.microsoft.com/rest/api/searchservice/update-indexer)REST API 參考中。

<a name="dotNetSdk"></a>

## <a name="define-a-schedule-using-the-net-sdk"></a>定義排程，使用.NET SDK

您可以定義索引子使用 Azure 搜尋服務.NET SDK 的排程。 若要這樣做，請包含**排程**時建立或更新索引子的屬性。

下列C#範例會建立索引子，使用預先定義的資料來源和索引，並設定其排程每天執行一次從現在開始 30 分鐘：

```
    Indexer indexer = new Indexer(
        name: "azure-sql-indexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        schedule: new IndexingSchedule(
                        TimeSpan.FromDays(1), 
                        new DateTimeOffset(DateTime.UtcNow.AddMinutes(30))
                    )
        );
    await searchService.Indexers.CreateOrUpdateAsync(indexer);
```
如果**排程**省略參數時，只有在建立後立即中索引子作業將只執行一次。

**StartTime**參數可以設定為過去的時間。 如同已因為持續執行索引子，在此情況下，排定第一次執行給定**startTime**。

使用定義排程[IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet)類別。 **IndexingSchedule**建構函式需要**間隔**使用指定的參數**TimeSpan**物件。 允許的最小間隔值為 5 分鐘，和最大為 24 小時。 第二個**startTime**指定為參數**DateTimeOffset**物件，是選擇性的。

.NET SDK 可讓您使用的控制項索引子作業[SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient)類別和其[的索引子](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers)屬性，它會實作方法，從**IIndexersOperations**介面。 

您可以使用其中一種，隨時視需要執行索引子[執行](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run)， [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)，或[RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync)方法。

如需有關如何建立的詳細資訊，請更新及執行索引子，請參閱[IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet)。
