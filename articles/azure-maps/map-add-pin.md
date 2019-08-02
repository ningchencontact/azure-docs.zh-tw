---
title: 將符號圖層新增至 Azure 地圖服務 | Microsoft Docs
description: 如何將符號新增至 Javascript 地圖
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ba5d5d3aaa6a83dbcc5e5072872bca0fcd22bbf9
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638716"
---
# <a name="add-a-symbol-layer-to-a-map"></a>將符號圖層新增至地圖

本文會說明如何將資料來源中的點資料轉譯為地圖上的符號圖層。 符號圖層會使用 WebGL 來轉譯, 並支援比 HTML 標籤更大的點集, 但不支援傳統 CSS 和 HTML 專案來進行樣式設定。  

> [!TIP]
> 根據預設，符號圖層會轉譯資料來源中所有幾何圖形的座標。 若要限制圖層, 使其只轉譯點幾何特徵, `filter`請將圖層的`['==', ['geometry-type'], 'Point']`屬性`['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`設為, 或者, 如果您也想要包含 MultiPoint 功能, 則設定為。

## <a name="add-a-symbol-layer"></a>新增符號圖層

<iframe height='500' scrolling='no' title='切換釘選位置' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>切換釘選位置</a>。
</iframe>

上述第一個程式碼區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

在第二個程式碼區塊中，則會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件。 包含[點](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)幾何的 [特徵] 是由[圖形](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)類別包裝，使其更輕鬆地更新，而後建立並新增至資料來源。

第三個程式碼區塊會建立[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)，並使用 shape 類別的 [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 方法，透過滑鼠點選來更新點的座標。

[符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的點式資料轉譯為地圖上的符號。  系統會建立資料來源、click 事件接聽程式和符號層, 並將其新增至[事件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)接聽程式`ready`函式內的對應, 以確保在地圖載入並可供存取之後, 才會顯示該點。

> [!TIP]
> 根據預設, 對於效能, 符號層會藉由隱藏重迭的符號來優化符號的轉譯。 當您放大隱藏符號時, 就會顯示出來。 若要停用此功能並隨時轉譯所有符號, 請將`allowOverlap` `iconOptions`選項的屬性設定為`true`。

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>將自訂圖示新增至符號圖層

使用 WebGL 來呈現符號圖層。 所有這類資源 (例如圖示影像) 必須載入 WebGL 內容中。 這個範例示範如何將自訂圖示新增至地圖資源, 然後用它來呈現地圖上具有自訂符號的點資料。 符號圖層的 `textField` 屬性需要指定運算式。 在此情況下, 我們想要轉譯溫度屬性, 但因為它是數位, 所以必須轉換成字串。 此外, 我們想要將 "° F" 附加至該檔案。 運算式可以用來執行此動作;`['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='自訂符號影像圖示' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>自訂符號影像圖示</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="customize-a-symbol-layer"></a>自訂符號圖層 

符號圖層有許多可用的樣式選項。 以下是用來測試各種樣式選項的工具。

<br/>

<iframe height='700' scrolling='no' title='符號圖層選項' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/PxVXje/'>符號圖層選項</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TexTOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [新增快顯](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [新增圖形](map-add-shape.md)

> [!div class="nextstepaction"]
> [新增泡泡圖層](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [新增 HTML 標記](map-add-bubble-layer.md)
