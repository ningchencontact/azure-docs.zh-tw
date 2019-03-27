---
title: 在 C# 的 Azure 搜尋服務索引 (.NET SDK) 中查詢資料 - Azure 搜尋服務
description: 可在 Azure 搜尋服務中建置搜尋查詢的 C# 程式碼範例。 新增搜尋參數以篩選和排序搜尋結果。
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: 6bb170a5f3353288ab9c393e01b7a0902361913b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287004"
---
# <a name="quickstart-3---query-an-azure-search-index-in-c"></a>快速入門：3 - 在 C# 的 Azure 搜尋服務索引中查詢

本文說明如何使用 C# 和 [.NET SDK](https://aka.ms/search-sdk)查詢 [Azure 搜尋服務索引](search-what-is-an-index.md)。 在您的索引中搜尋文件是透過執行以下工作來完成的：

> [!div class="checklist"]
> * 建立 [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) 物件，以唯讀權限連線至搜尋索引。
> * 建立包含搜尋或篩選定義的 [`SearchParameters` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet)物件。
> * 呼叫 `SearchIndexClient`上的 `Documents.Search` 方法，將查詢傳送到索引。

## <a name="prerequisites"></a>必要條件

使用 hotels 範例資料[載入 Azure 搜尋服務索引](search-import-data-dotnet.md)。

取得用來對文件進行唯讀存取的查詢金鑰。 到目前為止，您已使用管理 API 金鑰讓您可在搜尋服務上建立物件和內容。 但是針對應用程式中的查詢支援，我們強烈建議使用查詢金鑰。 如需相關指示，請參閱[建立查詢金鑰](search-security-api-keys.md#create-query-keys)。

## <a name="create-a-client"></a>建立用戶端
建立 `SearchIndexClient` 類別的執行個體，讓您可以賦予它唯讀存取權 (而不是上一課中使用的 `SearchServiceClient` 賦予的寫入存取權限)。

這個類別有數個建構函式。 您想要的那一個會將您的搜尋服務名稱、索引名稱和 [`SearchCredentials`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials?view=azure-dotnet) 物件當做參數。 `SearchCredentials` 會包裝您的 API 金鑰。

下列程式碼會使用搜尋服務名稱的值，以及儲存於應用程式組態檔中的 API 金鑰 (在[範例應用程式](https://aka.ms/search-dotnet-howto)的情況下為 `appsettings.json`)，來為 "hotels" 索引建立新的 `SearchIndexClient`：

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

`SearchIndexClient` 具有 [`Documents`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient.documents?view=azure-dotnet) 屬性。 此屬性會提供您查詢 Azure 搜尋服務索引所需的所有方法。

## <a name="construct-searchparameters"></a>建構 SearchParameters
使用 .NET SDK 進行搜尋就和在您的 `SearchIndexClient` 呼叫 `Documents.Search` 方法一樣簡單。 此方法會採用一些參數，包括搜尋文字，以及可進一步縮小查詢範圍的 `SearchParameters` 物件。

### <a name="types-of-queries"></a>查詢類型
您將會使用的兩個主要[查詢類型](search-query-overview.md#types-of-queries)是 `search` 和 `filter`。 `search` 查詢會搜尋索引中所有可搜尋欄位的一或多個字詞。 `filter` 查詢可跨索引的所有可篩選欄位評估布林運算式。 您可以同時或個別使用搜尋和篩選。

搜尋和篩選均使用 `Documents.Search` 方法執行。 搜尋查詢可在 `searchText` 參數中傳遞，而篩選運算式可在 `SearchParameters` 類別的 `Filter` 屬性中傳遞。 若要篩選而不進行搜尋，只要為 `searchText` 參數傳遞 `"*"` 即可。 若要在不進行篩選的情況下搜尋，則只要將 `Filter` 屬性保留在未設定狀態，或完全不要傳入 `SearchParameters` 執行個體。

### <a name="example-queries"></a>查詢範例
下列範例程式碼示範幾個不同方式，來查詢 [在 C# 中建立 Azure 搜尋服務索引](search-create-index-dotnet.md#DefineIndex)中定義的 "hotels" 索引。 請注意，隨著搜尋結果傳回的文件是 `Hotel` 類別的執行個體，其定義於 [將資料匯入 C# 中的 Azure 搜尋服務索引](search-import-data-dotnet.md#construct-indexbatch)。 範例程式碼運用 `WriteDocuments` 方法將搜尋結果輸出到主控台。 下一節將說明此方法。

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="handle-search-results"></a>處理搜尋結果
`Documents.Search` 方法會傳回包含查詢結果的 `DocumentSearchResult` 物件。 前一章節中的範例使用稱為 `WriteDocuments` 的方法將搜尋結果輸出到主控台：

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

以下是前一節查詢結果的可能樣貌，此處假設 "hotels" 索引是以範例資料填入：

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577
```

上方的範例程式碼使用主控台來輸出搜尋結果。 您同樣需要在自己的應用程式中顯示搜尋結果。 如需範例以了解如何在 ASP.NET MVC 架構的 Web 應用程式中轉譯搜尋結果，請參閱 GitHub 上的 [DotNetSample 專案](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample)。

## <a name="next-steps"></a>後續步驟

如果您尚未這麼做，請檢閱 GitHub 上的 [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) 中的範例程式碼，以及[如何從 .NET 應用程式使用 Azure 搜尋服務](search-howto-dotnet-sdk.md)以取得範例程式碼的更詳細描述。 