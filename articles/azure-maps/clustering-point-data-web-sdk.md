---
title: Azure 地圖服務中的群集點資料 |Microsoft Docs
description: 如何在 Web SDK 中叢集點資料
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 69e95a9e6c76da5d502314a7190e99fc10e968f7
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639075"
---
# <a name="clustering-point-data"></a>群集點資料

視覺化地圖上的許多資料點時, 點會彼此重迭, 地圖看起來很雜亂, 並且變得難以查看和使用。 點資料的叢集可以用來改善此使用者體驗。 群集點資料是結合彼此接近的點資料, 並將它們以單一叢集資料點表示在地圖上的程式。 當使用者放大地圖時, 叢集會分解成個別的資料點。

## <a name="enabling-clustering-on-a-data-source"></a>在資料來源上啟用叢集

將選項設定為 [ `DataSource` `cluster` true], 即可輕鬆地在類別上啟用叢集。 此外, 您可以使用來設定用`clusterRadius`來選取附近點以合併到叢集的圖元半徑, 並且可以`clusterMaxZoom`使用選項指定縮放層級來停用叢集邏輯。 以下是如何在資料來源中啟用群集的範例。

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15 
});
```

> [!TIP]
> 如果兩個資料點彼此接近, 則不論使用者如何放大, 叢集都可能永遠不會中斷。 若要解決此情況, 您可以`clusterMaxZoom`設定資料來源的選項, 以在縮放層級指定, 以停用叢集邏輯並只顯示所有專案。

`DataSource`類別也具有與群集相關的下列方法:

| 方法 | 傳回類型 | 描述 |
|--------|-------------|-------------|
| getClusterChildren (clusterId: number) | 承諾&lt;陣列&lt;功能幾何&lt;, 任何&gt;圖形\|&gt;&gt; | 在下一個縮放層級上, 抓取給定叢集的子系。 這些子系可以是圖案和 subclusters 的組合。 Subclusters 將會是屬性符合 ClusteredProperties 的功能。 |
| getClusterExpansionZoom (clusterId: number) | 承諾&lt;數量&gt; | 計算叢集將開始擴充或中斷的縮放層級。 |
| getClusterLeaves (clusterId: number, limit: number, offset: number) | 承諾&lt;陣列&lt;功能幾何&lt;, 任何&gt;圖形\|&gt;&gt; | 抓取叢集中的所有點。 設定以傳回點的子集, 並`offset`使用來逐頁流覽點。 `limit` |

## <a name="display-clusters-using-a-bubble-layer"></a>使用反升層顯示群集

反升層是轉譯叢集點的絕佳方式, 因為您可以輕鬆地調整半徑, 並根據叢集中的點數目, 使用運算式來變更色彩。 使用反升層顯示叢集時, 您也應該使用個別層來呈現叢集資料點。 此外, 也可以在氣泡上顯示叢集的大小, 這通常很好用。 具有文字且沒有圖示的符號圖層可用來達成此行為。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="基本反升層叢集" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 的畫筆基本反升<a href='https://codepen.io/azuremaps/pen/qvzRZY/'>層群集</a>。
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>使用符號圖層顯示群集

使用符號圖層視覺化點資料時, 預設會自動隱藏彼此重迭的符號, 以建立更清楚的體驗, 不過, 如果您想要查看地圖上資料點的密度, 這可能不是想要的體驗。 設定 [符號圖層`iconOptions` ] 屬性的`true` 選項可停用此體驗,但會導致顯示所有符號。`allowOverlap` 使用群集可讓您查看所有資料的密度, 同時建立絕佳的使用者體驗。 在此範例中, 自訂符號將用來代表叢集和個別資料點。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="叢集符號層" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 的畫筆叢集<a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>符號層</a>。
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>群集和熱度圖層

熱度圖是在地圖上顯示資料密度的絕佳方式。 此視覺效果可以單獨處理大量的資料點, 但如果資料點已叢集化, 而叢集大小是用來做為熱度圖的權數, 則可以處理更多的資料。 將熱度圖層的`['get', 'point_count']` 選項設定為,以達到此目的。`weight` 當叢集半徑較小時, 熱度圖會看起來幾乎與使用叢集資料點的熱度圖相同, 但效能會更好。 不過, 叢集半徑愈小, 熱度圖的精確度就愈高, 但效能效益也比較差。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="叢集加權熱度圖" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 的畫筆叢集<a href='https://codepen.io/azuremaps/pen/VRJrgO/'>加權熱度圖</a>。
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>叢集資料點上的滑鼠事件

當滑鼠事件發生在包含叢集資料點的圖層上時, 叢集資料點會以 GeoJSON 點功能物件的形式傳回至事件。 此點功能將具有下列屬性:

| 屬性名稱 | Type | 描述 |
|---------------|------|-------------|
| 叢集 | boolean | 指出功能是否代表叢集。 |
| cluster_id | string | 可搭配資料來源`getClusterExpansionZoom`、 `getClusterChildren`和`getClusterLeaves`方法使用之叢集的唯一識別碼。 |
| point_count | 號 | 叢集包含的點數。 |
| point_count_abbreviated | string | 縮寫`point_count`值的字串 (如果長度很長)。 (例如, 4000 變成 4K) |

這個範例會取得呈現叢集點的反升圖層, 並加入一個在觸發、計算和縮放地圖時的 click 事件, 以使用`getClusterExpansionZoom` `DataSource`類別的方法和`cluster_id`已按下之叢集資料點的屬性。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="叢集 getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 的 < 畫筆叢集<a href='https://codepen.io/azuremaps/pen/moZWeV/'>getClusterExpansionZoom</a> 。
</iframe>

## <a name="display-cluster-area"></a>顯示叢集區域 

叢集代表的點資料會散佈在某個區域。 在此範例中, 當滑鼠停留在叢集上時, 會使用它所包含的個別資料點 (葉子) 來計算凸殼, 並顯示在地圖上以顯示區域。 您可以使用`getClusterLeaves`方法, 從資料來源中取出包含在叢集中的所有點。 凸殼是一種多邊形, 會包裝一組點, 例如彈性的寬線, 而且可以使用`atlas.math.getConvexHull`方法來計算。

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="叢集區域凸殼" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 的畫筆叢集<a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>區域凸</a>殼。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [DataSource 類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions 物件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [阿特拉斯. math 命名空間](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

請參閱程式碼範例，將功能新增至您的應用程式：

> [!div class="nextstepaction"]
> [新增泡泡圖層](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [新增符號圖層](map-add-pin.md)

> [!div class="nextstepaction"]
> [新增熱度圖圖層](map-add-heat-map-layer.md)
