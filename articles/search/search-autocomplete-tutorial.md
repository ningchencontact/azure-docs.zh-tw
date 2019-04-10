---
title: 在 [搜尋] 方塊-Azure 搜尋服務中新增的建議和自動完成
description: 啟用 Azure 搜尋服務中的自動提示查詢動作藉由建立建議工具，並擬定填滿與已完成的詞彙或片語搜尋方塊中的要求。
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: 7c06489af7c418a437c2e7a8714c89bee1414c2b
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357958"
---
# <a name="example-add-suggestions-or-autocomplete-to-your-azure-search-application"></a>範例：將建議或自動完成功能新增至您的 Azure 搜尋服務應用程式

在這篇文章，了解如何使用[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)並[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete)建置功能強大的搜尋方塊中支援搜尋---輸入時的行為。

+ *建議*會產生當您輸入時，其中每項建議是符合您已到目前為止輸入索引的單一結果的建議的結果。 

+ *自動完成*，[預覽功能](search-api-preview.md)，「 完成 」 的單字或片語，在使用者目前輸入。 而不是傳回結果，它會完成的查詢，即可執行以傳回結果。 如同建議，已完成的單字或片語查詢中的都被基於索引中相符項目。 服務不會提供傳回零筆結果，在索引中的查詢。

您可以下載並執行範例應用程式**DotNetHowToAutocomplete**來評估這些功能。 範例程式碼為目標的預先建置的索引，填入[為 NYCJobs 示範資料](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)。 為 NYCJobs 索引包含[建議工具建構](index-add-suggesters.md)，這是建議或自動完成是必要項。 您可以使用裝載在沙箱服務中，已備妥之的索引或[填入您自己的索引](#configure-app)為 NYCJobs 範例方案中使用的資料載入器。 

**DotNetHowToAutocomplete**範例會示範建議和自動完成，在C#和 JavaScript 語言版本。 C#開發人員可以逐步執行的 ASP.NET MVC 為基礎的應用程式使用[Azure 搜尋服務.NET SDK](https://aka.ms/search-sdk)。 HomeController.cs 檔案中可進行自動完成和建議的查詢呼叫的邏輯。 JavaScript 開發人員會發現在 IndexJavaScript.cshtml，其中包含直接呼叫的對等的查詢邏輯[Azure 搜尋服務 REST API](https://docs.microsoft.com/rest/api/searchservice/)。 

如需這兩種語言版本，前端使用者經驗為基礎[jQuery UI](https://jqueryui.com/autocomplete/)並[XDSoft](https://xdsoft.net/jqplugins/autocomplete/)程式庫。 我們可以使用這些程式庫來建置支援建議和自動完成功能的 [搜尋] 方塊。 收集在搜尋方塊中輸入要搭配建議和自動完成動作，例如 HomeController.cs 或 IndexJavaScript.cshtml 所定義。

本練習將逐步引導您完成下列工作：

> [!div class="checklist"]
> * 在 JavaScript 並發出要求中實作搜尋輸入的方塊，建議的相符項目或自動完成條款
> * 在C#，在 HomeController.cs 中定義的建議和自動完成動作
> * 在 JavaScript 中，呼叫 REST Api 直接提供相同的功能

## <a name="prerequisites"></a>必要條件

Azure 搜尋服務是針對此練習中選擇性的因為解決方案會使用即時沙箱服務裝載的已備妥的為 NYCJobs 示範索引也一樣。 如果您想要在您自己的搜尋服務上執行這個範例，請參閱[設定 NYC 工作索引](#configure-app)如需相關指示。

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 的任何版本。 範例程式碼和指示已在免費的 Community 版本上經過測試。

* 下載[DotNetHowToAutoComplete 範例](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)。

此範例是全方位、 涵蓋建議、 自動完成、 多面向導覽，和用戶端快取。 您應該檢閱的讀我檔案和如需完整的範例所提供的功能描述的註解。

## <a name="run-the-sample"></a>執行範例

1. 開啟**AutocompleteTutorial.sln** Visual Studio 中。 此方案包含 ASP.NET MVC 專案中使用的 NYC 工作示範索引的連接。

2. 按 F5 執行專案，並在所選擇的瀏覽器中載入頁面。

在頂端，您會看到可供選取 C# 或 JavaScript 的選項。 C#選項呼叫 HomeController 從瀏覽器，並使用 Azure 搜尋服務.NET SDK 擷取結果。 

JavaScript 選項會直接從瀏覽器呼叫 Azure 搜尋服務 REST API。 此選項會將控制器拿到流程外，因此其效能通常會顯著提升。 您可以選擇適合您需求和語言喜好設定的選項。 頁面上有幾個自動完成範例，且各有一些指導方針。 每個範例都有一些建議的文字範例可供您嘗試。  

請嘗試在每個搜尋方塊中輸入幾個字母來看看會發生什麼事情。

## <a name="search-box"></a>搜尋方塊

兩個C#和 JavaScript 的版本中，搜尋方塊實作方式完全相同。 

開啟**Index.cshtml**下檢視程式碼資料夾 \Views\Home 檔案：

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

這是簡單的輸入的文字方塊中的類別樣式、 JavaScript 和預留位置文字所要參考的識別碼。  Magic 位於內嵌的 JavaScript。

C#語言的範例使用在 Index.cshtml 中運用 JavaScript [jQuery UI 自動完成程式庫](https://jqueryui.com/autocomplete/)。 此程式庫會新增至搜尋方塊自動完成體驗，藉由對 MVC 控制器的非同步呼叫，以擷取建議。 JavaScript 語言版本處於 IndexJavaScript.cshtml。 它包含下列指令碼搜尋列中，以及 Azure 搜尋服務 REST API 呼叫。

讓我們看看第一個範例中，它會呼叫 jQuery UI 自動完成函式，在要求中傳遞的建議的 JavaScript 程式碼：

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

若要設定 「 example1a"的輸入方塊的 jQuery UI 自動完成的頁面載入上，瀏覽器中執行上述程式碼。  `minLength: 3` 可確保在搜尋方塊中有至少三個字元時，才會顯示建議。  來源值很重要：

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

上述程式碼說明的 jQuery UI 自動完成函式來取得要在搜尋方塊中顯示的項目清單的位置。 由於這是 MVC 專案時，它會呼叫中包含的邏輯傳回查詢建議 （在下一節中的建議相關的更多） 的 HomeController.cs 建議函式。 此函式也會將控制項反白顯示、 模糊比對和詞彙的幾個參數。 自動完成 JavaScript API 會新增字詞參數。

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>延伸範例以支援模糊比對

當使用者在搜尋方塊中拼錯字組時，模糊搜尋依然可讓其根據相近的字組取得結果。 雖然並非必要，它會大幅改善自動提示體驗的強固性。 請將來源程式行變更為啟用模糊比對，試試這項功能。

將下面這行從：

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

變更為以下程式碼：

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

按 F5 啟動應用程式。

試試看輸入「execative」之類的字詞，並注意其變回「executive」結果的情形 (雖然這些結果並未與您所輸入的字母完全相符)。

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>由 Azure 搜尋服務自動完成的 jQuery 自動完成支援

到目前為止，有已搜尋 UX 程式碼置於建議。 下一步 的程式碼區塊顯示傳入要求，Azure 搜尋服務自動完成功能的 jQuery UI 自動完成函式 (列 91 的在 index.cshtml，):

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 http://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-example"></a>C# 範例

既然我們已檢閱了網頁的 JavaScript 程式碼，讓我們看看C#實際擷取建議的相符項目使用 Azure 搜尋服務.NET SDK 的伺服器端控制器程式碼。

開啟**HomeController.cs** [Controllers] 目錄下的檔案。 

您可能會注意到第一件事是一種方法呼叫的類別頂端`InitSearch`。 此方法會建立已對 Azure 搜尋服務進行驗證的 HTTP 索引用戶端。 如需詳細資訊，請參閱 <<c0> [ 如何使用 Azure 搜尋服務.NET 應用程式](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)。

該行 41 時，請注意建議函式。 它根據[DocumentsOperationsExtensions.Suggest 方法](/dotnet/api/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet-preview)。

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

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

建議函式會採用兩個參數，以決定是要傳回命中結果醒目提示，還是要使用搜尋字詞輸入外加模糊比對。 此方法會建立[SuggestParameters 物件](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet)，然後傳遞到建議 API。 結果接著會轉換為 JSON，以供在用戶端中顯示。

在第 69 行，請注意自動完成函式。 它根據[DocumentsOperationsExtensions.Autocomplete 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet-preview)。

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

自動完成函式會搜尋詞彙輸入。 此方法會建立[AutoCompleteParameters 物件](https://docs.microsoft.com/rest/api/searchservice/autocomplete)。 結果接著會轉換為 JSON，以供在用戶端中顯示。

(選擇性) 在建議函式開頭新增中斷點，然後逐步執行程式碼。 請注意，SDK，以及如何轉換至從方法傳回的結果所傳回的回應。

在頁面上的其他範例會遵循相同的模式，將搜尋結果醒目提示和以支援用戶端快取的自動完成結果的 facet。 檢閱上述每一個項目以了解其運作方式，以及如何在搜尋體驗中運用這些項目。

## <a name="javascript-example"></a>JavaScript 範例

自動完成和建議的 Javascript 實作會呼叫 REST API，做為來源使用的 URI，以指定的索引和作業。 

若要檢閱 JavaScript 實作，請開啟**IndexJavaScript.cshtml**。 請注意，jQuery UI 自動完成函式也會用於搜尋方塊中，收集搜尋詞彙輸入進行非同步呼叫以擷取 Azure 搜尋服務建議的相符項目或完成條款。 

請看第一個範例的 JavaScript 程式碼：

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

如果您比較這個範例與上述會呼叫首頁控制器的範例，您會發現幾個相似之處。  `minLength` 和 `position` 的自動完成設定完全相同。 

這邊的重大變更是來源。 而不是呼叫在首頁控制器的建議方法，REST 要求中的 JavaScript 函式會建立，並且使用 Ajax 來執行。 回應接著會「成功」處理，並作為來源。

REST 呼叫會使用 Uri 來指定是否[Autocomplete](https://docs.microsoft.com/rest/api/searchservice/autocomplete)或是[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)進行 API 呼叫。 下列 Uri 分別為 9 和 10 行上。

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

該行 148 時，您可以找到呼叫的指令碼`autocompleteUri`。 第一次呼叫`suggestUri`一行 39。

> [!Note]
> 進行服務的 REST 呼叫，在 JavaScript 中會提供為方便示範的 REST API，但不是應將其做為最佳作法或建議。 包含 API 金鑰和端點在指令碼會開啟您最多阻絕服務攻擊，可以讀取這些值的指令碼的任何人的服務。 使用 JavaScript，為了方便學習其安全，同時可能索引裝載在免費服務，我們建議使用 Java 或C#在實際執行程式碼中的編製索引和查詢作業。 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>設定您的服務上執行的為 NYCJobs

到目前為止，您已使用的裝載為 NYCJobs 示範索引。 如果您想要完整可見性的所有程式碼，包括索引，請遵循下列指示來建立並載入您自己的搜尋服務中的索引。

1. [建立 Azure 搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用此範例中的免費服務。 

   > [!Note]
   > 如果您使用免費的 Azure 搜尋服務，則最多只能使用三個索引。 NYCJobs 資料載入器會建立兩個索引。 請確定服務內有空間可接受新的索引。

1. 下載[為 NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)範例程式碼。

1. 在 為 NYCJobs 範例程式碼的 DataLoader 資料夾中，開啟**DataLoader.sln** Visual Studio 中。

1. 新增 Azure 搜尋服務的連接資訊。 開啟 DataLoader 專案中的 App.config，然後變更 TargetSearchServiceName 和 TargetSearchServiceApiKey appSettings 以反映 Azure 搜尋服務和 Azure 搜尋服務 API 金鑰。 這些資料可以在 Azure 入口網站中找到。

1. 按 F5 以啟動應用程式，建立兩個索引和匯入 NYCJob 範例資料。

1. 開啟**AutocompleteTutorial.sln**並編輯中的 Web.config **AutocompleteTutorial**專案。 將 SearchServiceName 和 SearchServiceApiKey 值變更為 適用於您的搜尋服務的值。

1. 按 F5 執行應用程式。 範例 web 應用程式會在預設瀏覽器中開啟。 體驗與沙箱版本相同，只有索引和資料裝載於您的服務。

## <a name="next-steps"></a>後續步驟

此範例示範建置搜尋方塊中，支援自動完成和建議的基本步驟。 您已看到如何，您便可以建置 ASP.NET MVC 應用程式上，並使用 Azure 搜尋服務.NET SDK 或 REST API 擷取建議。

在下一個步驟，嘗試整合到您的搜尋體驗的建議和自動完成功能。 下列參考文件應該幫助。

> [!div class="nextstepaction"]
> [自動完成 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [建議 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [建立索引 REST API 上的 Facet 索引屬性](https://docs.microsoft.com/rest/api/searchservice/create-index)

