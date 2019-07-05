---
title: 如何從 .NET 應用程式使用 Azure 搜尋服務 - Azure 搜尋服務
description: 了解如何在使用 C# 和 .NET SDK 的 .NET 應用程式中，使用 Azure 搜尋服務。 以程式碼為基礎的工作包括連線至服務、為內容編製索引，以及查詢索引。
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: brjohnst
ms.openlocfilehash: 9f0af40d442747181636b50612f7d2162ead6a86
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450022"
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>如何從 .NET 應用程式使用 Azure 搜尋服務

本文會逐步指引您學會如何使用 [Azure 搜尋服務 .NET SDK](https://aka.ms/search-sdk)。 您可以透過 .NET SDK，利用 Azure 搜尋服務在應用程式中實作豐富的搜尋經驗。

## <a name="whats-in-the-azure-search-sdk"></a>Azure 搜尋服務 SDK 中有哪些內容
SDK 包含數個用戶端程式庫，可讓您管理索引、資料來源、索引子及同義字地圖，以及上傳和管理文件，還可以執行查詢，而且一律不需要處理 HTTP 和 JSON 的細節。 這些用戶端程式庫都以 NuGet 套件來散發。

主要的 NuGet 套件是 `Microsoft.Azure.Search`，這是包含所有其他相依套件的中繼套件。 如果您剛開始使用，或知道應用程式將需要 Azure 搜尋服務的所有功能，則請使用此套件。

SDK 中還有其他 NuGet 套件：
 
  - `Microsoft.Azure.Search.Data`:如果您正在開發使用 Azure 搜尋服務的 .NET 應用程式，而且只需要查詢或更新索引中的文件，則請使用此套件。 如果您也需要建立或更新索引、同義字地圖或其他服務層級資源，請改用 `Microsoft.Azure.Search` 套件。
  - `Microsoft.Azure.Search.Service`:如果您正在開發 .NET 自動化來管理 Azure 搜尋服務的索引、同義字地圖、索引子、資料來源或其他服務層級資源，請使用此套件。 如果您只需要查詢或更新索引中的文件，請改用 `Microsoft.Azure.Search.Data` 套件。 如果您需要 Azure 搜尋服務的所有功能，請改用 `Microsoft.Azure.Search` 套件。
  - `Microsoft.Azure.Search.Common`:Azure 搜尋服務 .NET 程式庫所需的常用類型。 您不需要直接在您的應用程式中使用此套件。 它只是要用作相依性。

各種用戶端程式庫會定義類別，例如 `Index`、`Field` 及 `Document`，以及定義作業，例如 `SearchServiceClient` 和 `SearchIndexClient` 類別上的 `Indexes.Create` 和 `Documents.Search`。 這些類別可編成以下命名空間：

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

如果您想要針對未來的更新的 sdk 提供意見反應，請參閱我們[意見反應頁面](https://feedback.azure.com/forums/263029-azure-search/)或在建立問題[GitHub](https://github.com/azure/azure-sdk-for-net/issues)和提到問題標題中的 「 Azure 搜尋服務 」。

.NET SDK 支援 `2019-05-06` 版的 [Azure 搜尋服務 REST API](https://docs.microsoft.com/rest/api/searchservice/)。 此版本包含對[複雜型別](search-howto-complex-data-types.md)，[認知搜尋](cognitive-search-concept-intro.md)，[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete)，以及[JsonLines 剖析模式](search-howto-index-json-blobs.md)時Azure Blob 編製索引。 

此 SDK 不支援[管理作業](https://docs.microsoft.com/rest/api/searchmanagement/)，例如建立及調整搜尋服務和管理 API 金鑰。 如果您需要從 .NET 應用程式管理搜尋資源，您可以使用 [Azure 搜尋服務 .NET 管理 SDK](https://aka.ms/search-mgmt-sdk)。

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>升級到最新版本的 SDK
如果您已經使用較舊版本的 Azure 搜尋服務.NET SDK，而且您想要升級至最新正式推出的版本[這篇文章](search-dotnet-sdk-migration-version-9.md)說明如何。

## <a name="requirements-for-the-sdk"></a>SDK 的需求
1. Visual Studio 2017 或更新版本。
2. 擁有 Azure Search 服務。 為了使用 SDK，您需要為服務命名，還需要一或多個 API 金鑰。 [在入口網站建立服務](search-create-service-portal.md) 可協助您執行這些步驟。
3. 使用 Visual Studio 中的 [管理 NuGet 封裝] 下載 Azure 搜尋服務 .NET SDK [NuGet 封裝](https://www.nuget.org/packages/Microsoft.Azure.Search) 。 只要在 NuGet.org 搜尋套件名稱 `Microsoft.Azure.Search` (或者，如果您只需要某個功能子集，則搜尋上述其中一個其他套件名稱)。

Azure 搜尋服務.NET SDK 支援以.NET Framework 4.5.2 為目標的應用程式和更新版本，以及.NET Core 2.0 和更新版本。

## <a name="core-scenarios"></a>核心案例
您必須在搜尋應用程式中執行一些作業。 本教學課程中涵蓋了這些主要情節：

* 建立索引
* 透過文件填入索引
* 使用全文檢索搜尋和篩選來搜尋文件

下列範例程式碼說明每個案例。 歡迎在您的應用程式中使用這些程式碼片段。

### <a name="overview"></a>概觀
我們將探索的範例應用程式，會建立名為 "hotels" 的新索引，並透過一些文件填入索引，然後執行一些搜尋查詢。 以下是主要程式，該程式顯示了整體流程：

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> 您可以在 [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) 上尋找本逐步解說中所用範例應用程式的完整原始程式碼。
> 
>

我們會逐步說明， 首先必須建立新的 `SearchServiceClient`。 此物件可讓您管理索引。 若要建構此物件，必須提供 Azure Search 服務名稱和系統管理 API 金鑰。 您可以在[範例應用程式](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)的 `appsettings.json` 檔案中輸入這項資訊。

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> 如果提供不正確的金鑰 (例如，需要系統管理金鑰卻提供查詢金鑰)，則 `SearchServiceClient` 會在您第一次用它呼叫作業方法時 (例如 `Indexes.Create`) 擲回 `CloudException`，並附上「禁止」的錯誤訊息。 如果遇到此情況，請按兩下我們的 API 金鑰。
> 
> 

以下幾行程式碼會呼叫建立名為 "hotels" 索引的方法，如果索引已存在，請加以刪除。 稍後我們會逐項執行這些方法。

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

接著必須填入索引。 若要執行填入索引，我們需要`SearchIndexClient`。 取得的方式有兩種：建構該項目，或用 `SearchServiceClient` 呼叫 `Indexes.GetClient`。 為方便起見，我們使用後者。

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> 在一般搜尋應用程式中，索引管理和母體擴展可能會處理搜尋查詢中的個別元件。 `Indexes.GetClient` 很輕易填入索引，因為它可以節省您許多麻煩，提供其他`SearchCredentials`。 執行方法是將用於建立 `SearchServiceClient` 的系統管理金鑰傳遞至新的 `SearchIndexClient`。 不過，在您執行查詢的應用程式的一部分，最好是建立`SearchIndexClient`直接，讓您可以傳遞查詢金鑰，只允許您讀取資料，而不是系統管理金鑰。 這不但符合最低權限的準則，也可以讓您的應用程式更安全。 您可以 [在這裡](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization)深入了解系統管理金鑰和查詢金鑰。
> 
> 

現在我們有 `SearchIndexClient`，可以開始填入索引。 索引母體擴展，我們將逐步解說稍後的另一種方法即可。

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

最後，我們會執行一些搜尋查詢並顯示結果。 這次我們使用不同的 `SearchIndexClient`：

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

我們稍後會仔細查看 `RunQueries` 方法。 以下是可建立新 `SearchIndexClient` 的程式碼：

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

這次我們使用查詢金鑰，因為我們不需要索引的寫入存取權。 您可以在[範例應用程式](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)的 `appsettings.json` 檔案中輸入這項資訊。

如果您執行此應用程式具有有效的服務名稱和 API 金鑰時，輸出看起來應該如下列範例：（某些主控台輸出已取代為 [...]供說明之用。）

    Deleting index...

    Creating index...

    Uploading documents...

    Waiting for documents to be indexed...

    Search the entire index for the term 'motel' and return only the HotelName field:

    Name: Secret Point Motel

    Name: Twin Dome Motel


    Apply a filter to the index to find hotels with a room cheaper than $100 per night, and return the hotelId and description:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Times Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.

    HotelId: 2
    Description: The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.


    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

    Name: Triple Landscape Hotel
    Last renovated on: 9/20/2015 12:00:00 AM +00:00

    Name: Twin Dome Motel
    Last renovated on: 2/18/1979 12:00:00 AM +00:00


    Search the hotel names for the term 'hotel':

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

    Complete.  Press any key to end application... 

本文結尾會提供完整的應用程式原始程式碼。

接著，我們要進一步了解 `Main`所呼叫的每個方法。

### <a name="creating-an-index"></a>建立索引
在建立後`SearchServiceClient`，`Main`刪除"hotels"索引，如果已經存在。 該刪除是由下列方法：

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

此方法使用指定的 `SearchServiceClient` 查看索引是否存在，如果存在，就加以刪除。

> [!NOTE]
> 為簡單起見，本文的範例程式碼使用 Azure 搜尋服務 .NET SDK 的同步方法。 我們建議您在應用程式中使用非同步方法，讓應用程式保有可擴充性且回應靈敏。 例如，您可以在以上方法中使用 `ExistsAsync` 和 `DeleteAsync`，而非 `Exists` 和 `Delete`。
> 
> 

接著，`Main` 會呼叫此方法建立新的 "hotels" 索引：

```csharp
private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    
    serviceClient.Indexes.Create(definition);
}
```

此方法會以定義新索引結構描述的 `Field` 物件清單，建立新的 `Index` 物件。 每個欄位均有一個名稱、資料類型和一些屬性，以用於定義欄位的搜尋行為。 `FieldBuilder` 類別會檢查指定 `Hotel` 模型類別的公用屬性 (property) 和屬性 (attribute)，進而使用反映來建立索引的 `Field` 物件清單。 我們稍後會仔細查看 `Hotel` 類別。

> [!NOTE]
> 如有需要，您永遠可以直接建立 `Field` 物件清單，而不是使用`FieldBuilder`。 例如，您可能不想使用模型類別，或您可能需要使用不想藉由新增屬性來修改的現有模型類別。
>
> 

除了欄位之外，您也可以加入評分設定檔、 建議工具或 CORS 選項 （為求簡單明瞭的範例會省略這些參數） 的索引。 如需 Index 物件以及其組成部分的詳細資訊，請參閱 [SDK 參考](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)和 [Azure 搜尋服務 REST API 參考](https://docs.microsoft.com/rest/api/searchservice/)。

### <a name="populating-the-index"></a>填入索引
下一步`Main`填入新建立的索引。 這個索引母體擴展完成方法如下：（某些程式碼以取代 [...]為了方便說明。  請參閱完整的資料填入程式碼的完整範例解決方案）。

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        {
            HotelId = "1",
            HotelName = "Secret Point Motel",
            ...
            Address = new Address()
            {
                StreetAddress = "677 5th Ave",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Budget Room, 1 Queen Bed (Cityside)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (City View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "2",
            HotelName = "Twin Dome Motel",
            ...
            {
                StreetAddress = "140 University Town Center Dr",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Suite, 2 Double Beds (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Standard Room, 1 Queen Bed (City View)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Waterfront View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "3",
            HotelName = "Triple Landscape Hotel",
            ...
            Address = new Address()
            {
                StreetAddress = "3393 Peachtree Rd",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Standard Room, 2 Queen Beds (Amenities)",
                    ...
                },
                new Room ()
                {
                    Description = "Standard Room, 2 Double Beds (Waterfront View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (Cityside)",
                    ...
                }
            }
        }
    };

    var batch = IndexBatch.Upload(hotels);

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
}
```

此方法分四個部分。 第一個建立的 3 陣列`Hotel`物件各有 3`Room`將做為輸入資料上傳至索引的物件。 為簡單起見，此資料採硬式編碼。 在您的應用程式中，資料可能會來自像是 SQL 資料庫的外部資料來源。

第二個部分會建立包含文件的 `IndexBatch` 。 您在建立批次時 (在此案例中，是藉由呼叫 `IndexBatch.Upload`)，指定要套用至該批次的作業。 接著以 `Documents.Index` 方法將該 Batch 上傳至 Azure 搜尋服務索引。

> [!NOTE]
> 在此範例中，我們只上傳文件。 如果您想要將變更合併至現有的文件，或是刪除文件，您可以改為呼叫 `IndexBatch.Merge`、`IndexBatch.MergeOrUpload` 或 `IndexBatch.Delete` 來建立批次。 您也可以在單一批次中混合不同的作業，方法是呼叫 `IndexBatch.New`，它會採用一組 `IndexAction` 物件，其中每個物件都會要求 Azure 搜尋服務針對文件執行特定的作業。 您可以建立每個擁有自己作業的 `IndexAction`，方法是呼叫對應的方法，例如 `IndexAction.Merge`、`IndexAction.Upload` 等等。
> 
> 

此方法的第三部分是擷取區塊，該區塊會為編制索引處理重要錯誤情況。 如果您的 Azure Search 服務無法將 Batch 中的一些文件編制索引，則 `Documents.Index` 會擲回 `IndexBatchException`。 如果您的服務負載過重時，您會建立索引的文件，可能會發生這個例外狀況。 **我們強烈建議您在程式碼中明確處理此情況。** 您可以延遲，然後重新嘗試將失敗的文件編制索引，或像範例一樣加以記錄並繼續，或是根據您應用程式的資料一致性需求執行其他操作。

> [!NOTE]
> 您可以使用 [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) 方法來建構新的批次，該批次僅包含先前呼叫 `Index` 時失敗的動作。 [StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry) 上有關於如何適當地使用此方法的討論。
>
>

最後，`UploadDocuments` 方法會延遲兩秒。 您的 Azure 搜尋服務中會發生非同步索引編製，因此範例應用程式必須稍待一會，才能確定文件已準備好可供搜尋。 通常只有在示範、測試和範例應用程式中，才需要這類延遲。

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>.NET SDK 如何處理文件
您可能想知道 Azure 搜尋服務 .NET SDK 如何能夠將使用者定義的類別執行個體 (例如 `Hotel` 上傳至索引。 為了回答這問題，我們來看一下 `Hotel` 類別：

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsSearchable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.EnLucene)]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("Description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    // SmokingAllowed reflects whether any room in the hotel allows smoking.
    // The JsonIgnore attribute indicates that a field should not be created 
    // in the index for this property and it will only be used by code in the client.
    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? Rating { get; set; }

    public Address Address { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    public Room[] Rooms { get; set; }
}
```

要注意的第一件事是在每個公用屬性名稱`Hotel`類別會對應至具有相同的名稱，在索引定義中的欄位。 如果您想要開頭為小寫字母 （「 駝峰式大小寫 」） 的每個欄位，您所見到的屬性名稱對應至駝峰式自動與 SDK`[SerializePropertyNamesAsCamelCase]`類別上的屬性。 此案例中很常見執行資料繫結的目標結構描述而不必違反 「 巴斯卡"命名指導方針，在.NET 中的所在的應用程式開發人員控制之外的.NET 應用程式。

> [!NOTE]
> Azure 搜尋服務 .NET SDK 使用 [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) 程式庫來將您的自訂模型物件序列化到 JSON 中，以及將您在 JSON 中的自訂模型物件還原序列化。 如有需要，您可以自訂這個序列化的過程。 如需詳細資訊，請參閱 <<c0> [ 使用 JSON.NET 自訂序列化](#JsonDotNet)。
> 
> 

要注意的第二件事是每個屬性這類屬性裝飾`IsFilterable`， `IsSearchable`， `Key`，和`Analyzer`。 這些屬性直接對應至[中的 Azure 搜尋服務索引的對應欄位屬性](https://docs.microsoft.com/rest/api/searchservice/create-index#request)。 `FieldBuilder`類別來建構索引的欄位定義會使用這些屬性。

第三個要注意的是`Hotel`類別是資料類型的公用屬性。 這些屬性的 .NET 類型會對應至索引定義中，與其相當的欄位類型。 例如，`Category` 字串屬性會對應至 `category` 欄位 (此欄位屬於 `Edm.String` 類型)。 有類似的類型對應之間`bool?`， `Edm.Boolean`， `DateTimeOffset?`，和`Edm.DateTimeOffset`，依此類推。 類型對應的特定規則和 `Documents.Get` 方法已一起記載於 [Azure 搜尋服務 .NET SDK 參考](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get)。 `FieldBuilder` 類別會為您處理這項對應，但它仍有助您了解您需要對任何序列化問題進行疑難排解的情況。

您會注意到`SmokingAllowed`屬性嗎？

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

`JsonIgnore`在這個屬性的屬性會告知`FieldBuilder`不將它序列化為欄位的索引。  這是適合用來建立用戶端您可以使用協助程式為您的應用程式中的導出的屬性。  在此情況下，`SmokingAllowed`屬性會反映出是否有任何`Room`在`Rooms`集合允許吸煙。  如果所有都是 false，則表示整個旅館不允許吸煙。

某些屬性，例如`Address`和`Rooms`是.NET 類別的執行個體。  這些屬性會代表更複雜的資料結構，而且如此一來，需要使用的欄位[複雜資料型別](https://docs.microsoft.com/azure/search/search-howto-complex-data-types)索引中。

`Address`屬性表示一組中的多個值`Address`類別，定義如下：

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Address
    {
        [IsSearchable]
        public string StreetAddress { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string City { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string StateProvince { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string PostalCode { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Country { get; set; }
    }
}
```

這個類別包含用來描述在美國或加拿大地區的地址的標準值。 若要在索引中邏輯的欄位群組在一起，您可以使用像這樣的型別。

`Rooms`屬性代表陣列的`Room`物件：

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Room
    {
        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string Type { get; set; }

        [IsFilterable, IsFacetable]
        public double? BaseRate { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string BedOptions { get; set; }

        [IsFilterable, IsFacetable]
        public int SleepsCount { get; set; }

        [IsFilterable, IsFacetable]
        public bool? SmokingAllowed { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }
    }
}
```

應該設計您的資料模型，在.NET 和其對應的索引結構描述，以支援您想要提供給您的終端使用者的搜尋體驗。 在.NET 中，亦即文件在索引中，每個最上層物件會對應至搜尋結果，您會在您的使用者介面中呈現。 比方說，在飯店的搜尋應用程式終端使用者可能想要搜尋旅館名稱的旅館，功能或特定房間的特性。 我們將討論一些查詢範例有點更新版本。

這讓使用您自己的類別互動中索引的文件適用於雙向;您也可以擷取搜尋結果，並已自動還原序列化為您的選擇，一種 SDK，我們會看到下一節。

> [!NOTE]
> Azure 搜尋服務 .NET SDK 還支援使用 `Document` 類別的動態類型文件，也就是欄位名稱與欄位值的索引鍵/值對應。 當您在設計階段卻不知道索引的結構描述時，這很實用，否則要繫結到特定模型類別會很麻煩。 SDK 中所有處理文件的方法，都有可搭配 `Document` 類別使用的多載，以及使用泛型類型參數的強類型多載。 本教學課程的範例程式碼只使用了後者。 [`Document`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) 類別繼承自 `Dictionary<string, object>`。
> 
>

**為什麼您應該使用 Nullable 資料類型**

當您將自己的模型類別設計為可對應至 Azure 搜尋服務索引時，建議您將 `bool` 和 `int` 等的值類型屬性宣告為可為 Null (例如宣告為 `bool?`，而不是 `bool`)。 如果您使用不可為 Null 的屬性，則必須保證  索引中沒有任何文件的對應欄位包含 Null 值。 SDK 和 Azure 搜尋服務都不會協助您強制執行這項規定。

這不只是假設性的問題：如果您在類型為 `Edm.Int32` 的現有索引中新增欄位， 更新索引定義之後，所有文件對於該新的欄位具有 null 值 (因為所有類型在 Azure 搜尋服務中都可為 null)。 如果您接著對該欄位使用 `int` 屬性不可為 Null 的模型類別，就會在嘗試擷取文件時得到類似這樣的 `JsonSerializationException`：

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

因此，我們建議您在模型類別中使用可為 Null 的類型，來做為最佳作法。

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>使用 JSON.NET 自訂序列化
SDK 會使用 JSON.NET 序列化和還原序列化文件。 您可以自訂序列化和還原序列化視需要定義您自己`JsonConverter`或`IContractResolver`。 如需詳細資訊，請參閱 < [JSON.NET 文件](https://www.newtonsoft.com/json/help/html/Introduction.htm)。 當您想要調整您的應用程式的現有模型類別以搭配使用 Azure 搜尋服務以及其他更進階的案例時，這非常有用。 例如，使用自訂序列化，您可以：

* 包含或排除模型類別的特定屬性儲存為文件欄位。
* 在程式碼的屬性名稱和索引的欄位名稱之間進行對應。
* 建立可用來將屬性對應至文件欄位的自訂屬性。

您可以在 GitHub 上的 Azure 搜尋服務 .NET SDK 的單元測試中找到實作自訂序列化的範例。 [這個資料夾](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models)是好的起點。 它包含自訂序列化測試使用的類別。

### <a name="searching-for-documents-in-the-index"></a>搜尋索引中的文件
範例應用程式的最後一個步驟是要搜尋的索引中的一些文件：

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'motel' and return only the HotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "HotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels with a room cheaper than $100 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(r: r/BaseRate lt 100)",
            Select = new[] { "HotelId", "Description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "LastRenovationDate desc" },
            Select = new[] { "HotelName", "LastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'hotel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("hotel", parameters);

    WriteDocuments(results);
}
```

每次執行查詢時，這個方法都會先建立新的 `SearchParameters` 物件。 此物件用來指定其他選項，例如排序、 篩選、 分頁和多面向查詢。 在此方法中，我們會針對不同查詢設定 `Filter`、`Select`、`OrderBy` 和 `Top` 屬性。 所有 `SearchParameters` 屬性都記載於[這裡](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters)。

下一步是實際執行搜尋查詢， 執行搜尋是使用`Documents.Search`方法。 針對每次查詢，我們會傳遞搜尋文字做為字串 (如果沒有搜尋文字，則傳遞 `"*"`)，並再加上先前建立的搜尋參數。 我們也指定了 `Hotel` 做為 `Documents.Search` 的類型參數，藉此告訴 SDK 將搜尋結果中的文件還原序列化為 `Hotel` 類型的物件。

> [!NOTE]
> 如需搜尋查詢運算式語法的詳細資訊，請參閱 [這裡](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)。
> 
> 

最後，在每次查詢之後，此方法會反覆查詢搜尋結果中的所有相符項，然後將每個文件列印至主控台：

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

讓我們依序仔細查看每個查詢。 以下是執行第一個查詢的程式碼︰

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

在此案例中，我們要在任何可搜尋欄位中搜尋"motel"這個字的整個索引，而且我們只想要擷取所指定的旅館名稱`Select`參數。 結果如下︰

    Name: Secret Point Motel

    Name: Twin Dome Motel

下一個查詢是更有趣。  我們想要尋找的有每晚費率小於美金 100 元的空間，然後只傳回旅館識別碼並描述的所有旅館：

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)",
        Select = new[] { "HotelId", "Description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

此查詢會使用 OData `$filter` 運算式 (`Rooms/any(r: r/BaseRate lt 100)`)，以篩選索引中的文件。 這會使用[任何運算子](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators)套用 ' BaseRate lt 100' 聊天室集合中的每個項目。 您可以在 [這裡](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax)找到更多 Azure 搜尋服務支援的 OData 語法。

查詢的結果如下：

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

接下來，我們要尋找最近重新裝修的前兩間旅館，並顯示旅館名稱和上次重新裝修日期。 程式碼如下： 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "LastRenovationDate desc" },
        Select = new[] { "HotelName", "LastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

在此情況下，我們再次使用 OData 語法，將 `OrderBy` 參數指定為 `lastRenovationDate desc`。 我們也會將 `Top` 設定為 2，以確保只取得前兩份文件。 如同前面，我們會設定 `Select` 來指定應傳回哪些欄位。

結果如下︰

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

最後，我們想要尋找符合"hotel"這個字的所有旅館名稱：

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

結果如下，其中包含所有欄位，因為我們並未指定 `Select` 屬性︰

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

此步驟已完成本教學課程，但別就此結束。 \* * 下一個步驟會提供額外的資源，以深入了解 Azure 搜尋服務。

## <a name="next-steps"></a>後續步驟
* 瀏覽 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) 及 [REST API](https://docs.microsoft.com/rest/api/searchservice/) 參考文章。
* 檢閱 [命名規則](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) ，了解命名各種物件的規則。
* 檢閱 Azure 搜尋服務 [支援的資料類型](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) 。
