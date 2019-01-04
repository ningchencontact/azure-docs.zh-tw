---
title: 將熱度圖層新增至 Azure 地圖服務 | Microsoft Docs
description: 如何將熱度圖層新增至 Javascript 地圖
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ec1343f85216171adac22f873f9be2e72bb4c282
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892906"
---
# <a name="add-a-heat-map-layer"></a>新增熱度圖層

熱度圖 (也稱為點密度圖) 是一種資料視覺效果，用來以色彩範圍表示資料密度。 這些圖通常用來顯示地圖上的資料「作用點」，而且十分適合用來轉譯大型的點資料集。  例如，若將地圖檢視內數以萬計的點轉譯成符號，可涵蓋大部分的地圖區域，但也會造成許多符號被其他符號遮住，因此很難取得更深入的資料見解。 不過，若將此相同資料集視覺化為熱度圖，就可更輕易地看到點資料最密集的地方，以及其他區域的相對密度。 有許多案例都使用了熱度圖。 以下提供一些範例：

* 溫度資料常常會轉譯為熱度圖，因為其可為兩個資料點之間的溫度提供近似值。
* 將噪音感應器的資料轉譯為熱度圖，不僅可顯示感應器所在的噪音程度，也可以針對隨著距離消散的噪音提供見解。 任何一個站台上的噪音等級可能都不高，不過，如果多個感應器的噪音涵蓋區域重疊時，就有可能造成此重疊區域有到較高的噪音等級，並且顯示在熱度圖中。
* 將包括速度的 GPS 追蹤視覺化為權重高度圖 (其中每個資料點的強度都以速度為基礎)，這是可快速看到車輛正在何處高速行駛的好方法。

> [!TIP]
> 根據預設，泡泡圖層會轉譯資料來源中所有幾何圖形的座標。 若要限制圖層，使其只轉譯點幾何特徵，請將圖層的 `filter` 屬性設為 `['==', '$type', 'Point']`

## <a name="add-a-heat-map-layer"></a>新增熱度圖層

若要將點資料來源轉譯為熱度圖，只需將您的資料來源傳送至 HeatMapLayer 類別的執行個體，然後將其新增至地圖，如下所示。

<br/>

<iframe height='500' scrolling='no' title='簡易熱度圖層' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/gQqdQB/'>簡易熱度圖層</a>。
</iframe>

在此範例中，每個熱度點在所有縮放層級上都有半徑為 10 的像素。 將熱度圖層新增至地圖時，此範例會將圖層插入標籤圖層下方。 這會建立較佳的使用者體驗，因為標籤會清楚地顯示在熱度圖上方。 此範例中的資料來自 [USGS 地震災害計畫 (USGS Earthquake Hazards Program)](https://earthquake.usgs.gov/)，其中包含的點代表過去 30 天內發生的重大地震。

## <a name="customizing-the-heat-map-layer"></a>自訂熱度圖層

前一個範例已藉由設定半徑和透明度選項來自訂熱度圖。 熱度圖層會提供數個自訂選項；

* `radius`：定義用來轉譯每個資料點的像素半徑。 半徑可設為固定數字或運算式。 將半徑設為可根據縮放層級設定半徑的運算式，會讓熱圖在地圖上顯示為具有代表一致空間區域的半徑。
* `color`：指定如何以色彩標示熱度圖。 熱度圖通常會使用漸層色彩調色盤，但如果您想要讓熱度圖看起來更像等高線資料，也可使用分階色彩調色盤。 這些色彩調色盤會定義最小密度值到最大密度值的顏色。 熱度圖的色彩值會指定為 `heatmap-density` 值上的的運算式。 漸層運算式中位於指數 0 的色彩和分階色彩中的預設色彩會用於表示沒資料的區域，或用來作為為背景色彩。 許多人偏好將此值設定為透明或半透明的黑色。 以下是色彩運算式的範例；

| 漸層色彩運算式 | 分階色彩運算式 | 
|---------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1.00, 'red'<br/>\] |   

* `opacity`：指定熱度圖層的透明度。
* `intensity`：對每個資料點的權重套用乘數，以增加熱度圖的整體強度。 這有助於讓資料點權重中的微小差異變得更為凸顯。
* `weight`：根據預設，所有資料點的權數為 1，因此所有資料點的權重都一樣。 加權選項會作為乘數使用，並可設定為數字或運算式。 如果數字設為數字 2，則等於是將每個資料點放在地圖上兩次，因此密度會加倍。 將加權選項設定為數字所呈現的熱度圖，與使用強度選項呈現的熱度圖相似。 不過，如果使用運算式，則每個資料點可以根據不同項目來加權，或是根據點屬性中的一些計量來加權。 以地震資料為例，每個資料點代表地震，而每個地震的重要計量為震度。 地震隨時都在發生，但大部分地震的震度低，甚至感覺不到。 在運算式中使用震度值來指派加權選項，可讓熱度圖上表示出更多較大的地震。
* 除了基底圖層選項；最小/最大縮放、可見度和篩選，另外還有用於更新資料來源的 `source` 選項，以及用於資料來源是向量圖格來源的 `source-layer` 選項。

以下是用來測試不同熱度圖層選項的工具。

<br/>

<iframe height='700' scrolling='no' title='熱度圖層選項' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/WYPaXr/'>熱度圖層選項</a>。
</iframe>

> [!TIP]
> 藉由啟用資料來源上的叢集功能，彼此接近的點會群組在一起，成為叢集點。 每個叢集的點計數可以作為熱度圖的加權運算式，以及大幅降低必須要轉譯的點數目。 叢集的點計數會儲存在點特徵的 point_count 屬性，如下所示。 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> 如果叢集的半徑只有幾個像素，則在視覺呈現上只會有小小的差異。 較大的半徑會將更多點群組到每個叢集中，並改善熱度圖的效果，但會有較明顯的差異。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

如需更多可新增至地圖的程式碼範例，請參閱下列文章：

> [!div class="nextstepaction"]
> [新增符號圖層](./map-add-pin.md)

