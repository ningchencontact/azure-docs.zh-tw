---
title: 將熱度圖層新增至 Azure 地圖服務 | Microsoft Docs
description: 如何將熱度圖層新增至 Javascript 地圖
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 85f184603cdcadce6bf750db5765f32a0735453d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639030"
---
# <a name="add-a-heat-map-layer"></a>新增熱度圖層

熱度圖 (也稱為點密度圖) 是一種資料視覺效果，用來以色彩範圍表示資料密度。 這些圖通常用來顯示地圖上的資料「作用點」，而且十分適合用來轉譯大型的點資料集。  例如, 在地圖視圖中將數十個點轉譯為符號, 涵蓋大部分的對應區域, 並會導致許多符號彼此重迭, 因此難以取得資料的深入解析。 不過，若將此相同資料集視覺化為熱度圖，就可更輕易地看到點資料最密集的地方，以及其他區域的相對密度。 有許多案例都使用了熱度圖。 以下提供一些範例：

* 溫度資料常常會轉譯為熱度圖，因為其可為兩個資料點之間的溫度提供近似值。
* 將噪音感應器的資料轉譯為熱度圖，不僅可顯示感應器所在的噪音程度，也可以針對隨著距離消散的噪音提供見解。 任何一個站台上的噪音等級可能都不高，不過，如果多個感應器的噪音涵蓋區域重疊時，就有可能造成此重疊區域有到較高的噪音等級，並且顯示在熱度圖中。
* 將包含速度的 GPS 追蹤視覺化為加權高度地圖, 其中每個資料點的強度都以速度為基礎, 這是查看車輛正在何處加速的絕佳方式。

> [!TIP]
> 根據預設，熱度圖圖層會轉譯資料來源中所有幾何圖形的座標。 若要限制圖層, 使其只轉譯點幾何特徵, 請`filter`將圖層的屬性`['==', ['geometry-type'], 'Point']`設`['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`為, 如果您也想要包含 MultiPoint 功能, 請將它設定為。

## <a name="add-a-heat-map-layer"></a>新增熱度圖層

若要將點資料來源轉譯為熱度圖，請將您的資料來源傳送至 `HeatMapLayer` 類別的執行個體，然後將其新增至地圖，如下所示。

<br/>

<iframe height='500' scrolling='no' title='簡易熱度圖層' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/gQqdQB/'>簡易熱度圖層</a>。
</iframe>

在此範例中，每個熱度點在所有縮放層級上都有半徑為 10 的像素。 將熱度圖層新增至地圖時, 此範例會將其插入至標籤圖層下方, 以建立更好的使用者體驗, 因為標籤會清楚地顯示在熱度圖上方。 此範例中的資料來源自于[USGS 地震風險計畫](https://earthquake.usgs.gov/), 並代表過去30天內發生的重大地震。

## <a name="customizing-the-heat-map-layer"></a>自訂熱度圖層

前一個範例已藉由設定半徑和透明度選項來自訂熱度圖。 熱度圖層會提供數個自訂選項；

* `radius`:定義用來轉譯每個資料點的像素半徑。 半徑可設為固定數字或運算式。 使用運算式時, 您可以根據縮放層級來調整半徑, 這會顯示在地圖上的一致空間區域 (例如, 5 英里半徑)。
* `color`:指定如何以色彩標示熱度圖。 色彩漸層通常用於熱度圖, 並可`interpolate`透過運算式來達成。 `step`使用運算式來上色熱度圖, 會以視覺化方式將密度分解成類似輪廓或雷達圖樣式地圖的範圍。 這些色彩調色盤會定義最小密度值到最大密度值的顏色。 熱度圖的色彩值會指定為 `heatmap-density` 值上的的運算式。 插補運算式中位於索引0的色彩, 或步驟運算式的預設色彩, 會定義沒有任何資料並可用來定義背景色彩的區域色彩。 許多人偏好將此值設定為透明或半透明的黑色。 以下是色彩運算式的範例；

| 插補色彩運算式 | 分階色彩運算式 | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25、「綠色」、<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50、' 黃色 '、<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75、' red '<br/>\] | 

* `opacity`:指定熱度圖層的透明度。
* `intensity`:將乘數套用到每個資料點的權數, 以增加熱度圖的整體濃度, 並協助您更輕鬆地將資料點權數的細微差異變得更容易視覺化。
* `weight`:根據預設，所有資料點的權數為 1，因此所有資料點的權重都一樣。 加權選項會作為乘數使用，並可設定為數字或運算式。 如果數字設為權重，假設為 2，則等於是將每個資料點放在地圖上兩次，因此密度會變成兩倍。 將加權選項設定為數字所呈現的熱度圖，與使用強度選項呈現的熱度圖相似。 不過, 如果使用運算式, 每個資料點的權數可以根據每個資料點的屬性。 以地震資料作為例子，每個資料點都代表一次地震。 每個地震資料點的一個重要計量便是震度值。 地震隨時都在發生，但大部分地震的震度低，甚至感覺不到。 使用運算式中的 [大小] 值, 將權數指派給每個資料點, 可讓熱度圖中更明顯地呈現更高的地震。
* 除了基底圖層選項 (最小/最大縮放、可見度和篩選) 之外，另外還有用於更新資料來源的 `source` 選項，以及用於資料來源是向量圖格來源的 `source-layer` 選項。

以下是用來測試不同熱度圖層選項的工具。

<br/>

<iframe height='700' scrolling='no' title='熱度圖層選項' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/WYPaXr/'>熱度圖層選項</a>。
</iframe>

## <a name="consistent-zoomable-heat-map"></a>一致的可縮放熱度圖

根據預設, 熱度圖圖層中轉譯之資料點的半徑對於所有縮放層級都有固定圖元半徑。 因為地圖會將資料匯總放大, 而且熱度圖層看起來會不同。 `zoom`運算式可以用來調整每個縮放層級的半徑, 讓每個資料點都涵蓋對應的相同實體區域。 這會讓熱度圖層看起來更為靜態且一致。 地圖的每個縮放層級都會以垂直和水準方式, 與上一個縮放層級相同的圖元數倍。 調整半徑, 讓每個縮放層級加倍, 會建立在所有縮放比例上看起來一致的熱度圖。 這可以透過使用具有基底`zoom` 2 `exponential interpolation`運算式的來完成, 如下列範例所示。 縮放地圖以查看熱度圖如何隨著縮放層級調整。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="一致的可縮放熱度圖" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=light&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 的「手寫筆<a href='https://codepen.io/azuremaps/pen/OGyMZr/'>一致可縮放熱度圖</a>」。
</iframe>

> [!TIP]
> 藉由啟用資料來源上的叢集功能，彼此接近的點會群組在一起，成為叢集點。 每個叢集的點計數可以作為熱度圖的加權運算式，以及大幅降低必須要轉譯的點數目。 叢集的點計數會儲存在點特徵的`point_count`屬性中, 如下所示。 
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