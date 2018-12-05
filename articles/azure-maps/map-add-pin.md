---
title: 使用 Azure 地圖服務新增符號和標記 | Microsoft Docs
description: 如何將符號和標記新增至 Javascript 地圖
author: walsehgal
ms.author: v-musehg
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c56ac35f49c364b7b0f2ad26b82b178411419414
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2018
ms.locfileid: "52282680"
---
# <a name="add-symbols-and-markers-to-a-map"></a>在地圖新增符號和標記

此文章會示範如何使用資料來源在地圖中新增符號和標記。

## <a name="add-a-symbol-marker"></a>新增符號標記

<iframe height='500' scrolling='no' title='切換釘選位置' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>切換釘選位置</a>。
</iframe>

上述第一個程式碼區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

在第二個程式碼區塊中，則會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件。 然後，點就會建立並新增至資料來源。 點是 [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 的[功能](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)。

第三個程式碼區塊會建立[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)，並使用 shape 類別的 [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest#setcoordinates) 方法，透過滑鼠點選來更新點的座標。

[符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的點式資料轉譯為地圖上的符號。  資料來源 (點選事件接聽程式) 及符號圖層會在[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函式內建立並新增至地圖，以確保點會在地圖完全載入後顯示。

## <a name="add-a-custom-symbol"></a>新增自訂符號

<iframe height='500' scrolling='no' title='HTML DataSource' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構對應物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會使用 [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 類別的 [markers](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) 屬性，將 [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) 新增至地圖。 HtmlMarker 會在[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函式內新增至地圖，以確保其會在地圖完全載入後顯示。

## <a name="add-bubble-markers"></a>新增泡泡標記

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

在第二個程式碼區塊中，會定義位置陣列及建立 [MultiPoint](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.multipoint?view=azure-iot-typescript-latest) 物件。 接著使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別建立資料來源物件，並將 MultiPoint 物件新增至資料來源。

[泡泡圖層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)會將[資料來源](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)中包裝的點式資料轉譯為地圖上的圓圈。 最後一個程式碼區塊會建立泡泡圖層，並將其新增至地圖。 請在 [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions) 查看泡泡圖層的屬性。

MultiPoint 物件 (資料來源) 及泡泡圖層會在[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函式內建立並新增至地圖，以確保圓形會在地圖完全載入後顯示。

## <a name="add-bubble-markers-with-label"></a>新增具有標籤的泡泡標記

<iframe height='500' scrolling='no' title='MultiLayer DataSource' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

上述程式碼會示範如何視覺化和標記地圖上的資料。 上述第一個程式碼區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會建立 [point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 物件。 然後使用 [data source](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別建立資料來源物件，並將點新增至資料來源。

[泡泡圖層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)會將[資料來源](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)中包裝的點式資料轉譯為地圖上的圓圈。 第三個程式碼區塊會建立泡泡圖層，並將其新增至地圖。 請在 [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions) 查看泡泡圖層的屬性。

[符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的點式資料轉譯為地圖上的符號。 最後一個程式碼區塊會在地圖上建立及新增符號圖層，以轉譯泡泡的文字標籤。 請在 [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions) 上查看符號圖層的屬性。

資料來源及圖層會在[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函式內建立並新增至地圖，以確保資料會在地圖完全載入後顯示。


## <a name="next-steps"></a>後續步驟

深入了解此文章使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [新增快顯](./map-add-popup.md)

> [!div class="nextstepaction"]
> [新增圖形](./map-add-shape.md)