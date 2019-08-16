---
title: 將自動提示查詢加入至索引 Azure 搜尋服務
description: 藉由建立會叫用自動完成或 autosuggested 查詢詞彙的建議工具和表述要求, 在 Azure 搜尋服務中啟用預先輸入的查詢動作。
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
ms.openlocfilehash: 22b53000fa2eebdd8f9cf7fd9f1a2d00763c035b
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533200"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>在 Azure 搜尋服務中, 將建議工具新增至自動提示的索引

**建議工具**是[Azure 搜尋服務索引](search-what-is-an-index.md)中的結構, 可支援「搜尋型別」體驗。 它包含您要啟用自動提示查詢輸入的欄位清單。 在索引中, 相同的建議工具支援這兩個自動提示變體的其中一個或兩個: [*自動完成*] 會完成您輸入的字詞或片語, 而 [*建議*] 會提供簡短的結果清單。 

下列螢幕擷取畫面說明這兩種自動提示功能。 在此 Xbox 搜尋頁面中, 自動完成專案會帶您前往該查詢的新 [搜尋結果] 頁面, 而這些建議是實際的結果, 會將您帶到該特定遊戲的頁面。 您可以將自動完成限制為搜尋列中的一個專案, 或提供如這裡所示的清單。 如需建議, 您可以在檔中呈現最能描述結果的任何部分。

![自動完成和建議查詢的視覺化比較](./media/index-add-suggesters/visual-comparison-suggest-complete.png "自動完成和建議查詢的視覺化比較")

若要在 Azure 搜尋服務中執行這些行為, 有一個索引和查詢元件。 

+ 索引元件是建議工具。 您可以使用入口網站、REST API 或 .NET SDK 來建立建議工具。 

+ 查詢元件是在查詢要求 (建議或自動完成動作) 上指定的動作。 

以每個欄位為基礎啟用搜尋型別支援。 如果您想要有類似螢幕擷取畫面中所示的體驗, 您可以在相同的搜尋解決方案內同時執行這兩個自動提示行為。 這兩個要求都會以特定索引的*檔*集合為目標, 而在使用者至少提供了三個字元輸入字串之後, 就會傳迴響應。

## <a name="create-a-suggester"></a>建立建議工具

雖然建議工具有數個屬性, 但它主要是您要啟用自動提示體驗的欄位集合。 例如，旅遊應用程式應該針對目的地、城市及景點啟用鍵盤預先鍵入緩衝搜尋。 因此, 所有三個欄位都會放在 fields 集合中。

若要建立建議工具, 請將其中一個加入至索引架構。 索引中可以有一個建議工具 (尤其是建議工具集合中的一個建議工具)。 

### <a name="use-the-rest-api"></a>使用 REST API

在 REST API 中, 您可以透過 [[建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index)] 或 [[更新索引](https://docs.microsoft.com/rest/api/searchservice/update-index)] 來新增建議工具。 

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
建立建議工具之後, 請在您的查詢邏輯中新增[建議 api](https://docs.microsoft.com/rest/api/searchservice/suggestions)或[自動完成 api](https://docs.microsoft.com/rest/api/searchservice/autocomplete) , 以叫用此功能。

### <a name="use-the-net-sdk"></a>使用 .NET SDK

在C#中, 定義[建議工具物件](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)。 `Suggesters`是集合, 但只能接受一個專案。 

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

關於建議工具的重點是, 有一個名稱 (要求上的名稱會參考建議工具)、searchMode (目前只有一個)、"analyzingInfixMatching"), 以及已啟用自動提示的欄位清單。 

定義建議工具的屬性包含如下：

|屬性      |描述      |
|--------------|-----------------|
|`name`        |建議工具的名稱。 呼叫 [[建議] REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions)或 [[自動完成] REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)時, 您會使用建議工具的名稱。|
|`searchMode`  |用來搜尋候選片語的策略。 目前唯一支援的模式是 `analyzingInfixMatching`，其可在句子開頭或中間執行彈性的片語比對。|
|`sourceFields`|建議之內容來源的一或多個欄位清單。 只有類型 `Edm.String` 和 `Collection(Edm.String)` 的欄位可以用來做為提供建議的來源。 您只能使用未設定自訂語言分析器的欄位。<p/>僅指定將本身提供給預期和適當回應的欄位, 不論它是搜尋列或下拉式清單中的完整字串。<p/>飯店名稱是很好的候選, 因為它有精確度。 詳細資訊欄位 (如描述和批註) 太密集。 同樣地, 重複的欄位 (例如分類和標記) 比較不有效率。 在範例中, 我們仍會包含「類別」, 以示範您可以包含多個欄位。 |

#### <a name="analysis-of-sourcefields-in-a-suggester"></a>建議工具中的 SourceFields 分析

Azure 搜尋服務會分析欄位內容, 以啟用個別詞彙的查詢。 除了完整詞彙以外, 建議工具還需要編制前置詞的索引, 而這需要對來源欄位進行額外的分析。 自訂分析器設定可以合併各種不同的 token 化工具和篩選器, 通常會以產生不可能的建議所需的前置詞的方式來進行。 基於這個理由, **Azure 搜尋服務會防止含有自訂分析器的欄位包含在建議工具中**。

> [!NOTE] 
>  解決上述限制的建議方法是針對相同的內容使用2個不同的欄位。 這可讓其中一個欄位具有建議工具, 另一個則可使用自訂分析器設定進行設定。

## <a name="when-to-create-a-suggester"></a>建立建議工具的時機

若要避免重建索引, 必須同時建立建議工具和中`sourceFields`指定的欄位。

如果您將建議工具新增至現有的索引 (其中包含現有的`sourceFields`欄位), 則欄位定義基本上會變更, 而且需要重建。 如需詳細資訊, 請參閱[如何重建 Azure 搜尋服務索引](search-howto-reindex.md)。

## <a name="how-to-use-a-suggester"></a>如何使用建議工具

如先前所述, 您可以使用建議工具來取得建議的查詢、自動完成或兩者。 

在要求上會參考建議工具以及作業。 例如, 在「取得 REST」呼叫上, 于`suggest` [ `autocomplete`檔] 集合中指定或。 對於 REST, 在建立建議工具之後, 請在您的查詢邏輯中使用[建議 api](https://docs.microsoft.com/rest/api/searchservice/suggestions)或[自動完成 api](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 。

針對 .NET, 請使用[SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)或[AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)。

如需示範這兩個要求的範例, 請參閱[在 Azure 搜尋服務中新增自動完成和建議的範例](search-autocomplete-tutorial.md)。

## <a name="sample-code"></a>範例程式碼

[DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)範例同時C#包含和 JAVA 程式碼, 並示範建議工具結構、建議的查詢、自動完成和 facet 導覽。 

它使用沙箱 Azure 搜尋服務服務和預先載入的索引, 因此您只需要按 F5 就可以執行它。 不需要訂用帳戶或登入。

## <a name="next-steps"></a>後續步驟

我們建議下列範例, 以查看要求的編寫方式。

> [!div class="nextstepaction"]
> [建議和自動完成範例](search-autocomplete-tutorial.md) 
