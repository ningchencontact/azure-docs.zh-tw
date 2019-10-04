---
title: 將自動提示查詢加入至索引 Azure 搜尋服務
description: 藉由建立會叫用自動完成或 autosuggested 查詢詞彙的建議工具和表述要求，在 Azure 搜尋服務中啟用預先輸入的查詢動作。
ms.date: 09/30/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
manager: nitinme
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: d3f934bea5df821e51a4747170af4f7efd1eaacc
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828303"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>在 Azure 搜尋服務中，將建議工具新增至自動提示的索引

在 Azure 搜尋服務中，「搜尋型別」或自動提示功能是以您新增至[搜尋索引](search-what-is-an-index.md)的**建議工具**結構為基礎。 這是您要啟用自動提示的一或多個欄位清單。

建議工具支援兩種自動提示變體： [*自動完成*]，這會完成您輸入的字詞或片語，以及傳回相符檔之簡短清單的*建議*。  

下列螢幕擷取畫面，從在範例[中C#建立第一個應用程式](tutorial-csharp-type-ahead-and-suggestions.md)中，說明自動提示。 自動完成會預見使用者可能在搜尋方塊中輸入的內容。 實際的輸入是「臺灣」，自動完成會以「in」完成，並解析為「對應項」做為預期的搜尋詞彙。 建議會在下拉式清單中視覺化。 如需建議，您可以在檔中呈現最能描述結果的任何部分。 在此範例中，建議是旅館名稱。 

![自動完成和建議查詢的視覺效果比較](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "自動完成和建議查詢的視覺化比較")

若要在 Azure 搜尋服務中執行這些行為，有一個索引和查詢元件。 

+ 在索引中，將建議工具加入至索引。 您可以使用入口網站、 [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)或[.net SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)。 本文的其餘部分著重于建立建議工具。 

+ 在查詢要求中，呼叫下列其中一個[api](#how-to-use-a-suggester)。

以每個欄位為基礎啟用搜尋型別支援。 如果您想要有類似螢幕擷取畫面中所示的體驗，您可以在相同的搜尋解決方案內同時執行這兩個自動提示行為。 這兩個要求都會以特定索引的*檔*集合為目標，而在使用者至少提供了三個字元輸入字串之後，就會傳迴響應。

## <a name="create-a-suggester"></a>建立建議工具

雖然建議工具有數個屬性，但它主要是您要啟用自動提示體驗的欄位集合。 例如，旅遊應用程式應該針對目的地、城市及景點啟用鍵盤預先鍵入緩衝搜尋。 因此，所有三個欄位都會放在 fields 集合中。

若要建立建議工具，請將其中一個加入至索引架構。 索引中可以有一個建議工具（尤其是建議工具集合中的一個建議工具）。 

### <a name="when-to-create-a-suggester"></a>建立建議工具的時機

建立建議工具的最佳時機是當您也建立欄位定義本身時。

如果您嘗試使用既有的欄位來建立建議工具，API 將不會允許它。 在編制索引期間，當兩個或多個字元組合中的部分詞彙與整個詞彙一起標記時，會建立自動提示文字。 假設現有的欄位已標記為 token 化，如果您想要將其新增至建議工具，則必須重建索引。 如需重新編制索引的詳細資訊，請參閱[如何重建 Azure 搜尋服務索引](search-howto-reindex.md)。

### <a name="create-using-the-rest-api"></a>使用 REST API 建立

在 REST API 中，透過 [[建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index)] 或 [[更新索引](https://docs.microsoft.com/rest/api/searchservice/update-index)] 來新增建議工具。 

  ```json
  {
    "name": "hotels",
    "fields": [
      . . .
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["hotelName", "category"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```


### <a name="create-using-the-net-sdk"></a>使用 .NET SDK 建立

在C#中，定義[建議工具物件](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)。 `Suggesters` 是一個集合，但它只能接受一個專案。 

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

### <a name="property-reference"></a>屬性參考

|屬性      |描述      |
|--------------|-----------------|
|`name`        |建議工具的名稱。|
|`searchMode`  |用來搜尋候選片語的策略。 目前唯一支援的模式是 `analyzingInfixMatching`，其可在句子開頭或中間執行彈性的片語比對。|
|`sourceFields`|建議之內容來源的一或多個欄位清單。 欄位的類型必須是 `Edm.String`，而 `Collection(Edm.String)`。 如果在欄位上指定分析器，則[此清單](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet)中必須是名稱為 analyzer （不是自訂分析器）。<p/>最佳做法是只指定那些會讓自己成為預期和適當回應的欄位，不論它是搜尋列或下拉式清單中的完整字串。<p/>飯店名稱是很好的候選，因為它有精確度。 詳細資訊欄位（如描述和批註）太密集。 同樣地，重複的欄位（例如分類和標記）比較不有效率。 在範例中，我們仍會包含「類別」，以示範您可以包含多個欄位。 |

### <a name="analyzer-restrictions-for-sourcefields-in-a-suggester"></a>建議工具中 sourceFields 的分析器限制

Azure 搜尋服務會分析欄位內容，以啟用個別詞彙的查詢。 除了完整詞彙以外，建議工具還需要編制前置詞的索引，而這需要對來源欄位進行額外的分析。 自訂分析器設定可以合併各種不同的 token 化工具和篩選器，通常會以產生不可能的建議所需的前置詞的方式來進行。 基於這個理由，Azure 搜尋服務會防止含有自訂分析器的欄位包含在建議工具中。

> [!NOTE] 
>  如果您需要解決上述限制，請針對相同的內容使用兩個不同的欄位。 這可讓其中一個欄位具有建議工具，而另一種則可以使用自訂分析器設定進行設定。

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>在查詢中使用建議工具

建立建議工具之後，請在您的查詢邏輯中呼叫適當的 API，以叫用此功能。 

+ [建議 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [自動完成 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

下列 [自動完成] REST API 的呼叫中會說明 API 的使用方式。 這個範例有兩個優點。 首先，如同所有查詢，作業是針對索引的檔集合。 第二，您可以加入查詢參數。 雖然許多查詢參數對這兩個 Api 都很常見，但每一個都有不同的清單。

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

如果索引中未定義建議工具，則對自動完成或建議的呼叫將會失敗。

## <a name="sample-code"></a>範例程式碼

+ [在範例中C#建立您的第一個應用程式](tutorial-csharp-type-ahead-and-suggestions.md)會示範建議工具結構、建議的查詢、自動完成和多面向導覽。 此程式碼範例會在沙箱 Azure 搜尋服務服務上執行，並使用預先載入的飯店索引，因此您只需要按 F5 執行應用程式。 不需要訂用帳戶或登入。

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)是較舊的範例C# ，其中包含和 JAVA 程式碼。 它也會示範建議工具的結構、建議的查詢、自動完成和多面向導覽。 此程式碼範例會使用託管的[NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)範例資料。 


## <a name="next-steps"></a>後續步驟

我們建議下列範例，以查看要求的編寫方式。

> [!div class="nextstepaction"]
> [建議和自動完成範例](search-autocomplete-tutorial.md) 
