---
title: 使用 Azure 地圖服務新增快顯 | Microsoft Docs
description: 如何在 JavaScript 地圖上新增快顯
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 92d44ef3d0db8e93d4babd7441238c7fa105dbd5
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638998"
---
# <a name="add-a-popup-to-the-map"></a>在地圖上新增快顯

本文說明如何在地圖上的某個點新增快顯。

## <a name="understand-the-code"></a>了解程式碼

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='使用 Azure 地圖服務新增快顯' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>使用 Azure 地圖服務新增快顯</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。 它也會建立要在快顯中顯示的 HTML 內容。

第二個程式碼區塊會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件。 點是 [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 類別的[功能](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)。 接著會建立具有名稱和描述屬性的點物件，並新增至資料來源。

[符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的點式資料轉譯為地圖上的符號。  在第三個程式碼區塊中會建立符號層。 資料來源會新增至符號層，然後新增至地圖。

程式碼的第四個區塊會透過 `new atlas.Popup()` 建立[快顯物件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)。 position 和 pixelOffset 等快顯屬性都屬於 [PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions)。 PopupOptions 可定義在快顯建構函式中或透過快顯類別的 [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) 函式來定義。 接著會建立符號層的 `mouseover` 事件接聽程式。

最後一個程式碼區塊會建立由 `mouseover` 事件接聽程式所觸發的函式。 它會設定快顯的內容和屬性，並將快顯物件新增至地圖。

## <a name="reusing-a-popup-with-multiple-points"></a>重複使用具有多個點的快顯

當您有很多點，但只想要一次顯示一個快顯時，最好的方法是建立一個快顯，並重複使用它，而不是為每個點特徵建立快顯。 如果您這麼做，應用程式所建立的 DOM 元素數目就會大幅減少從而提升效能。 此範例會建立 3 個點特徵。 如果您對任何一個特徵按一下，便會顯示快顯，內有該點特徵的內容。

<br/>

<iframe height='500' scrolling='no' title='重複使用具有多個釘選的快顯' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>重複使用具有多個釘選的快顯</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

請參閱下列推薦文章，了解完整程式碼範例：

> [!div class="nextstepaction"]
> [新增符號圖層](./map-add-pin.md)

> [!div class="nextstepaction"]
> [新增 HTML 標記](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [新增圖形](./map-add-shape.md)