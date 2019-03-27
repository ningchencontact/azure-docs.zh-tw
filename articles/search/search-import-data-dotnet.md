---
title: 在 C# 中將資料載入 Azure 搜尋服務索引 (.NET SDK) - Azure 搜尋服務
description: 了解如何使用 C# 範例程式碼與 .NET SDK 將資料上傳至 Azure 搜尋服務中全文檢索的可搜尋索引。
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: d2d54d1425bbb67a3f5ba1b6081a9f74ff87f4d6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286902"
---
# <a name="quickstart-2---load-data-to-an-azure-search-index-using-c"></a>快速入門：2 - 使用 C# 將資料載入 Azure 搜尋服務索引

本文說明如何使用 C# 和 [.NET SDK](https://aka.ms/search-sdk)，將資料匯入至 [Azure 搜尋服務索引](search-what-is-an-index.md)。 將文件推送到索引中是透過執行以下工作來完成的：

> [!div class="checklist"]
> * 建立 [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) 物件以連接到搜尋服務索引。
> * 建立 [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 物件，其中包含要新增、修改或刪除的文件。
> * 在 `SearchIndexClient` 上呼叫 `Documents.Index` 方法，以便將文件上傳至索引。

## <a name="prerequisites"></a>必要條件

[建立 Azure 搜尋服務索引](search-create-index-dotnet.md)及 `SearchServiceClient` 物件，如[「建立用戶端」](search-create-index-dotnet.md#CreateSearchServiceClient)中所示。


## <a name="create-a-client"></a>建立用戶端
若要匯入資料，您需要 `SearchIndexClient` 類別的執行個體。 建立這個類別有好幾種方法，包括使用已建立的 `SearchServiceClient` 執行個體。 

如下列範例所示，您可以使用 `SearchServiceClient` 執行個體並呼叫其 `Indexes.GetClient` 方法。 此程式碼片段會從名為 `serviceClient` 的 `SearchServiceClient`，針對名為 "hotels" 的索引，取得 `SearchIndexClient`。

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

`SearchIndexClient` 具有 `Documents` 屬性。 此屬性提供您新增、修改、刪除或查詢索引中文件所需的所有方法。

> [!NOTE]
> 在一般搜尋應用程式中，查詢和編製索引會經過個別處理。 雖然 `Indexes.GetClient` 很方便 (因為您可以重複使用物件，例如 `SearchCredentials`)，但是更強固的方法牽涉到直接建立 `SearchIndexClient`，讓您可以傳入查詢金鑰，而不是系統管理金鑰。 這種做法不但符合[最低權限的準則](https://en.wikipedia.org/wiki/Principle_of_least_privilege)，也有助於讓您的應用程式更安全。 您將在下一個練習中建構 `SearchIndexClient`。 如需有關金鑰的詳細資訊，請參閱[針對 Azure 搜尋服務建立及管理 API 金鑰](search-security-api-keys.md)。
> 
> 

<a name="construct-indexbatch"></a>

## <a name="construct-indexbatch"></a>建構 IndexBatch

若要使用 .NET SDK 匯入資料，請將資料封裝到 `IndexBatch` 物件中。 `IndexBatch` 會封裝 `IndexAction` 物件集合，每個物件各包含一份文件和一個屬性，後者會告知 Azure 搜尋服務要對該文件執行什麼動作 (上傳、合併、刪除和 mergeOrUpload)。 如需有關編製動作索引的詳細資訊，請參閱[編製動作索引：上傳、合併、mergeOrUpload、刪除](search-what-is-data-import.md#indexing-actions)。

假設您知道可對文件執行哪些動作，並準備好建構 `IndexBatch`。 下方範例示範如何使用不同動作建立批次。 此範例使用稱為 `Hotel` 的自訂類別，其對應到 "hotels" 索引中的文件。

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

在此案例中，我們使用了 `Upload`、`MergeOrUpload`和 `Delete` 作為搜尋動作，如同對 `IndexAction` 類別呼叫的方法所指定。

假設此 "hotels" 索引範例已填入幾份文件。 請留意我們在使用 `MergeOrUpload` 時是如何不必指定所有可能的文件欄位，以及在使用 `Delete` 時如何僅指定文件索引鍵 (`HotelId`)。

另請注意，每個索引編製要求中最多只能包含 1000 份文件。

> [!NOTE]
> 在此範例中，我們在不同文件中套用了不同動作。 如果您要在批次中所有文件執行相同動作，而不要呼叫 `IndexBatch.New`，則可以使用 `IndexBatch` 的其他靜態方法。 例如，您可以藉由呼叫 `IndexBatch.Merge`、`IndexBatch.MergeOrUpload` 或 `IndexBatch.Delete` 來建立批次。 這些方法會採用文件 (在此範例中為類型 `Hotel` 的物件) 集合，而非 `IndexAction` 物件。
> 
> 

## <a name="call-documentsindex"></a>呼叫 Documents.Index
您現在已將 `IndexBatch` 物件初始化，便可在 `SearchIndexClient` 物件上呼叫 `Documents.Index`，將其傳送到索引。 下列範例示範如何呼叫 `Index`，以及您必須執行的幾個額外步驟：

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

請留意對 `Index` 方法的呼叫前後的 `try`/`catch`。 Catch 區塊會處理索引編製的重大錯誤案例。 如果您的 Azure Search 服務無法將 Batch 中的一些文件編制索引，則 `Documents.Index` 會擲回 `IndexBatchException`。 如果您在服務負載過重時編制文件的索引，就會發生此情況。 **我們強烈建議您在程式碼中明確處理此情況。**  您可以延遲，然後重新嘗試將失敗的文件編制索引，或像範例一樣加以記錄並繼續，或是根據您應用程式的資料一致性需求執行其他操作。

最後，上方範例中的程式碼會延遲兩秒。 您的 Azure 搜尋服務中會發生非同步索引編製，因此範例應用程式必須稍待一會，才能確定文件已準備好可供搜尋。 通常只有在示範、測試和範例應用程式中，才需要這類延遲。

如需有關文件處理的詳細資訊，請參閱[「.NET SDK 如何處理文件」](search-howto-dotnet-sdk.md#how-dotnet-handles-documents)。


## <a name="next-steps"></a>後續步驟
在填入 Azure 搜尋服務索引後，下一步是發出查詢來搜尋文件。 

> [!div class="nextstepaction"]
> [在 C# 中查詢 Azure 搜尋服務索引](search-query-dotnet.md)
