---
title: 在 Azure 地圖服務中新增 HTML 標記 | Microsoft Docs
description: 如何在 Javascript 地圖中新增 HTML 標記
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 21689cf7fe3df7ca7e362ba49824fadd5daaa3ea
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888761"
---
# <a name="add-html-markers-to-the-map"></a>在地圖中新增 HTML 標記

本文說明如何在地圖中新增自訂 HTML (例如影像檔) 來作為 HTML 標記。

> [!NOTE]
> HTML 標記不會連線至資料來源。 相反地，位置資訊會直接新增至標記中，標記則會新增至地圖的 `markers` 屬性，即 [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)。

> [!IMPORTANT]
> Azure 地圖服務 Web 控制項中的大多數圖層會使用 WebGL 來進行轉譯，HTML 標記則與之不同，會使用傳統的 DOM 元素來進行轉譯。 因此，新增了頁面的 HTML 標記越多，其中的 DOM 元素就會越多。 在新增幾百個 HTML 標記之後，效能就會下降。 因此，對於規模較大的資料集，請考慮將資料叢集化，或使用「符號」或「泡泡」圖層。

## <a name="add-an-html-marker"></a>新增 HTML 標記

HtmlMarker 類別有預設樣式。 若要自訂標記，請設定標記的色彩和文字選項。 HtmlMarker 類別的預設樣式是 SVG 範本，具有色彩和文字預留位置。 若要快速自訂，請在 HtmlMarker 選項中設定色彩和文字屬性。 

<br/>

<iframe height='500' scrolling='no' title='在地圖中新增 HTML 標記' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>在地圖中新增 HTML 標記</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構對應物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會使用 [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 類別的 [markers](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) 屬性，將 [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) 新增至地圖。 HtmlMarker 會在[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函式內新增至地圖，以確保其會在地圖完全載入後顯示。

## <a name="create-svg-templated-html-marker"></a>建立 SVG 樣板化 HTML 標記

HTML 標記的預設 `htmlContent` 是內含預留位置 `{color}` 和 `{text}` 的 SVG 範本。 您可以建立自訂 SVG 字串，並在 SVG 中新增同樣的預留位置，讓標記的 `color` 和 `text` 選項的設定更新 SVG 中的這些預留位置。

<br/>

<iframe height='500' scrolling='no' title='HTML 標記與自訂 SVG 範本' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>HTML 標記與自訂 SVG 範本</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="add-a-css-styled-html-marker"></a>新增 CSS 樣式的 HTML 標記

HTML 標記的好處之一是您可以使用 CSS 來實現許多完美的自訂功能。 在此範例中，HtmlMarker 的內容是由 HTML 和 CSS 所組成，其會建立可就定位和有脈衝波的動畫圖釘。

<br/>

<iframe height='500' scrolling='no' title='HTML DataSource' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="draggable-html-markers"></a>可拖曳的 HTML 標記

此範例說明如何建立可拖曳的 HTML 標記。 HTML 標記支援 `drag`、`dragstart` 和 `dragend` 事件。

<br/>

<iframe height='500' scrolling='no' title='可拖曳的 HTML 標記' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>可拖曳的 HTML 標記</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>在 HTML 標記中新增滑鼠事件

這些範例會說明如何藉由在標記的 HTML 內容中新增標準 JavaScript 事件，而在 HTML 標記中新增滑鼠事件。 

如果標記的 `htmlContent` 是 DOM 元素 (div, img...)，您可以直接在其中新增事件。 

<br/>

<iframe height='500' scrolling='no' title='在 HTML 標記中新增滑鼠事件' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>在 HTML 標記中新增滑鼠事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

如果 `htmlContent` 是字串 (也就是類似預設值)，您可以建立 DOM 元素並於其中新增 innerHTML 字串，然後新增 DOM 元素 htmlContent。 the like the default), you can create a DOM element and add the string as innerHTML to it, then add the DOM element as the htmlContent. 如果 `htmlContent` 是樣板化字串，您必須先尋找/取代字串中的 `{text}` 和 `{color}` 值。

<br/>

<iframe height='500' scrolling='no' title='在預設的 HTML 標記中新增滑鼠事件' src='//codepen.io/azuremaps/embed/ZmZLBa/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/ZmZLBa/'>在預設的 HTML 標記中新增滑鼠事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

如需更多可新增至地圖的程式碼範例，請參閱下列文章：

> [!div class="nextstepaction"]
> [新增符號圖層](./map-add-pin.md)

> [!div class="nextstepaction"]
> [新增泡泡圖層](./map-add-bubble-layer.md)