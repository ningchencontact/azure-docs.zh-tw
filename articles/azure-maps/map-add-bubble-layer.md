---
title: 將泡泡圖層新增至 Azure 地圖服務 | Microsoft Docs
description: 如何將泡泡圖層新增至 Javascript 地圖
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 516e4f35c88ae9c0e2d63e8a4ee40eb57c05ac29
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639032"
---
# <a name="add-a-bubble-layer-to-a-map"></a>將泡泡圖層新增至地圖

本文會說明如何將資料來源中的點資料轉譯為地圖上的泡泡圖層。 泡泡圖層會將點轉譯為地圖上像素半徑固定的圓圈。 

> [!TIP]
> 根據預設，泡泡圖層會轉譯資料來源中所有幾何圖形的座標。 若要限制圖層, 使其只轉譯點幾何特徵, `filter`請將圖層的`['==', ['geometry-type'], 'Point']`屬性`['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`設為, 或者, 如果您也想要包含 MultiPoint 功能, 則設定為。

## <a name="add-a-bubble-layer"></a>新增泡泡圖層

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

在第二個程式碼區塊中，[Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 物件陣列會加以定義，並新增至 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 物件。

[泡泡圖層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)會將[資料來源](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)中包裝的點式資料轉譯為地圖上的圓圈。 最後一個程式碼區塊會建立泡泡圖層，並將其新增至地圖。 請在 [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions) 查看泡泡圖層的屬性。

Point 物件 (資料來源) 及泡泡圖層會在[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函式內建立並新增至地圖，以確保圓圈會在地圖完全載入後顯示。

## <a name="show-labels-with-a-bubble-layer"></a>顯示標籤與泡泡圖層

<iframe height='500' scrolling='no' title='MultiLayer DataSource' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

上述程式碼會示範如何視覺化和標記地圖上的資料。 上述第一個程式碼區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會建立 [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 物件。 然後使用 [data source](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別建立資料來源物件，並將點新增至資料來源。

[泡泡圖層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)會將[資料來源](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)中包裝的點式資料轉譯為地圖上的圓圈。 第三個程式碼區塊會建立泡泡圖層，並將其新增至地圖。 請在 [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions) 查看泡泡圖層的屬性。

[符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的點式資料轉譯為地圖上的符號。 最後一個程式碼區塊會在地圖上建立及新增符號圖層，以轉譯泡泡的文字標籤。 請在 [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions) 上查看符號圖層的屬性。

資料來源及圖層會在[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函式內建立並新增至地圖，以確保資料會在地圖完全載入後顯示。

## <a name="customize-a-bubble-layer"></a>自訂泡泡圖層

泡泡圖層只有少數樣式選項。 以下是可讓您試用這些選項的工具。

<br/>

<iframe height='700' scrolling='no' title='泡泡圖層選項' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/eQxbGm/'>泡泡圖層選項</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [新增符號圖層](map-add-pin.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)