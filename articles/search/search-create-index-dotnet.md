---
title: 以 C# 建立索引 - Azure 搜尋服務
description: 了解如何使用 Azure 搜尋服務 .NET SDK 以 C# 建立全文檢索的可搜尋索引。
author: heidisteen
manager: cgronlun
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/22/2019
ms.openlocfilehash: a5861faaf26962d34d1c356e29dce1be40f8716b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370579"
---
# <a name="quickstart-1---create-an-azure-search-index-in-c"></a>快速入門：1 - 在 C# 中建立 Azure 搜尋服務索引

本文將逐步引導您完成使用 C# 和 [.NET SDK](https://aka.ms/search-sdk) 建立 [Azure 搜尋服務索引](search-what-is-an-index.md)的程序。 這是建立、載入和查詢索引的 3 部分練習中的第 1 課。 索引建立是透過執行這些工作來完成的：

> [!div class="checklist"]
> * 建立 [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) 物件以連接到搜尋服務。
> * 建立 [`Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 物件以作為參數傳遞至 `Indexes.Create`。
> * 呼叫 `SearchServiceClient` 上的 `Indexes.Create` 方法，將 `Index` 傳送至服務。

## <a name="prerequisites"></a>必要條件

[建立 Azure 搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本快速入門的免費服務。

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)任何版本。 範例程式碼和指示已在免費的 Community 版本上經過測試。

取得您搜尋服務的 URL 端點和管理 API 金鑰。 搜尋服務是同時建立，因此如果您將 Azure 搜尋服務新增至您的訂用帳戶，請遵循下列步驟來取得必要的資訊：

  1. 在 Azure 入口網站中，您的搜尋服務 [概觀] 頁面上，取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

  2. 在 [設定]  >  [金鑰] 中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

  ![取得 HTTP 端點和存取金鑰](media/search-fiddler/get-url-key.png "取得 HTTP 端點和存取金鑰")

所有要求均都需要在傳送至您服務上的每個要求上使用 API 金鑰。 擁有有效的金鑰就能為每個要求在傳送要求之應用程式與處理要求之服務間建立信任。

## <a name="1---open-the-project"></a>1 - 開啟專案

從 GitHub 下載範例程式碼 [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)。 

在 appsettings.json 中，將預設內容取代為下列範例，然後提供您服務的服務名稱和管理 API 金鑰。 針對服務名稱，只需要名稱本身。 例如，如果您的 URL 是 https://mydemo.search.windows.net，請將 `mydemo` 新增至 JSON 檔案。


```json
{
    "SearchServiceName": "Put your search service name here",
    "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
}
```

設定好這些值之後，您可以 F5 建置用來執行主控台應用程式的解決方案。 本練習中的其餘步驟和後面的步驟將探討此程式碼的工作方式。 

或者，您也可以參考[如何從 .NET 應用程式使用 Azure 搜尋服務](search-howto-dotnet-sdk.md)，取得 SDK 行為的更詳細內容。 

<a name="CreateSearchServiceClient"></a>

## <a name="2---create-a-client"></a>2 - 建立用戶端

若要開始使用 Azure 搜尋服務 .NET SDK，請建立 `SearchServiceClient` 類別的執行個體。 這個類別有數個建構函式。 您所需的建構函式會取得您的搜尋服務名稱和 `SearchCredentials` 物件作為參數。 `SearchCredentials` 會包裝您的 API 金鑰。

下列程式碼可在 Program.cs 檔案中找到。 它會使用搜尋服務名稱的值，以及儲存於應用程式組態檔 (appsettings.json) 中的 API 金鑰，來建立新的 `SearchServiceClient`。

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

`SearchServiceClient` 具有 `Indexes` 屬性。 這個屬性會提供您需要用來建立、列出、更新或刪除 Azure 搜尋服務索引的所有方法。

> [!NOTE]
> `SearchServiceClient` 類別會管理連至搜尋服務的連線。 為了避免開啟太多連線，如果可以，您應該嘗試在應用程式中共用 `SearchServiceClient` 的單一執行個體。 它的方法是可啟用這類共用的安全執行緒。
> 
> 

<a name="DefineIndex"></a>

## <a name="3---construct-index"></a>3 - 建構索引
對 `Indexes.Create` 方法的單一呼叫會建立索引。 這個方法會將 `Index` 物件作為參數，來定義 Azure 搜尋服務索引。 建立 `Index` 物件並將它初始化，如下所示︰

1. 將 `Index` 物件的 `Name` 屬性設定為索引的名稱。

2. 將 `Index` 物件的 `Fields` 屬性設定為 `Field` 物件的陣列。 建立 `Field` 物件最簡單的方式是藉由呼叫 `FieldBuilder.BuildForType` 方法，傳遞類型參數的模型類別。 模型類別具有會對應至您索引欄位的屬性。 這可讓您將來自您搜尋索引的文件繫結至模型類別的執行個體。

> [!NOTE]
> 如果您不打算使用模型類別，仍然可以藉由直接建立 `Field` 物件來定義您的索引。 您可以為建構函式提供欄位的名稱，以及資料類型 (或字串欄位的分析器)。 您也可以設定其他屬性，例如 `IsSearchable`、`IsFilterable` 等等。
>
>

在設計索引時，請務必牢記搜尋服務使用者體驗和商務需求。 每個欄位必須指派[屬性](https://docs.microsoft.com/rest/api/searchservice/Create-Index)，屬性控制著哪些搜尋功能 (篩選、Facet、排序等等) 要套用至哪些欄位。 針對您未明確設定的任何屬性，除非您特別啟用，否則 `Field` 類別預設會停用對應的搜尋功能。

在此範例中，索引名稱為 "hotels"，而欄位是使用模型類別來定義的。 每個模型類別的屬性皆具有屬性，會判斷對應索引欄位的搜尋相關行為。 會定義模型類別，如下所示︰

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

我們已根據欄位在應用程式中的可能使用方式仔細選擇每個屬性的屬性。 例如，搜尋旅館的人很可能會對 `description` 欄位上的關鍵字相符項目感興趣，因此我們藉由將 `IsSearchable` 屬性新增至 `Description` 屬性來啟用該欄位的全文檢索搜尋。

請注意，`string` 類型的索引中必須有一個欄位 (且只有一個) 指定為 key 欄位，方法是新增 `Key`屬性 (請參閱上述範例中的 `HotelId`)。

`description_fr` 欄位會用來儲存法文文字，因此上述索引定義會對此欄位使用語言分析器。 如需詳細資訊，請參閱[將語言分析器新增至 Azure 搜尋服務索引](index-add-language-analyzers.md)。

> [!NOTE]
> 根據預設，模型類別中的每個屬性名稱會對應到索引中的欄位名稱。 如果您想要將所有屬性名稱對應至駝峰式欄位名稱，使用 `SerializePropertyNamesAsCamelCase` 屬性來標示類別。 如果您想要對應至不同的名稱，您可以使用 `JsonProperty` 屬性，類似上述 `DescriptionFr` 屬性。 `JsonProperty` 屬性會優先於 `SerializePropertyNamesAsCamelCase` 屬性。
> 
> 

現在，我們定義了模型類別，可以非常輕鬆地建立索引定義︰

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="4---call-indexescreate"></a>4 - 呼叫 Indexes.Create
您現在已初始化 `Index` 物件，請在 `SearchServiceClient` 物件上呼叫 `Indexes.Create`來建立索引：

```csharp
serviceClient.Indexes.Create(definition);
```

若要求成功，方法將會正常傳回。 如果要求發生問題 (例如無效的參數)，方法即會擲回 `CloudException`。

索引已使用完畢而想要將其刪除時，請在 `SearchServiceClient` 上呼叫 `Indexes.Delete` 方法。 例如︰

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> 為簡單起見，本文的範例程式碼使用 Azure 搜尋服務 .NET SDK 的同步方法。 我們建議您在應用程式中使用非同步方法，讓應用程式保有可擴充性且回應靈敏。 例如，您可以在上述範例中使用 `CreateAsync` 和 `DeleteAsync`，而非 `Create` 和 `Delete`。
> 
> 

## <a name="next-steps"></a>後續步驟
在本快速入門中，您會根據定義欄位資料類型和行為的結構描述來建立空的 Azure 搜尋服務索引。 此索引是由名稱和屬性化欄位集合所組成的「準系統」索引。 更實際的索引會包含其他元素，例如[評分設定檔](index-add-scoring-profiles.md)、[建議工具](index-add-suggesters.md) (適用於預先鍵入支援)、[同義字](search-synonyms.md)，還可能包含[自訂分析器](index-add-custom-analyzers.md)。 我們建議在您了解基本工作流程之後，重新瀏覽這些功能。

本系列的下一個快速入門會說明如何載入具有可搜尋的內容的索引。

> [!div class="nextstepaction"]
> [使用 C# 將資料載入 Azure 搜尋服務索引](search-import-data-dotnet.md)