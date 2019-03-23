---
title: 自動建議加入搜尋方塊-Azure 搜尋服務中的下拉式清單中詞彙的範例
description: 藉由建立建議工具，並擬定叫用建議的查詢，在 Azure 搜尋服務的要求中新增建議的查詢輸入。
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: heidist
ms.openlocfilehash: 1db085d61c60d303aaff5c3b0d16998805fcda90
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373071"
---
# <a name="example-add-autosuggest-for-dropdown-query-selections"></a>範例：新增下拉式清單中選取之查詢項目的自動建議

搜尋詞彙輸入可以包含建議的查詢詞彙的下拉式清單。 您已了解商業的搜尋引擎，在這項功能，您可以在使用 Azure 搜尋服務中實作類似的體驗[建議工具建構](index-add-suggesters.md)和建議作業上的查詢要求。 這篇文章會使用範例來示範自動建議查詢，使用您已定義的建議工具的形式。 

## <a name="rest-api"></a>REST API

您可以使用[Postman](search-fiddler.md)或是[PowerShell](search-create-index-rest-api.md)並[REST API](https://docs.microsoft.com/rest/api/searchservice/)試用此範例中，但結果會傳回為 JSON 文件。 使用就可以找到更實際且視覺體驗[範例程式碼的自動完成](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)。

### <a name="1---index-with-a-suggester-construct"></a>1-使用編製索引的建議工具建構

自動建議開頭[建議工具建構](index-add-suggesters.md)新增到索引，提供下拉式清單中值的欄位所組成。 指定下列的結構描述，建議的查詢將會構成使用從 hotelName] 和 [類別目錄欄位的值。

假設在快速入門中找到的最小的範例資料集，旅館名稱包含"花俏保持"和"Roach motel"，而類別包含"Luxury"和"Budget"。

如果您已經有的 hotels 索引時，您應該卸除並重新建立使用下列結構描述。 此結構描述新增建議工具。 請記得重新載入的資料。

```json
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ],
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["hotelName", "category"]
    }
  ]
}

```

### <a name="2---query-with-suggestions-operator"></a>2-查詢建議運算子

使用建議工具，並叫用自動建議，您必須傳送[建議 API](https://docs.microsoft.com/rest/api/searchservice/suggestions)要求使用 GET 或 POST。 在這類要求中，搜尋服務會掃描可能相符項目之後收到的前三個字元。 

要求標頭中設定**api 金鑰**系統管理員或查詢索引鍵，並**Content-type**為 application/json。 

```http
GET https://mydemo.search.windows.net/indexes/hotels/docs/suggest?search=fan&&suggesterName=sg
```

建議工具 （hotelName 和類別目錄） 中的所有欄位都包含要求掃描傳回下列回應：

```
{
    "@odata.context": "https://mydemo.search.windows.net/indexes('hotels')/$metadata#docs(*)",
    "value": [
        {
            "@search.text": "Fancy Stay",
            "hotelId": "1"
        }
    ]
}
```

為了提供預期的結果，用戶端程式碼會將結果轉譯成搜尋列下方的下拉式清單。

## <a name="net-sdk-c"></a>.NET SDK (C#)

### <a name="1---index-with-a-suggester-construct"></a>1-使用編製索引的建議工具建構

在.NET SDK 中，使用[建議工具類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)。 建議工具集合，但它只能接受一個項目。

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

### <a name="2---query-with-suggest-method"></a>2-查詢使用建議的方法

[DocumentsOperationsExtensions.Suggest 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)用來傳回建議的查詢字串。

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult suggestResult = _indexClient.Documents.Suggest(term, "sg",sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

## <a name="see-also"></a>請參閱

+ [瀏覽使用 Postman 的 REST API](search-fiddler.md)
+ [範例：自動完成](search-autocomplete-tutorial.md)
+ [將建議工具新增至索引](index-add-suggesters.md)
+ [新增使用.NET 的建議工具類別](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)
+ [呼叫使用 GET 或 POST (REST API) 的建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)
+ [呼叫使用 SuggestWithHttpMessagesAsync (.NET) 的建議](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview)或 