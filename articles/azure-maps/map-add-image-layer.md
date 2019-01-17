---
title: 在 Azure 地圖服務中新增影像圖層 | Microsoft Docs
description: 如何在 Javascript 地圖中新增影像圖層
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5396fefca3a60dea7a503f8b4e84cc575753ea30
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229110"
---
# <a name="add-an-image-layer-to-a-map"></a>在地圖中新增影像圖層

本文說明如何在地圖上的一組固定座標覆蓋影像。 在許多情況下，地圖上會覆蓋影像。 以下是幾種常會覆蓋在地圖上的影像類型範例：

* 無人機所擷取的影像。
* 建物平面圖。
* 歷史性或其他特製化地圖影像。
* 施工現場的藍圖。
* 氣象雷達影像。

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) 可讓您輕鬆快速地在地圖上覆蓋影像。 不過，如果影像很大，瀏覽器可能會很難載入它。 在此情況下，請考慮將影像分拆成小影像，再將小影像載入地圖作為 [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)。

## <a name="add-an-image-layer"></a>新增映像圖層

此範例說明如何在地圖上覆蓋 [1922 年紐澤西紐瓦克地圖](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg)的影像。

<br/>

<iframe height='500' scrolling='no' title='簡單的影像圖層' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/eQodRo/'>簡單的影像圖層</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

程式碼的第二個區塊會建立 [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)，所用方法是傳遞影像 URL 和四個角落的座標 (格式為 `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`)。

## <a name="import-a-kml-ground-overlay"></a>匯入 KML Ground Overlay

此範例說明如何在地圖上覆蓋 KML Ground Overlay 資訊來作為影像圖層。 KML Ground Overlay 會提供北、南、東和西四個座標和逆時針方向的旋轉 (因為影像圖層預期要有影像四個角落的座標)。 此範例中的 KML Ground Overlay 是沙特爾主教座堂，其來源是 [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)。

<br/>

<iframe height='500' scrolling='no' title='KML Ground Overlay 作為影像圖層' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML Ground Overlay 作為影像圖層</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

上述程式碼會使用 [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) 類別的靜態 `getCoordinatesFromEdges` 函式，透過 KML Ground Overlay 中的北、南、東和西和旋轉等資訊來計算影像的四個角落。


## <a name="customize-an-image-layer"></a>自訂影像圖層

影像圖層有許多樣式選項。 以下是可讓您試用這些選項的工具。

<br/>

<iframe height='700' scrolling='no' title='影像圖層選項' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>影像圖層選項</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [新增圖格圖層](./map-add-tile-layer.md)