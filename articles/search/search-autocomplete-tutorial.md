---
title: 在搜尋方塊中新增建議和自動完成-Azure 搜尋服務
description: 藉由建立建議工具, 並以已完成的詞彙或片語填入搜尋方塊, 以在 Azure 搜尋服務中啟用自動提示查詢動作。
manager: nitinme
author: mrcarter8
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: mcarter
ms.openlocfilehash: 1ec343228e32c9dd6fb126560a7a17b54c5e36cb
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183285"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-search-application"></a>將建議或自動完成新增至您的 Azure 搜尋服務應用程式

在本文中, 您將瞭解如何使用[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)和[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete)功能, 建立支援搜尋型別行為的強大搜尋方塊。

+ *建議*是您輸入時所產生的建議結果, 其中每個建議都是索引中符合您到目前為止所輸入內容的單一結果。 

+ *自動完成*「完成」使用者目前輸入的單字或片語。 它不會傳回結果, 而是完成查詢, 然後您就可以執行它來傳回結果。 如同建議, 在查詢中, 已完成的單字或片語會前提索引中的相符項。 服務不會提供在索引中傳回零個結果的查詢。

您可以在**DotNetHowToAutocomplete**中下載並執行範例程式碼, 以評估這些功能。 範例程式碼會將以[NYCJobs 示範資料](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)填入的預建索引做為目標。 NYCJobs 索引包含[建議工具結構](index-add-suggesters.md), 這是使用 [建議] 或 [自動完成] 的需求。 您可以使用沙箱服務中裝載的備妥索引, 或使用 NYCJobs 範例方案中的資料載入器[填入您自己的索引](#configure-app)。 

**DotNetHowToAutocomplete**範例會示範C#和 JavaScript 語言版本中的建議和自動完成。 C#開發人員可以逐步執行使用[Azure 搜尋服務 .NET SDK](https://aka.ms/search-sdk)的 ASP.NET MVC 應用程式。 您可以在 HomeController.cs 檔案中找到進行自動完成和建議查詢呼叫的邏輯。 JavaScript 開發人員會在 IndexJavaScript 中找到對等的查詢邏輯, 其中包含[Azure 搜尋服務 REST API](https://docs.microsoft.com/rest/api/searchservice/)的直接呼叫。 

針對這兩種語言版本, 前端使用者體驗是以[JQUERY UI](https://jqueryui.com/autocomplete/)和[XDSoft](https://xdsoft.net/jqplugins/autocomplete/)程式庫為基礎。 我們會使用這些程式庫來建立支援建議和自動完成的搜尋方塊。 在 [搜尋] 方塊中收集的輸入會與建議和自動完成動作 (例如 HomeController.cs 或 IndexJavaScript 中定義的) 進行配對。

此練習會引導您完成下列工作:

> [!div class="checklist"]
> * 在 JavaScript 中執行搜尋輸入方塊, 並針對建議的相符專案或標記詞彙發出要求
> * 在C#中, 定義 HomeController.cs 中的建議和自動完成動作
> * 在 JavaScript 中, 直接呼叫 REST Api 來提供相同的功能

## <a name="prerequisites"></a>必要條件

Azure 搜尋服務服務在此練習中是選擇性的, 因為解決方案會使用裝載備妥 NYCJobs 示範索引的即時沙箱服務。 如果您想要在自己的搜尋服務上執行此範例, 請參閱[設定 NYC 作業索引](#configure-app)以取得相關指示。

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 的任何版本。 範例程式碼和指示已在免費的 Community 版本上經過測試。

* 下載[DotNetHowToAutoComplete 範例](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)。

此範例完整, 涵蓋建議、自動完成、多面向導覽和用戶端快取。 如需範例所提供內容的完整描述, 請參閱讀我檔案和批註。

## <a name="run-the-sample"></a>執行範例

1. 在 Visual Studio 中開啟**autocompletetutorial.sln** 。 解決方案包含 ASP.NET 的 MVC 專案, 並具有 NYC 作業示範索引的連接。

2. 按 F5 執行專案，並在所選擇的瀏覽器中載入頁面。

在頂端，您會看到可供選取 C# 或 JavaScript 的選項。 C#選項會從瀏覽器呼叫 HomeController, 並使用 AZURE 搜尋服務 .net SDK 來取得結果。 

JavaScript 選項會直接從瀏覽器呼叫 Azure 搜尋服務 REST API。 此選項會將控制器拿到流程外，因此其效能通常會顯著提升。 您可以選擇適合您需求和語言喜好設定的選項。 頁面上有幾個自動完成範例，且各有一些指導方針。 每個範例都有一些建議的文字範例可供您嘗試。  

請嘗試在每個搜尋方塊中輸入幾個字母來看看會發生什麼事情。

## <a name="search-box"></a>搜尋方塊

對於C#和 JavaScript 版本而言, 搜尋方塊的執行方式完全相同。 

開啟資料夾 \Views\Home 底下的**Index. cshtml**檔案, 以查看程式碼:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

這個範例是簡單的輸入文字方塊, 其中包含用於設定樣式的類別、JavaScript 要參考的識別碼, 以及預留位置文字。  神奇之處在于內嵌的 JavaScript。

此C#語言範例會在索引中使用 JavaScript, 以利用[JQuery UI 自動完成程式庫](https://jqueryui.com/autocomplete/)。 此程式庫會對 MVC 控制器發出非同步呼叫來取得建議, 以將自動完成體驗新增至搜尋方塊。 JavaScript 語言版本是在 IndexJavaScript 中。 其中包含適用于搜尋列的腳本, 以及 Azure 搜尋服務的 REST API 呼叫。

讓我們看看第一個範例的 JavaScript 程式碼, 它會呼叫 jQuery UI 自動完成功能, 並傳入建議的要求:

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

上述程式碼會在頁面載入時于瀏覽器中執行, 以設定 "example1a" 輸入方塊的 jQuery UI 自動完成。  `minLength: 3` 可確保在搜尋方塊中至少有三個字元時，才會顯示建議。  來源值很重要：

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

上述程式程式碼會告訴 jQuery UI 自動完成功能, 在哪裡取得要在搜尋方塊底下顯示的專案清單。 由於此專案是 MVC 專案, 因此它會呼叫 HomeController.cs 中的建議函式, 其中包含傳回查詢建議的邏輯 (詳細說明下一節中的建議)。 此函式也會傳遞幾個參數, 以控制重點、模糊比對和詞彙。 自動完成 JavaScript API 會新增字詞參數。

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>延伸範例以支援模糊比對

當使用者在搜尋方塊中拼錯字組時，模糊搜尋依然可讓其根據相近的字組取得結果。 雖然並非必要, 但可大幅改善自動提示體驗的健全性。 請將來源程式行變更為啟用模糊比對，試試這項功能。

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

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>Azure 搜尋服務自動完成支援的 jQuery 自動完成

到目前為止, 搜尋 UX 程式碼已集中在建議的中心。 下一個程式碼區塊會顯示 jQuery UI 自動完成函式 (在 index 中為91行), 並傳入 Azure 搜尋服務自動完成的要求:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
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

既然我們已經複習過網頁的 JavaScript 程式碼, 讓我們來看一下C#伺服器端控制器程式碼, 它會使用 AZURE 搜尋服務 .net SDK 來實際抓取建議的相符專案。

開啟 [控制器] 目錄下的**HomeController.cs**檔案。 

您可能會注意到的第一件事是呼叫`InitSearch`類別頂端的方法。 這個方法會為 Azure 搜尋服務服務建立已驗證的 HTTP 索引用戶端。 如需詳細資訊, 請參閱[如何從 .Net 應用程式使用 Azure 搜尋服務](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)。

在第41行上, 請注意 [建議函式]。 它是以[DocumentsOperationsExtensions. 建議方法](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)為基礎。

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

建議函式會採用兩個參數，以決定是要傳回命中結果醒目提示，還是要使用搜尋字詞輸入外加模糊比對。 方法會建立[SuggestParameters 物件](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), 然後將它傳遞至建議 API。 結果接著會轉換為 JSON，以供在用戶端中顯示。

在第69行上, 請注意自動完成功能。 它是以[DocumentsOperationsExtensions。自動完成方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)為基礎。

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

自動完成功能會接受搜尋字詞輸入。 方法會建立[AutoCompleteParameters 物件](https://docs.microsoft.com/rest/api/searchservice/autocomplete)。 結果接著會轉換為 JSON，以供在用戶端中顯示。

(選擇性) 在建議函式開頭新增中斷點，然後逐步執行程式碼。 請注意 SDK 傳回的回應, 以及如何將其轉換為方法所傳回的結果。

頁面上的其他範例會遵循相同的模式來新增命中結果醒目提示和 facet, 以支援自動完成結果的用戶端快取。 檢閱上述每一個項目以了解其運作方式，以及如何在搜尋體驗中運用這些項目。

## <a name="javascript-example"></a>JavaScript 範例

自動完成和建議的 JAVAscript 執行會呼叫 REST API, 並使用 URI 做為指定索引和作業的來源。 

若要查看 JavaScript 的執行, 請開啟**IndexJavaScript**。 請注意, jQuery UI 自動完成函式也會用於 [搜尋] 方塊, 並收集搜尋字詞輸入, 並對 Azure 搜尋服務進行非同步呼叫, 以取得建議的相符專案或已完成的詞彙。 

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

如果您將此範例與呼叫 Home 控制器的上述範例進行比較, 您會發現幾個相似之處。  `minLength` 和 `position` 的自動完成設定完全相同。 

這邊的重大變更是來源。 REST 要求是在 JavaScript 函式中建立, 並使用 Ajax 執行, 而不是在 home 控制器中呼叫 [建議] 方法。 回應接著會「成功」處理，並作為來源。

REST 呼叫會使用 Uri 來指定是否進行[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete)或[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)API 呼叫。 下列 Uri 分別位於第9和10行。

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

在第148行上, 您可以找到呼叫的`autocompleteUri`腳本。 第一次呼叫`suggestUri`是在39行。

> [!Note]
> 這裡提供以 JavaScript 進行服務的 REST 呼叫, 以方便示範 REST API, 但不應將其視為最佳做法或建議。 在腳本中包含 API 金鑰和端點, 會開啟您的服務, 讓任何人都能從腳本讀取這些值, 而拒絕服務的攻擊。 雖然它可以安全地使用 JavaScript 來進行學習, 但可能是在裝載于免費服務上的索引上, C#但我們建議您在實際執行的程式碼中使用 JAVA 或編制索引和查詢作業。 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>設定要在您的服務上執行的 NYCJobs

到目前為止, 您已使用 hosted NYCJobs 示範索引。 如果您想要完整查看所有程式碼 (包括索引), 請遵循這些指示, 在您自己的搜尋服務中建立和載入索引。

1. [建立 Azure 搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 在此範例中, 您可以使用免費服務。 

   > [!Note]
   > 如果您使用免費的 Azure 搜尋服務，則最多只能使用三個索引。 NYCJobs 資料載入器會建立兩個索引。 請確定服務內有空間可接受新的索引。

1. 下載[NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)範例程式碼。

1. 在 NYCJobs 範例程式碼的 DataLoader 資料夾中, 開啟 Visual Studio 中的**DataLoader** 。

1. 新增 Azure 搜尋服務服務的連接資訊。 開啟 DataLoader 專案中的 App.config，然後變更 TargetSearchServiceName 和 TargetSearchServiceApiKey appSettings 以反映 Azure 搜尋服務和 Azure 搜尋服務 API 金鑰。 這項資訊可在 Azure 入口網站中找到。

1. 按 F5 啟動應用程式, 建立兩個索引並匯入 NYCJob 範例資料。

1. 開啟**autocompletetutorial.sln** , 並編輯**autocompletetutorial.sln**專案中的 web.config。 將 SearchServiceName 和 SearchServiceApiKey 值變更為對您的搜尋服務有效的值。

1. 按 F5 執行應用程式。 範例 web 應用程式會在預設瀏覽器中開啟。 此體驗與沙箱版本相同, 只會在您的服務上託管索引和資料。

## <a name="next-steps"></a>後續步驟

此範例示範建立支援自動完成和建議的搜尋方塊的基本步驟。 您已瞭解如何建立 ASP.NET MVC 應用程式, 並使用 Azure 搜尋服務 .NET SDK 或 REST API 來取得建議。

在下一個步驟中, 嘗試將建議和自動完成整合到您的搜尋體驗中。 下列參考文章應該有所説明。

> [!div class="nextstepaction"]
> [自動完成 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [建議 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [建立索引 REST API 上的 Facet 索引屬性](https://docs.microsoft.com/rest/api/searchservice/create-index)

