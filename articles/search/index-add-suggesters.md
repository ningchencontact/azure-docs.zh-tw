---
title: 將自動提示查詢加入至索引-Azure 搜尋服務
description: 啟用 Azure 搜尋服務中的預先輸入的查詢動作，藉由建立建議工具，並形成要求，以叫用自動完成或 autosuggested 查詢詞彙。
ms.date: 05/02/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: eb6667a1429382ed566826de64ad7ffbe83183cf
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521892"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>將建議工具新增至 Azure 搜尋服務中的自動提示的索引

A**建議工具**是在建構[Azure 搜尋服務索引](search-what-is-an-index.md)支援 「 搜尋---輸入 」 體驗。 它包含您要啟用自動提示查詢輸入欄位的清單。 索引，在相同的建議工具會支援這些兩種自動提示變化之一或兩者： *autocomplete*完成的詞彙或片語輸入，*建議*提供結果的簡短清單。 

下列螢幕擷取畫面說明這兩種自動提示功能。 在這個 Xbox 搜尋頁面中，自動完成的項目帶您前往新的搜尋結果頁面上，該查詢中，而這些建議會帶您前往該特定的遊戲頁面的實際結果。 您可以限制自動完成，在搜尋列中的一個項目，或提供如下所示類似的清單。 如需建議，您可能會出現文件最能描述結果的任何部分。

![自動完成和建議的查詢的視覺化比較](./media/index-add-suggesters/visual-comparison-suggest-complete.png "的自動完成和建議的查詢的視覺化比較")

若要在 Azure 搜尋服務中實作這些行為，沒有索引和查詢的元件。 

+ 索引元件是建議工具。 您可以使用入口網站、 REST API 或.NET SDK 來建立建議工具。 

+ 查詢元件是在查詢要求 （建議或自動完成動作） 中指定的動作。 

針對每個欄位已啟用搜尋---輸入時支援。 如果您想體驗類似於螢幕擷取畫面所示，您可以實作相同的搜尋解決方案中這兩種自動提示行為。 這兩個要求的目標*文件*之後使用者已提供至少三個字元的輸入的字串，會傳回集合特定索引和回應。

## <a name="create-a-suggester"></a>建立建議工具

雖然建議工具有數個屬性，它會是您啟用自動提示體驗的欄位的集合。 例如，旅遊應用程式應該針對目的地、城市及景點啟用鍵盤預先鍵入緩衝搜尋。 因此，所有的三個欄位會放置在 fields 集合。

若要建立建議工具，加入至索引結構描述。 您可以在索引中的一個建議工具 (具體而言，建議工具集合中的一個建議工具)。 

### <a name="use-the-rest-api"></a>使用 REST API

在 REST API 中，您可以新增建議工具，透過[Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index)或是[更新索引](https://docs.microsoft.com/rest/api/searchservice/update-index)。 

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
建立建議工具之後，新增[建議 API](https://docs.microsoft.com/rest/api/searchservice/suggestions)或是[自動完成 API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)在您的查詢邏輯，來叫用的功能。

### <a name="use-the-net-sdk"></a>使用 .NET SDK

在C#，定義[建議工具物件](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)。 `Suggesters` 是集合，但它只能接受一個項目。 

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

## <a name="property-reference"></a>屬性參考

建議工具的相關注意事項重點是，有名稱 （建議工具會在要求中的名稱所參考的）、 searchMode (目前只有一個，「 analyzingInfixMatching") 和自動提示已啟用的欄位清單。 

定義建議工具的屬性包含如下：

|屬性      |說明      |
|--------------|-----------------|
|`name`        |建議工具名稱。 呼叫時，使用的建議工具名稱[建議 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions)或是[自動完成 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)。|
|`searchMode`  |用來搜尋候選片語的策略。 目前唯一支援的模式是 `analyzingInfixMatching`，其可在句子開頭或中間執行彈性的片語比對。|
|`sourceFields`|建議之內容來源的一或多個欄位清單。 只有類型 `Edm.String` 和 `Collection(Edm.String)` 的欄位可以用來做為提供建議的來源。 您只能使用未設定自訂語言分析器的欄位。<p/>無論是完整的字串搜尋列中的下拉式清單，請指定只擇預期且適當的回應，這些欄位。<p/>旅館名稱是很好的候選項目，因為其有效位數。 詳細資訊的欄位，例如描述和註解是過於密集。 同樣地，重複的欄位，例如類別和標記，會比較沒有效率。 在範例中，我們包含 「 類別 」 仍以示範您可以包含多個欄位。 |

## <a name="when-to-create-a-suggester"></a>建立建議工具的時機

若要避免重建索引、 建議工具和欄位中指定`sourceFields`必須建立在相同的時間。

如果您新增至現有的索引，其中中包含現有欄位的建議工具`sourceFields`，徹底變更欄位定義，而重建是必要。 如需詳細資訊，請參閱 <<c0> [ 如何重建的 Azure 搜尋服務索引](search-howto-reindex.md)。

## <a name="how-to-use-a-suggester"></a>如何使用建議工具

如先前所述，您可以使用建議工具，建議的查詢、 自動完成，或兩者。 

建議工具會參考針對要求的作業。 例如，在 GET REST 呼叫中指定`suggest`或`autocomplete`文件集合。 其餘部分，會建立建議工具之後，請使用[建議 API](https://docs.microsoft.com/rest/api/searchservice/suggestions)或[自動完成 API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)在您的查詢邏輯。

適用於.NET，使用[SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)或是[AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)。

如需示範這兩個要求的範例，請參閱 <<c0> [ 新增 Azure 搜尋服務中的 自動完成和建議的範例](search-autocomplete-tutorial.md)。

## <a name="sample-code"></a>範例程式碼

[DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)範例同時包含C#和 Java 程式碼，並示範建議工具建構、 建議的查詢，自動完成，以及多面向導覽。 

它會使用 Azure 搜尋服務的沙箱和預先載入的索引，所以所有您只需要是按 f5 鍵執行它。 沒有訂用帳戶或登入需要。

## <a name="next-steps"></a>後續步驟

我們建議下列的範例，以瞭解如何要求所建構而成。

> [!div class="nextstepaction"]
> [建議和自動完成範例](search-autocomplete-tutorial.md) 
