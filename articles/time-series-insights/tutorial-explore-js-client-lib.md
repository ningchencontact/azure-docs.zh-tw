---
title: 教學課程：探索 Azure 時間序列深入解析 JavaScript 用戶端程式庫 | Microsoft Docs
description: 本教學課程可讓您了解 Azure 時間序列深入解析 JavaScript 用戶端程式庫和相關的程式設計模型。
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: 3e56792673c33ac641ce23213f2b3662b889ec2a
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725652"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>教學課程：探索 Azure 時間序列深入解析 JavaScript 用戶端程式庫

為了協助 Web 開發人員查詢時間序列深入解析中儲存的資料並將其視覺化，我們開發出以 JavaScript D3 為基礎的 Azure 時間序列深入解析用戶端程式庫。 本教學課程會引導您使用託管的應用程式範例來探索時間序列深入解析用戶端程式庫及程式設計模型。

本教學課程會詳細說明如何使用程式庫、如何存取時間序列深入解析資料，以及如何使用圖表控制項來轉譯並視覺化資料。 您也將了解如何嘗試以不同種類的圖表來將資料視覺化。 在本教學課程結束時，您將能夠使用用戶端程式庫將時間序列深入解析功能納入自己的 Web 應用程式。

具體而言，您將了解：

> [!div class="checklist"]
> * 時間序列深入解析應用程式範例
> * 時間序列深入解析 JavaScript 用戶端程式庫
> * 應用程式範例如何使用程式庫來將時間序列深入解析資料視覺化

> [!NOTE]
> * 本教學課程將使用免費的託管[時間序列深入解析 Web 示範](https://insights.timeseries.azure.com/clientsample)。
> * [GitHub 範例存放庫](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)會提供時間序列深入解析範例應用程式的來源檔案。
> * 請閱讀[時間序列深入解析用戶端參考文件](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)。

如果您沒有帳戶，請註冊[免費 Azure 訂用帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

* 本教學課程使用您瀏覽器的**開發人員工具**功能。 新式網頁瀏覽器 ([Microsoft Edge](/microsoft-edge/devtools-guide)、[Chrome](https://developers.google.com/web/tools/chrome-devtools/)、[Firefox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools) 和 [Safari](https://developer.apple.com/safari/tools/) 等等) 通常都可透過鍵盤上的 F12 快速鍵來存取 **Web 檢查工具檢視**。 另一種可存取檢視的方法是在網頁上按一下滑鼠右鍵，然後選取 [檢查元素]  。

## <a name="review-video"></a>檢閱影片

在此影片中，我們會介紹開放原始碼「時間序列深入解析 JavaScript SDK」：
<br /><br />

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="sample-application"></a>範例應用程式

本教學課程通篇使用免費的託管時間序列深入解析應用程式範例，來瀏覽應用程式背後的原始程式碼以及瀏覽時間序列深入解析 JavaScript 用戶端程式庫。 您可藉由使用此應用程式範例，來了解如何以 JavaScript 與時間序列深入解析互動，以及如何透過圖表和圖形來將資料視覺化。

1. 移至[時間序列深入解析應用程式範例](https://insights.timeseries.azure.com/clientsample)。 隨即會出現下列登入提示：

   [![時間序列深入解析用戶端範例登入提示](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. 選取 [登入]  ，以輸入或選取您的認證。 請使用企業組織帳戶 (Azure Active Directory) 或個人帳戶 (Microsoft 帳戶)。

   [![時間序列深入解析用戶端範例認證提示](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. 登入之後會出現一個頁面，其中顯示已填入時間序列深入解析資料的圖表。 您的使用者帳戶和**登出**選項會顯示在右上角：

   [![登入之後的時間序列深入解析用戶端範例主頁面](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>網頁原始碼和結構

首先，讓我們來檢視轉譯網頁的 [HTML 和 JavaScript 原始程式碼](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html)：

1. 在您的瀏覽器中開啟 [開發人員工具]  。 檢查組成目前網頁的 HTML 元素 (又稱為 HTML 或 DOM 樹狀結構)。

1. 展開 `<head>` 和 `<body>` 元素，並觀察下列各區段：

   * 在 `<head>` 元素底下，您可以找到可讓應用程式執行下列項目的頁面中繼資料和相依性：
     * 用來參照 Azure Active Directory 驗證程式庫 (ADAL) 檔案 adal.min.js  的 `<script>` 元素。 ADAL 是一個 JavaScript 程式庫，會提供 OAuth 2.0 驗證 (登入)，並且可讓您取得用來存取 API 的權杖。
     * 用於樣式表 (也稱為 CSS  ，例如 sampleStyles.css  和 tsiclient.css  ) 的多個 `<link>` 元素。 樣式表可控制視覺效果網頁樣式詳細資料，例如色彩、字型和間距。
     * 用來參照時間序列深入解析 JavaScript 用戶端程式庫 tsiclient.js  的 `<script>` 元素。 該頁面會使用此程式庫來呼叫時間序列深入解析服務 API，以及轉譯該頁面上的圖表控制項。

     >[!NOTE]
     > * ADAL JavaScript 程式庫的原始程式碼可於 [azure-activedirectory-library-for-js 存放庫](https://github.com/AzureAD/azure-activedirectory-library-for-js)取得。
     > * 時間序列深入解析 JavaScript 用戶端程式庫的原始程式碼可於 [tsiclient 存放庫](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)取得。

   * 在 `<body>` 元素底下，您可以找到 `<div>` 元素，這可幫助您定義頁面上的項目配置，此外，還可以找到另一個 `<script>` 元素：
     * 第一個 `<div>` 元素會指定 [登入]  對話方塊 (`id="loginModal"`)。
     * 第二個 `<div>` 元素則作為下列元素的父項：
       * 標頭 `<div>` 元素，用於頁面頂端附近的狀態訊息和登入資訊 (`class="header"`)。
       * 用於頁面主體元素其餘部分的 `<div>` 元素，包括圖表 (`class="chartsWrapper"`)。
       * `<script>` 區段，其中包含用來控制頁面的 JavaScript。

   [![使用開發人員工具的時間序列深入解析用戶端範例](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. 展開 `<div class="chartsWrapper">` 元素，您會發現更多子 `<div>` 元素。 這些元素會用來定位每個圖表控制項範例。 其中有好幾對 `<div>` 元素，每個圖表範例各會使用一對：

   * 第一個 (`class="rowOfCardsTitle"`) 元素包含用來彙總圖表所述項目的敘述性標題。 例如：`Static Line Charts With Full-Size Legends.`
   * 第二個 (`class="rowOfCards"`) 元素是父系，包含其他用來將資料列中實際圖表控制項定位的子 `<div>` 元素。

   [![主體 div 元素](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. 展開位於 `<div class="chartsWrapper">` 元素正下方的 `<script type="text/javascript">` 元素。 頁面層級 JavaScript 區段的開頭處，這部分可用來處理所有頁面邏輯 (驗證、呼叫時間序列深入解析服務 API、轉譯圖表控制項等等)：

   [![主體指令碼](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="javascript-client-library"></a>JavaScript 用戶端程式庫

時間序列深入解析用戶端程式庫 (*tsiclient.js*) 提供兩個重要 JavaScript 功能的抽象概念：

* **用來呼叫時間序列深入解析查詢 API 的包裝函式方法**：可讓您使用彙總運算式來查詢時間序列深入解析資料的 REST API。 這些方法會組織在程式庫的 TsiClient.Server 命名空間下。

* **用來建立和填入數種圖表控制項類型的方法**：可用來在網頁上呈現時間序列深入解析彙總資料的方法。 這些方法會組織在程式庫的 TsiClient.UX 命名空間下。

透過這些簡化方式，開發人員可以更輕易地建置由時間序列深入解析資料支援的 UI 圖形和圖表元件。

### <a name="authentication"></a>Authentication

[時間序列深入解析應用程式範例](https://insights.timeseries.azure.com/clientsample)是具有 ADAL OAuth 2.0 使用者驗證支援的單頁應用程式：

1. 使用 ADAL 進行驗證時，用戶端應用程式必須在 Azure Active Directory (Azure AD) 中註冊。 事實上，註冊單頁應用程式是為了使用 [OAuth 2.0 隱含授與流程](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow)。
1. 應用程式必須在執行階段指定一些註冊屬性。 這些屬性包括用戶端 GUID (`clientId`) 和重新導向 URI (`postLogoutRedirectUri`)。
1. 之後，應用程式會從 Azure AD 要求「存取權杖」  。 系統會對特定服務或 API 識別碼 (https:\//api.timeseries.azure.com) 的一組有限權限發出存取權杖。 這些權杖權限是由系統代表所登入的使用者來發出。 服務或 API 的識別碼是應用程式 Azure AD 註冊中所包含的另一個屬性。
1. ADAL 將存取權杖傳回給應用程式後，該存取權杖在 ADAL 存取時間序列深入解析服務 API 時就會以*持有人權杖*的形式來傳遞。

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> 若要深入了解 Microsoft 支援的 Azure AD 驗證程式庫，請參閱 [Azure Active Directory 驗證程式庫參考文件](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries)。

### <a name="control-identification"></a>控制項識別

在提供的範例中，`<div>` 元素會在父代 `<body>` 元素中加以排列，以針對頁面上轉譯的圖表控制項提供合適的版面配置。

每個 `<div>` 元素各自都會針對圖表控制項的位置和視覺效果屬性指定相關屬性。 HTML 元素的 `id` 屬性會作為繫結至特定控制項的唯一識別碼，以轉譯及更新視覺化的資料。

### <a name="aggregate-expressions"></a>彙總運算式

時間序列深入解析用戶端程式庫 API 會使用彙總運算式：

* 彙總運算式可讓您建構一或多個*搜尋字詞*。

* 用戶端 API 可提供類似於其他示範應用程式 ([時間序列深入解析總管](https://insights.timeseries.azure.com/demo)) 的功能，其使用搜尋範圍、`where` 述詞、量值和 `splitBy` 值。

* 大多數用戶端程式庫 API 採用彙總運算式陣列，以供服務用來建置時間序列深入解析資料查詢。

### <a name="call-pattern"></a>呼叫模式

圖表控制項的填入和轉譯會遵循一般模式。 您會在整個應用程式範例中看到一般模式，其可在您使用用戶端程式庫時提供協助：

1. 宣告 `array` 以保存一或多個時間序列深入解析彙總運算式：

   ```javascript
   var aes =  [];
   ```

1. 建置 1  到 n  個彙總運算式物件。 然後，將這些物件新增至彙總運算式陣列：

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **aggregateExpression 參數**

   | 參數 | 說明 | 範例 |
   | --------- | ----------- | ------- |
   | `predicateObject` | 資料篩選運算式 |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | 使用中量值的屬性名稱 | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | 所需的量值屬性彙總 | `['avg', 'min']` |
   | `searchSpan`      | 彙總運算式的持續時間和間隔大小 | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | 要作為分割依據的字串屬性 (選擇性：可為 null) | `{property: 'Station', type: 'String'}` |
   | `color`         | 所要轉譯物件的色彩 | `'pink'` |
   | `alias`           | 彙總運算式的易記名稱 | `'Factory3Temperature'` |
   | `contextMenuActions` | 要繫結至視覺效果中時間序列物件的動作陣列 (選擇性) | 如需詳細資訊，請參閱[快顯功能表](#pop-up-context-menus)。 |

1. 使用 TsiClient.Server API 呼叫時間序列深入解析查詢以要求彙總資料：

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **getAggregates 參數**

   | 參數 | 說明 | 範例 |
   | --------- | ----------- | ------- |
   | `token`     | 時間序列深入解析 API 的存取權杖 |  `authContext.getTsiToken()`<br />如需詳細資訊，請參閱[驗證](#authentication)。 |
   | `envFQDN`   | 時間序列深入解析環境的完整網域名稱 (FQDN) | 從 Azure 入口網站。 例如： `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com` 。 |
   | `aeTsxArray` | 時間序列深入解析查詢運算式的陣列 | 如先前所述使用 `aes` 變數：`aes.map(function(ae){return ae.toTsx()}`。 |

1. 將時間序列深入解析查詢所傳回的壓縮結果轉換為視覺效果的 JSON：

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. 使用 TsiClient.UX API 建立圖表控制項。 將其繫結至頁面上的其中一個 `<div>` 元素：

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. 使用轉換後的 JSON 資料物件填入圖表控制項，並在頁面上轉譯該控制項：

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="render-controls"></a>轉譯控制項

時間序列深入解析用戶端程式庫提供八個唯一的現成分析控制項：

* **折線圖**
* **圓形圖**
* **橫條圖**
* **heatmap**
* **階層控制項**
* **可存取的方格**
* **離散事件時間軸**
* **狀態轉換時間軸**

### <a name="line-chart-bar-chart-and-pie-chart-examples"></a>折線圖、橫條圖和圓形圖範例

請看一下用來轉譯一些標準圖表控制項的示範程式碼。 並注意用來建立這些控制項的程式設計模型和模式。 具體來說，請檢查 `// Example 3/4/5` 註解底下的 HTML，其轉譯了具有 HTML `id` 值 `chart3`、`chart4` 和 `chart5` 的控制項。

回想一下[網頁原始碼和結構一節](#page-source-and-structure)中的步驟 3，圖表控制項會在網頁上成列排列。 每個圖表控制項各有一個敘述性標題資料列。 在此範例中，所填入的三個圖表皆位於 `Multiple Chart Types From the Same Data` 標題 `<div>` 元素底下，並且繫結至該標題下的三個 `<div>` 元素：

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

接下來的 JavaScript 程式碼區段會使用稍早說明的模式：建置時間序列深入解析彙總運算式，使用這些運算式來查詢時間序列深入解析資料，然後轉譯三個圖表。 會用到三個來自 tsiClient.ux 命名空間的圖表類型：`LineChart`、`BarChart` 和 `PieChart`。 圖表類型可用來建立及轉譯個別的圖表。 這三個圖表全都能使用相同的彙總運算式資料 `transformedResult`：

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

三個圖表在轉譯時會呈現如下：

[![來自相同資料的多個圖表類型](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="learn-about-advanced-features"></a>了解進階功能

時間序列深入解析用戶端程式庫有數個額外功能，可讓您用來實作具創意性的資料視覺效果。

### <a name="states-and-events"></a>狀態和事件

進階功能可將狀態轉換和離散事件新增至圖表。 這項功能可用於醒目提示事件、警示和建立狀態切換 (例如開啟/關閉)。

請看一下 `// Example 10` 註解周圍的程式碼。 此程式碼會轉譯 `Line Charts with Multiple Series Types` 標題底下的線條控制項，並將其繫結至 HTML `id` 值為 `chart10` 的 `<div>` 元素。

下列步驟說明此程序：

1. 定義名為 `events4` 的結構，以保存要追蹤的狀態變更元素。此結構包含：

   * 名為 `Component States` 的字串索引鍵。
   * 代表狀態的值物件陣列。 每個物件各包含：
     * 包含 JavaScript ISO 時間戳記的字串索引鍵。
     * 包含狀態特性的陣列：色彩和描述。

1. 為 `Incidents` 定義 `events5` 結構，其保有要追蹤的事件元素陣列。陣列結構的圖形與針對 `events4` 所概述的結構相同。

1. 轉譯折線圖，並傳入兩個具有圖表選項參數的結構：`events:` 和 `states:`。 請注意其他用於指定 `tooltip:`、`theme:` 或 `grid:` 的選項參數。

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

用來表示事件的菱形標記/快顯視窗，與時間刻度上的彩色橫條/快顯視窗，皆表示狀態的變更：

[![具有多個序列類型的折線圖](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>快顯功能表

另一個進階功能是能夠建立自訂快顯功能表 (以滑鼠右鍵按一下快顯功能表)。 自訂快顯功能表可用來啟用應用程式範圍內的動作和邏輯上的後續步驟。

請看一下 `// Example 13/14/15` 註解周圍的程式碼。 此程式碼一開始會轉譯 `Line Chart with Context Menu to Create Pie/Bar Chart` 標題底下的折線圖。 此折線圖會繫結至 HTML `id` 值為 `chart13` 的 `<div>` 元素。

使用快顯功能表，折線圖就可讓您動態建立圓形圖和橫條圖，且繫結至識別碼為 `chart14` 和 `chart15` 的 `<div>` 元素。 圓形圖和橫條圖也都會使用快顯功能表來啟用自己的功能：分別能夠將圓形圖的資料複製到橫條圖，以及將橫條圖的資料列印至瀏覽器的主控台視窗。

下列步驟說明此程序：

1. 定義一系列的自訂動作。 每個動作都有一個包含一或多個元素的陣列。 每個元素分別定義單一快顯功能表項目：

   * `barChartActions`：此動作會定義圓形圖的快顯功能表，其包含一個元素來定義單一項目：
     * `name`：用於功能表項目的文字：「將參數列印至主控台」。
     * `action`：與功能表項目相關聯的動作。 此動作一律是匿名函式，會根據用來建立圖表的彙總運算式採用三個引數。 在此情況下，這些引數會寫入至瀏覽器的主控台視窗：
       * `ae`：彙總運算式陣列。
       * `splitBy`：`splitBy` 值。
       * `timestamp`：時間戳記。

   * `pieChartActions`：此動作會定義橫條圖的快顯功能表，其包含一個元素來定義單一項目。 圖形和結構描述與先前所述的 `barChartActions` 元素相同，但 `action` 函式極為不同：它會將橫條圖具現化並轉譯。 `ae` 引數會用來指定在執行階段於功能表項目開啟時所傳遞的彙總運算式陣列。 此函式也會對 `barChartActions` 快顯功能表設定 `ae.contextMenu` 屬性。
   * `contextMenuActions`：此動作會定義折線圖的快顯功能表，其包含三個元素來定義三個功能表項目。 每個元素的形狀和結構描述皆與先前所述的元素相同。 和 `barChartActions` 元素一樣，第一個項目會將三個函式引數寫入至瀏覽器主控台視窗。 與 `pieChartActions` 元素類似，第二的兩個項目會分別將圓形圖和橫條圖具現化與轉譯。 第二的兩個項目也會分別對 `pieChartActions` 和 `barChartActions` 快顯功能表設定其 `ae.contextMenu` 屬性。

1. 系統會將兩個彙總運算式推送到 `aes` 彙總運算式陣列。 這些運算式會為每個項目指定 `contextMenuActions` 陣列。 這些運算式會與折線圖控制項搭配使用。

1. 一開始只會轉譯折線圖，然後可在執行階段從中轉譯出圓形圖和橫條圖。

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

下列螢幕擷取畫面顯示這些圖表，以及其各自的快顯功能表。 系統會使用折線圖快顯功能表選項，以動態方式建立圓形圖和橫條圖。

[![有快顯功能表可供建立圓形圖和橫條圖的折線圖](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>筆刷

您可以使用筆刷來界定時間範圍，以定義縮放和瀏覽等動作。

用來說明筆刷的程式碼會顯示在 `Line Chart with Context Menu to Create Pie/Bar Chart` 範例中，以說明快顯功能表。

* 筆刷動作與快顯功能表類似，兩者都會定義筆刷的一系列自訂動作。 每個動作都有一個包含一或多個元素的陣列。 每個元素分別定義單一快顯功能表項目：
   * `name`：用於功能表項目的文字：「將參數列印至主控台」。
   * `action`：與功能表項目相關聯的動作，一律為匿名函式，且會採用兩個引數。 在此情況下，這些引數會寫入至瀏覽器的主控台視窗：
     * `fromTime`：筆刷選取項目的 `from` 時間戳記。
     * `toTime`：筆刷選取項目的 `to` 時間戳記。

* 筆刷動作會新增為另一個圖表選項屬性。 `brushContextMenuActions: brushActions` 屬性會傳遞至 `linechart.Render` 呼叫。

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![有快顯功能表可供使用比刷來建立圓形圖和橫條圖的折線圖](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="clean-up-resources"></a>清除資源

您現在已完成本教學課程，請清除您所建立的資源：

1. 從 [Azure 入口網站](https://portal.azure.com)的左側功能表中，選取 [所有資源]  ，找出 Azure 時間序列深入解析資源群組。
1. 選取 [刪除]  或個別移除每個資源，以刪除整個資源群組 (和其中包含的所有資源)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 登入並瀏覽時間序列深入解析應用程式範例和其來源
> * 使用時間序列深入解析 JavaScript 用戶端程式庫中的 API
> * 使用 JavaScript 來以時間序列深入解析資料建立和填入圖表控制項

時間序列深入解析應用程式範例會使用示範資料集。 若要了解如何建立自己的時間序列深入解析環境和資料集，請閱讀下列文章：

> [!div class="nextstepaction"]
> [教學課程：建立 Azure 時間序列深入解析環境](tutorial-create-populate-tsi-environment.md)

或者，檢視時間序列深入解析應用程式範例來源檔案：

> [!div class="nextstepaction"]
> [時間序列深入解析應用程式範例存放庫](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

閱讀時間序列深入解析用戶端 API 參考文件：

> [!div class="nextstepaction"]
> [時間序列深入解析 API 參考文件](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)
