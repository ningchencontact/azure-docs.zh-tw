---
title: 叢集在 Azure 地圖服務中的點資料 |Microsoft Docs
description: 如何叢集化 Web SDK 中的點資料
author: rbrundritt
ms.author: richbrun
ms.date: 03/27/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: d4dc6f0c8fd2dff74a1997c9dca5a31abc70c03a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60795926"
---
# <a name="clustering-point-data"></a>叢集點資料

視覺化地圖上的許多資料點，當點彼此重疊對應看起來很雜亂，但是很難看到並使用。 叢集的點資料可用來改善這種使用者經驗。 叢集的資料點是結合彼此相近的點資料，並為單一叢集的資料點在地圖上代表它們的程序。 因為使用者拉近到對應中，叢集分解成其個別的資料點。

## <a name="enabling-clustering-on-a-data-source"></a>啟用叢集上的資料來源

叢集可以輕鬆上啟用`DataSource`類別，藉由設定`cluster`選項設為 true。 此外，結合到叢集中選取點附近的像素半徑可以使用來設定`clusterRadius`並縮放層級可指定要停用叢集的邏輯使用`clusterMaxZoom`選項。 以下是如何啟用叢集的資料來源中的範例。

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximium zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15 
});
```

> [!TIP]
> 如果兩個資料點彼此鄰近到地上，就可以叢集將永遠不會分解，無論在接近使用者縮放。 若要解決此問題，您可以設定`clusterMaxZoom`的縮放層級，若要停用叢集的邏輯，並且只顯示所有項目會指定資料來源的選項。

`DataSource`類別也有與叢集相關的下列方法：

| 方法 | 傳回類型 | 描述 |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | 承諾&lt;功能&lt;幾何，任何&gt;\|圖形&gt; | 擷取指定的叢集上的下一步 的縮放層級的子系。 這些子系可能是圖形和 subclusters 的組合。 Subclusters 會屬性符合 ClusteredProperties 的功能。 |
| getClusterExpansionZoom(clusterId: number) | Promise&lt;number&gt; | 計算縮放層級的叢集將會開始展開或分解。 |
| getClusterLeaves(clusterId: number, limit: number, offset: number) | 承諾&lt;功能&lt;幾何，任何&gt;\|圖形&gt; | 擷取在叢集中的所有點。 設定`limit`傳回的點，子集，並使用`offset`逐頁查看點。 |

## <a name="display-clusters-using-a-bubble-layer"></a>使用泡泡圖層顯示叢集

泡泡圖層是適合用來呈現叢集的點，您可以輕鬆地調整半徑，並變更它們依據的叢集中的點數使用運算式的色彩。 顯示時使用的泡泡圖層的叢集，您也應該使用個別的圖層來呈現非叢集的資料點。 它通常是不錯也能夠顯示泡泡上叢集的大小。 無圖示文字與符號層可用來達成此行為。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="叢集的基本泡泡圖層" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱畫筆<a href='https://codepen.io/azuremaps/pen/qvzRZY/'>基本泡泡圖層群集</a>由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>顯示使用符號層的叢集

當使用符號圖層中，依預設它會自動隱藏的點資料視覺化符號的重疊彼此建立簡潔的體驗時，不過這可能不是所需的體驗如果您想要查看資料的密度點在地圖上。 設定`allowOverlap`符號圖層的選項`iconOptions`屬性設`true`停用這項體驗，但會導致顯示的所有符號。 使用叢集，可讓您在建立好用的全新使用者體驗時看到的所有資料的密度。 在此範例中，自訂的符號，將用來代表叢集和個別資料點中。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="叢集的符號層" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱畫筆<a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>叢集符號層</a>透過 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>叢集和熱度圖對應圖層

熱度是適合用來在地圖上顯示資料的密度。 此視覺效果可處理大量的資料點，但它可以處理更多的資料，如果資料點叢集和叢集大小做為熱量分佈圖的加權。 設定`weight`熱度圖地圖圖層，以選擇`['get', 'point_count']`來達到此目的。 如果叢集 radius 很小，熱量分佈圖看起來幾乎完全相同熱度圖使用的非叢集的資料點，但更佳。 不過，小叢集 radius、 更精確的熱度圖會但更少的效能獲益。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="叢集加權熱量分佈圖" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱畫筆<a href='https://codepen.io/azuremaps/pen/VRJrgO/'>叢集加權熱度</a>由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>在叢集的資料點上的滑鼠事件

滑鼠事件發生時，包含叢集的資料點圖層上，將事件傳回叢集的資料點，以 GeoJSON 點功能物件。 此點功能將會有下列屬性：

| 屬性名稱 | 類型 | 描述 |
|---------------|------|-------------|
| 叢集 | boolean | 指出是否功能代表叢集。 |
| cluster_id | string | 可以搭配資料來源叢集的唯一識別碼`getClusterExpansionZoom`， `getClusterChildren`，和`getClusterLeaves`方法。 |
| point_count | number | 叢集中包含的點數目。 |
| point_count_abbreviated | string | 縮寫 point_count 值，如果很長的字串。 （例如 4000 變成 4k） |

這個範例會取得泡泡圖層可呈現叢集點，並新增 click 事件，當觸發時，計算中, 與縮放地圖來叢集就會中斷分開使用的下一步] 縮放層級`getClusterExpansionZoom`方法`DataSource`類別，並`cluster_id`屬性的已按下 [叢集資料點。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="叢集 getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱畫筆<a href='https://codepen.io/azuremaps/pen/moZWeV/'>叢集 getClusterExpansionZoom</a>透過 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="display-cluster-area"></a>顯示叢集的區域 

群集代表點資料分散到區域。 在此範例中的滑鼠停留的叢集中，個別的資料點，它包含 （分葉） 會用來計算的凸殼和地圖以顯示區域上顯示時。 可以從使用您建立資料來源擷取包含在叢集中的所有點`getClusterLeaves`方法。 凸殼就包裝一組點的多邊形像彈性頻外，而且可以使用計算`atlas.math.getConvexHull`方法。

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="叢集區域凸殼" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱畫筆<a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>叢集區域凸殼</a>透過 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [資料來源類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions 物件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.math namespace](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

請參閱程式碼範例，將功能新增至您的應用程式：

> [!div class="nextstepaction"]
> [新增泡泡圖層](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [新增符號圖層](map-add-pin.md)

> [!div class="nextstepaction"]
> [加入熱度圖地圖圖層](map-add-heat-map-layer.md)
