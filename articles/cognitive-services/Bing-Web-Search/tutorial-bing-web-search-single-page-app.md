---
title: Bing Web 搜尋單頁 Web 應用程式 | Microsoft Docs
description: 說明如何在單頁 Web 應用程式中使用 Bing Web 搜尋 API。
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: f22e38a1d6ee4042684b9822b58669bed6fe29a0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369990"
---
# <a name="tutorial-single-page-web-app"></a>教學課程：單頁 Web 應用程式

Bing Web 搜尋 API 可讓您搜尋網頁，並取得與搜尋查詢相關的各類結果。 在本教學課程中，我們會建置單頁 Web 應用程式，以使用 Bing Web 搜尋 API 直接在頁面中顯示搜尋結果。 該應用程式包含 HTML、CSS 和 JavaScript 元件。

<!-- Remove until this can be replaced with a sanitized version.
![[Single-page Bing Web Search app]](media/cognitive-services-bing-web-api/web-search-spa-demo.png)
-->

> [!NOTE]
> 點按時，頁面底部的 JSON 和 HTTP 標題會顯示 JSON 回應和 HTTP 要求資訊。 這些詳細資料有助於探索服務。

本教學課程應用程式將說明如何：

> [!div class="checklist"]
> * 在 JavaScript 中執行 Bing Web 搜尋 API 呼叫
> * 將搜尋選項傳至 Bing Web 搜尋 API
> * 顯示網頁、新聞、影像和視訊的搜尋結果
> * 逐頁檢視搜尋結果
> * 處理 Bing 用戶端識別碼和 API 訂用帳戶金鑰
> * 處理可能會發生的錯誤

本教學課程頁面完全獨立，不會使用任何外部架構、樣式表甚或影像檔案。 其中僅使用廣受支援的 JavaScript 語言功能，並且與所有主要網頁瀏覽器的目前版本搭配使用。

在本教學課程中，我們只會討論原始程式碼的特定部分。 完整的原始程式碼位於[另一個頁面上](tutorial-bing-web-search-single-page-app-source.md)。 請將此程式碼複製並貼到文字編輯器中，然後另存為 `bing.html`。

## <a name="app-components"></a>應用程式元件

如同任何單頁 Web 應用程式，本教學課程應用程式包含三個部分：

> [!div class="checklist"]
> * HTML - 定義頁面的結構和內容
> * CSS - 定義頁面的外觀
> * JavaScript - 定義頁面的行為

本教學課程並未詳細說明大部分的 HTML 或 CSS，因為這些元件相當簡單。

HTML 包含搜尋表單，使用者可在其中輸入查詢並選擇搜尋選項。 該表單會透過 `<form>` 標記的 `onsubmit` 屬性，連線至實際執行搜尋的 JavaScript：

```html
<form name="bing" onsubmit="return newBingWebSearch(this)">
```

`onsubmit` 處理常式會傳回 `false`，這可防止表單提交至伺服器。 JavaScript 程式碼會實際從表單收集所需的資訊，並執行搜尋。

HTML 也包含顯示搜尋結果的區域 (HTML `<div>` 標記)。

## <a name="managing-subscription-key"></a>管理訂用帳戶金鑰

為了避免必須在程式碼中包含 Bing 搜尋 API 訂用帳戶金鑰，我們使用瀏覽器的永續性儲存體來儲存金鑰。 若未儲存金鑰，我們會提示使用者提供金鑰，並加以儲存供後續使用。 如果 API 稍後拒絕金鑰，我們會讓儲存的金鑰失效，並再次提示使用者。

我們會定義 `storeValue` 和 `retrieveValue` 函式，以使用 `localStorage` 物件 (如果瀏覽器支援的話) 或 Cookie。 我們的 `getSubscriptionKey()` 函式會使用這些函式來儲存及擷取使用者的金鑰。

```javascript
// cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

HTML `form` 標記 `onsubmit` 會呼叫 `bingWebSearch` 函式以傳回搜尋結果。 `bingWebSearch` 會使用 `getSubscriptionKey` 來驗證每個查詢。 如先前的定義所示，如果尚未輸入金鑰，`getSubscriptionKey` 就會提示使用者輸入金鑰。 其後應儲存金鑰，以供應用程式繼續使用。

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>選取搜尋選項

![[Bing Web 搜尋表單]](media/cognitive-services-bing-web-api/web-search-spa-form.png)

HTML 表單包含具有下列名稱的元素：

| | |
|-|-|
| `where` | 可供選取市場 (位置和語言) 以用於搜尋的下拉式功能表。 |
| `query` | 要在其中輸入搜尋字詞的文字欄位。 |
| `what` | 用來升階特定結果種類的核取方塊。 例如，升階影像會提高影像的排名。 |
| `when` | 可選擇性地將搜尋限定為最近一天、一週或一個月的下拉式功能表。 |
| `safe` | 指出是否要使用 Bing 的安全搜尋功能來篩選掉「成人」結果的核取方塊。 |
| `count` | 隱藏的欄位。 毎個要求要傳回的搜尋結果數目。 變更為每頁顯示更多或更少結果。 |
| `offset` | 隱藏的欄位。 要求中第一個搜尋結果的位移；用於分頁。 它會在新要求執行時重設為 `0`。 |

> [!NOTE]
> Bing Web 搜尋提供了其他多項查詢參數。 我們在此僅使用其中一些參數。

JavaScript 函式 `bingSearchOptions()` 會將這些欄位轉換為 Bing 搜尋 API 所需的格式。

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++) 
        if (form.what[i].checked) what.push(form.what[i].value);
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

例如，實際 API 呼叫中的 `SafeSearch` 參數可以是 `strict`、`moderate` 或 `off`，預設值為 `moderate`。 不過，我們的表單使用只有兩種狀態的核取方塊。 JavaScript 程式碼會將此設定轉換為 `strict` 或 `off` (不會使用 `moderate`)。

如果標示了任何 [升階] 核取方塊，我們也會將 `answerCount` 參數新增至查詢。 如果使用 `promote` 參數，則必須使用 `answerCount`。 我們會逕行將其設為 `9` (Bing Web 搜尋 API 所支援的結果類型數目)，以確定能夠盡可能取得最大的結果類型數目。

> [!NOTE]
> 將某個結果類型升階，並不*保證*搜尋結果一定會包含該類型的結果。 實際情況是，升階會提高此類結果相對於其正常狀態下的排名。 若要將搜尋限定於特定類型的結果，請使用 `responseFilter` 查詢參數，或呼叫更明確的端點，例如 Bing 影像搜尋或 Bing 新聞搜尋。

我們也會傳送 `textDecoration` 和 `textFormat` 查詢參數，使搜尋字詞在搜尋結果中以粗體顯示。 這些值在指令碼中會採硬式編碼。

## <a name="performing-the-request"></a>執行要求

假設有查詢、選項字串和 API 金鑰，`BingWebSearch`　函式會使用 `XMLHttpRequest` 物件向 Bing Web 搜尋端點提出要求。

```javascript
// perform a search given query, options string, and API key
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // open the request
    try {
        request.open("GET", queryurl);
    } 
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);
    
    // event handler for successful response
    request.addEventListener("load", handleBingResponse);
    
    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```

HTTP 要求成功完成時，JavaScript 會呼叫我們的 `load` 事件處理常式 (`handleBingResponse()` 函式)，來處理成功傳遞至 API 的 HTTP GET 要求。 

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
        return;
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> 成功的 HTTP 要求「不一定」表示搜尋本身成功。 如果搜尋作業中發生錯誤，Bing Web 搜尋 API 會傳回非 200 HTTP 狀態碼，並在 JSON 回應中包含錯誤資訊。 此外，如果要求速率受到限制，API 會傳回空白回應。

上述兩個函式中大部分的程式碼都是專門用來處理錯誤的。 錯誤可能會在下列階段發生：

|階段|可能的錯誤|處理者|
|-|-|-|
|建置 JavaScript 要求物件|無效的 URL|`try`/`catch` 區塊|
|提出要求|網路錯誤、已中止連線|`error` 和 `abort` 事件處理常式|
|執行搜尋|無效的要求、無效的 JSON、速率限制|`load` 事件處理常式中的測試|

錯誤會透過呼叫 `renderErrorMessage()` 來處理，並提供關於錯誤的任何已知詳細資料。 如果回應通過錯誤測試的所有檢驗，我們會呼叫 `renderSearchResults()` 以在頁面中顯示搜尋結果。

## <a name="displaying-search-results"></a>顯示搜尋結果

Bing Web 搜尋 API 會[要求您依指定順序顯示結果](useanddisplayrequirements.md)。 由於 API 可能傳回不同類型的回應，因此逐一查看 JSON 回應中的最上層 `WebPages` 集合並顯示這些結果，是不夠的。 (如果您只需要一個類型的結果，請使用 `responseFilter` 查詢參數或其他 Bing 搜尋端點。)

正確的做法是，我們會在搜尋結果中使用 `rankingResponse`，以排序要顯示的結果。 此物件會參照 `WebPages`、`News`、`Images` 及/或 `Videos` 集合中的字詞，或 JSON 回應中其他最上層答案集合內的字詞。

`rankingResponse` 最多可包含三個搜尋結果集合，並已指定 `pole`、`mainline` 和 `sidebar`。 

`pole` (如果存在) 是最相關的搜尋結果，應優先顯示。 `mainline` 會參照大量搜尋結果。 Mainline 結果應在 `pole` 之後立即顯示 (如果 `pole` 不存在，則優先顯示)。 

最後， `sidebar` 會參照輔助搜尋結果。 這些結果通常是相關的搜尋或影像。 可能的話，這些結果應顯示在實際的資訊看板中。 如果因螢幕限制而無法顯示資訊看板 (例如，在行動裝置上)，則這些結果應顯示在 `mainline` 結果後面。

`rankingResponse` 集合中的每個項目會以不同但對等的方式參照實際搜尋結果項目。

| | |
|-|-|
|`id`|`id` 看起來像是 URL，但不應該用於連結。 排名結果的 `id` 類型會符合答案集合中某個搜尋結果項目的 `id`，「或」整個答案集合 (例如 `Images`) 的 id。
|`answerType`、`resultIndex`|`answerType` 會參照包含結果的最上層答案集合 (例如 `WebPages`)。 `resultIndex` 會參照該集合內的結果索引。 如果省略 `resultIndex`，則排名結果會參照整個集合。

> [!NOTE]
> 如需這部分搜尋回應的詳細資訊，請參閱[排名結果](rank-results.md)。

您可以使用對您的應用程式而言最方便的任何方法，來尋找參考的搜尋結果項目。 在我們的教學課程程式碼中，我們使用 `answerType` 和 `resultIndex` 來尋找每個搜尋結果。

最後，讓我們來看看函式 `renderSearchResults()`。 此函式會逐一查看三個 `rankingResponse` 集合，這分別代表搜尋結果的三個區段。 針對每個區段，我們會呼叫 `renderResultsItems()` 以轉譯該區段的結果。

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    // for each possible section, render the resuts from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

`renderResultsItems()` 會依序逐一查看每個 `rankingResponse` 區段中的項目、使用 `answerType` 和 `resultIndex` 欄位將每個排名結果對應至搜尋結果，然後呼叫適當的轉譯函式以產生結果的 HTML。 

若未針對給定的排名項目指定 `resultIndex`，則 `renderResultsItems()` 會逐一查看該類型的所有結果，並為每個項目呼叫轉譯函式。 

無論如何，產生的 HTML 都會插入到頁面上的適當 `<div>` 元素中。

```javascript
// render search results from rankingResponse object in specified order
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` rankingResult type is in the `webPages` top-level collection
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        // must have results of the given type AND a renderer for it
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // this ranking item refers to ONE result of the specified type
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // this ranking item refers to ALL results of the specified type
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="rendering-result-items"></a>轉譯結果項目

我們的 JavaScript 程式碼中有一個物件 `searchItemRenderers`，其中包含 *renderers:* 函式，可為每種搜尋結果產生 HTML。

```javascript
// render functions for various types of search results
searchItemRenderers = { 
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!NOTE]
> 我們的教學課程應用程式具有網頁、新聞項目、影像、視訊和相關搜尋的轉譯器。 您自己的應用程式則需要有轉譯器可用於您可能取得的各類結果，其中可能包括運算、拼字建議、實體、時區和定義等。

我們的某些轉譯函式僅接受 `item` 參數：代表單一搜尋結果的 JavaScript 物件。 其餘函式則接受其他參數，可用來以不同方式轉譯不同內容中的項目。 (未使用這項資訊的轉譯器不需要接受這些參數。)

內容引數包括：

| | |
|-|-|
|`section`|顯示項目的結果區段 (`pole`、`mainline` 或 `sidebar`)。
|`index`<br>`count`|如果 `rankingResponse` 項目指定要顯示給定集合中的所有結果，則可供使用，否則會使用 `undefined`。 這些參數會接收集合內所含項目的索引，以及該集合中的項目總數。 您可以使用此資訊為結果編號，而為第一個或最後一個結果 (或其他結果) 產生不同的 HTML。|

在我們的教學課程應用程式中，`images` 和 `relatedSearches` 轉譯器都會使用內容引數自訂它們所產生的 HTML。 以下我們將深入觀察 `images` 轉譯器：

```javascript
searchItemRenderers = { 
    // render image result using thumbnail
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    }, // other renderers omitted
}
```

影像轉譯器函式會：

> [!div class="checklist"]
> * 計算影像縮圖大小 (寬度會變動，高度則固定為 60 像素)。
> * 根據內容插入位於影像結果前面的 HTML。
> * 建置 HTML `<a>` 標記以連結至包含影像的頁面。
> * 建置 HTML `<img>` 標記以顯示影像縮圖。 

影像轉譯器會使用 `section` 和 `index` 變數，根據結果的顯示位置以不同方式顯示結果。 資訊看板中的影像結果之間會插入分行符號 (`<br>` 標記)，讓資訊看板顯示影像的資料行。 在其他區段中，第一個影像結果 `(index === 0)`前面會有 `<p>` 標記。 否則縮圖會在瀏覽器視窗中視需要彼此接合並圍繞。

縮圖大小會用於 `<img>` 標記以及縮圖 URL 中的 `h` 和 `w` 欄位。 [Bing 縮圖服務](resize-and-crop-thumbnails.md)接著會提供完全符合該大小的縮圖。 `title` 和 `alt` 屬性 (影像的文字描述) 會從影像的名稱和 URL 中的主機名稱建構。

影像會依下列方式顯示在主線搜尋結果中。

![[Bing 影像結果]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persisting-client-id"></a>保存用戶端識別碼

來自 Bing 搜尋 API 的回應可能會包含應在後續要求中傳回至 API 的 `X-MSEdge-ClientID` 標頭。 如果要使用多個 Bing 搜尋 API，請盡可能對所有 API 使用相同的用戶端識別碼。

提供 `X-MSEdge-ClientID` 標頭可讓 Bing API 建立所有使用者搜尋的關聯，這有兩個重要的優點。

首先，這樣可讓 Bing 搜尋引擎將過去的內容套用至搜尋結果，以尋找更符合使用者需要的結果。 例如，如果使用者之前搜尋過航海相關字詞，則其後搜尋「節」時可能會優先傳回與航海用的節有關的資訊。

其次，Bing 可能會隨機選取使用者來體驗新功能，再廣泛提供這些功能。 在每個要求中提供相同的用戶端識別碼，可確保已選擇看到功能的使用者一律會看到功能。 若沒有用戶端識別碼，使用者可能會在其搜尋結果中看到功能不規則地出現和消失。

瀏覽器安全性原則 (CORS) 可防止將 `X-MSEdge-ClientID` 標頭提供給 JavaScript 使用。 當搜尋回應的來源與要求回應的頁面不同時，就會有這項限制。 在生產環境中，您應該裝載伺服器端指令碼，在與網頁相同的網域上執行 API 呼叫，以處理此原則。 由於指令碼的來源與網頁相同，因此 `X-MSEdge-ClientID` 標頭會接著提供給 JavaScript 使用。

> [!NOTE]
> 在生產 Web 應用程式中，無論如何都應該執行要求伺服器端。 否則，您的 Bing 搜尋 API 金鑰必須包含在網頁中，以提供給檢視來源的任何人。 您會根據 API 訂用帳戶金鑰的所有使用量付費，即使是未經授權的合作對象所提出的要求，因此請務必不要公開您的金鑰。

若要進行開發，您可以透過 CORS Proxy 提出 Bing Web 搜尋 API 要求。 來自這類 Proxy 的回應包含 `Access-Control-Expose-Headers` 標頭，可將回應標頭列入白名單並提供給 JavaScript 使用。

您可以輕鬆地安裝 CORS Proxy，讓我們的教學課程應用程式存取用戶端識別碼標頭。 首先，請[安裝 Node.js](https://nodejs.org/en/download/) (如果尚未安裝)。 然後在命令視窗中發出下列命令：

    npm install -g cors-proxy-server

接下來，將 HTML 檔案中的 Bing Web 搜尋端點變更為：

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

最後，使用下列命令啟動 CORS Proxy：

    cors-proxy-server

當您使用教學課程應用程式時，請保留開啟命令視窗；關閉視窗會停止 Proxy。 在可展開的 [HTTP 標頭] 區段搜尋結果下，您現在可以看到 `X-MSEdge-ClientID` 標頭 (及其他標頭)，並確認每個要求的此標頭都相同。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [圖像式搜尋行動應用程式教學課程](computer-vision-web-search-tutorial.md)

> [!div class="nextstepaction"]
> [Bing Web 搜尋 API 參考](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
