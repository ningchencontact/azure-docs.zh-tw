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
ms.openlocfilehash: 82a6d6b2af7df91696844b09fb7650c547cb6bd1
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59258856"
---
# <a name="add-a-heat-map-layer"></a>新增熱度圖層

熱度圖 (也稱為點密度圖) 是一種資料視覺效果，用來以色彩範圍表示資料密度。 這些圖通常用來顯示地圖上的資料「作用點」，而且十分適合用來轉譯大型的點資料集。  比方說，轉譯數以萬計的地圖檢視內的點做為符號，涵蓋大部分的對應區域，並會導致多重疊一，因此很難取得更深入了解資料的符號。 不過，若將此相同資料集視覺化為熱度圖，就可更輕易地看到點資料最密集的地方，以及其他區域的相對密度。 有許多案例都使用了熱度圖。 以下提供一些範例：

* 溫度資料常常會轉譯為熱度圖，因為其可為兩個資料點之間的溫度提供近似值。
* 將噪音感應器的資料轉譯為熱度圖，不僅可顯示感應器所在的噪音程度，也可以針對隨著距離消散的噪音提供見解。 任何一個站台上的噪音等級可能都不高，不過，如果多個感應器的噪音涵蓋區域重疊時，就有可能造成此重疊區域有到較高的噪音等級，並且顯示在熱度圖中。
* 視覺化 GPS 包括速度為每個資料點的濃度，基於速度加權的高度對應的追蹤是適合用來查看已加速車輛的位置。

> [!TIP]
> 根據預設，熱度圖圖層會轉譯資料來源中所有幾何圖形的座標。 若要限制圖層，使它只會轉譯點幾何功能設定`filter`的圖層屬性`['==', ['geometry-type'], 'Point']`或`['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`如果您想要包含 MultiPoint 的功能。

## <a name="add-a-heat-map-layer"></a>新增熱度圖層

若要將點資料來源轉譯為熱度圖，請將您的資料來源傳送至 `HeatMapLayer` 類別的執行個體，然後將其新增至地圖，如下所示。

<br/>

<iframe height='500' scrolling='no' title='簡易熱度圖層' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/gQqdQB/'>簡易熱度圖層</a>。
</iframe>

在此範例中，每個熱度點在所有縮放層級上都有半徑為 10 的像素。 將熱度圖地圖圖層加入至地圖中，此範例會建立較佳的使用者體驗，標籤會清楚地顯示熱量分佈圖上方標籤層下面的將其插入。 在此範例中的資料來自[USGS 地震危險程式](https://earthquake.usgs.gov/)和代表過去的 30 天內發生的重大地震。

## <a name="customizing-the-heat-map-layer"></a>自訂熱度圖層

前一個範例已藉由設定半徑和透明度選項來自訂熱度圖。 熱度圖層會提供數個自訂選項；

* `radius`：定義用來轉譯每個資料點的像素半徑。 半徑可設為固定數字或運算式。 使用運算式，就可以調整的縮放層級，來代表對應 (例如，5 英哩 radius) 上的一致空間區域會顯示為基礎的半徑。
* `color`：指定如何以色彩標示熱度圖。 色彩漸層熱度通常會使用，而且可以是達到`interpolate`運算式。 使用`step`來標示熱量分佈圖的色彩的運算式會以視覺化方式分成多個範圍，類似於分佈或雷達圖樣式對應的密度。 這些色彩調色盤會定義最小密度值到最大密度值的顏色。 熱度圖的色彩值會指定為 `heatmap-density` 值上的的運算式。 位於索引 0 內插補點運算式中的色彩或預設色彩是步驟運算式中，定義區域的色彩沒有任何資料而且可用來定義背景色彩。 許多人偏好將此值設定為透明或半透明的黑色。 以下是色彩運算式的範例；

| 插補色彩運算式 | 分階色彩運算式 | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25，' 綠色 '<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50，[黃色]，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75，'red'<br/>\] | 

* `opacity`：指定熱度圖層的透明度。
* `intensity`：適用於以增加整體的強度的熱度圖，每個資料點的加權乘數，有助於讓小型差異變得更容易視覺化的資料點的加權。
* `weight`：根據預設，所有資料點的權數為 1，因此所有資料點的權重都一樣。 加權選項會作為乘數使用，並可設定為數字或運算式。 如果數字設為權重，假設為 2，則等於是將每個資料點放在地圖上兩次，因此密度會變成兩倍。 將加權選項設定為數字所呈現的熱度圖，與使用強度選項呈現的熱度圖相似。 不過，如果使用運算式，則每個資料點的加權可以根據每個資料點的內容。 以地震資料作為例子，每個資料點都代表一次地震。 每個地震資料點的一個重要計量便是震度值。 地震隨時都在發生，但大部分地震的震度低，甚至感覺不到。 在運算式中使用的大小值來指派權數給每個資料點會允許更重要的地震，更好的熱度圖內部表示。
* 除了基底圖層選項 (最小/最大縮放、可見度和篩選) 之外，另外還有用於更新資料來源的 `source` 選項，以及用於資料來源是向量圖格來源的 `source-layer` 選項。

以下是用來測試不同熱度圖層選項的工具。

<br/>

<iframe height='700' scrolling='no' title='熱度圖層選項' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/WYPaXr/'>熱度圖層選項</a>。
</iframe>

## <a name="consistent-zoomable-heat-map"></a>一致的可熱圖

根據預設，在熱度圖地圖圖層中呈現的資料點的半徑會有固定的像素半徑，所有的縮放層級。 如已放大地圖放在一起的資料彙總與熱度圖地圖圖層看起來不同。 A`zoom`運算式可用來調整每個縮放層級的半徑，使得每個資料點涵蓋相同的實體區域的地圖。 靜態更一致，這會讓尋找熱度圖地圖圖層。 對應的每個縮放層級有兩倍的像素垂直和水平與先前的縮放層級。 調整 radius，使它與每個縮放層級會加倍，會建立熱度圖，在所有的縮放層級上一致的外觀。 這可藉由使用`zoom`基底 2`exponential interpolation`運算式，如下列範例所示。 放大地圖以查看熱量分佈圖的縮放層級進行的調整方式。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="一致的可熱圖" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=light&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱畫筆<a href='https://codepen.io/azuremaps/pen/OGyMZr/'>一致對熱度</a>由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>

> [!TIP]
> 藉由啟用資料來源上的叢集功能，彼此接近的點會群組在一起，成為叢集點。 每個叢集的點計數可以作為熱度圖的加權運算式，以及大幅降低必須要轉譯的點數目。 叢集的點計數會儲存在`point_count`點功能，如下所示的屬性。 
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

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)