---
title: 在 Azure 地圖服務中建立資料來源 |Microsoft Docs
description: 如何建立資料來源, 並將它與 Azure 地圖服務 Web SDK 搭配使用。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c225aa5f821925247d27890e9ee75f3eac4d9eb9
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977348"
---
# <a name="create-a-data-source"></a>建立資料來源

Azure 地圖服務 Web SDK 會將資料儲存在資料來源中, 以將資料優化以進行查詢和呈現。 目前有兩種類型的資料來源:

**GeoJSON 資料來源**

以 GeoJSON 為基礎的資料來源可以使用類別在`DataSource`本機載入和儲存資料。 您可以使用 GeoJSON 資料, 以[資料](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data)命名空間中的 helper 類別手動建立或建立。 `DataSource`類別提供匯入本機或遠端 GeoJSON 檔案的功能。 遠端 GeoJSON 檔必須裝載在啟用 CORs 的端點上。 `DataSource`類別會提供群集點資料的功能。 

**向量圖格來源**

向量圖格來源說明如何存取向量圖格圖層, 並可使用[VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource)類別來建立。 Azure 地圖服務與[Mapbox 向量磚規格](https://github.com/mapbox/vector-tile-spec)一致, 這是一種開放標準。 向量圖格圖層與磚圖層相似, 而不是每個磚都是點陣影像, 而是包含向量地圖資料的壓縮檔案 (PBF 格式), 以及一或多個圖層, 可以根據每個圖層的樣式在用戶端上呈現和設定樣式。 [向量] 磚中的資料會以點、線條和多邊形的形式包含地理特徵。 向量圖格圖層的幾個優點是透過點陣磚圖層;

 - [向量] 磚的檔案大小通常會比對等的 [點陣] 磚小很多。 因此, 會使用較少的頻寬, 這表示延遲較低, 而且對應速度更快。 這會產生更佳的使用者體驗。
 - 由於向量圖格會在用戶端上轉譯, 因此可以適應其顯示所在裝置的解析度。 這可讓呈現的對應更妥善地定義, 並具有 crystal clear 標籤。 
 - 變更向量對應中的資料樣式並不需要再次下載資料, 因為新的樣式可以套用至用戶端。 相反地, 變更點陣磚圖層的樣式通常需要從已套用新樣式的伺服器載入磚。
 - 由於資料是以向量形式傳遞, 因此需要較少的伺服器端處理來準備資料, 這表示較新的資料可以更快速地提供。

所有使用向量來源的圖層都必須指定`sourceLayer`一個值。 

建立之後, 您可以透過`map.sources`屬性 ( [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager)) 將資料來源加入至對應。 下列程式碼示範如何建立`DataSource` , 並將它加入至對應。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

## <a name="connecting-a-data-source-to-a-layer"></a>將資料來源連接至圖層

資料會使用呈現層在地圖上轉譯。 單一資料來源可由一或多個呈現層參考。 下列轉譯圖層要求資料來源必須具備強大的電源:

- 反升[層](map-add-bubble-layer.md)-將點資料轉譯為地圖上的縮放圓形。
- [符號圖層](map-add-pin.md)-以圖示和 (或) 文字呈現點資料。
- [熱度圖圖層](map-add-heat-map-layer.md)-以密度熱度圖呈現點資料。
- [線條圖層](map-add-shape.md)-可以用來呈現線條和多邊形的外框。 
- [多邊形圖層](map-add-shape.md)-以純色或影像模式填滿多邊形的區域。

下列程式碼示範如何建立資料來源、將它加入至地圖, 並將它連接到反升圖層, 然後將 GeoJSON 點資料從遠端位置匯入其中。 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

還有其他轉譯層不會連接到這些資料來源, 而是會載入它們直接呈現的資料。 

- [影像圖層](map-add-image-layer.md)-在地圖上將單一影像重迭, 並將其角落系結至一組指定的座標。
- 圖格[圖層](map-add-tile-layer.md)-在地圖上方 superimposes 點陣磚圖層。

## <a name="one-data-source-with-multiple-layers"></a>一個具有多個層級的資料來源

多個層級可以連接到單一資料來源。 這聽起來可能會很奇怪, 但有許多不同的案例會變得非常有用。 例如, 建立多邊形繪圖體驗的案例。 當使用者繪製多邊形時, 我們應該在使用者將點加入地圖時, 轉譯填滿多邊形區域。 加入概述多邊形的樣式線, 可讓您更輕鬆地查看繪製多邊形的邊緣。 最後, 在多邊形中的每個位置上方加入某種控制碼, 例如釘選或標記, 可讓您更輕鬆地編輯每個個別位置。 以下是示範此案例的影像。

![顯示從單一資料來源轉譯資料之多個圖層的地圖](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

若要在大部分的對應平臺中完成這個案例, 您必須為多邊形中的每個位置建立多邊形物件、線條物件和釘選。 當多邊形已修改時, 您必須手動更新線條和 pin, 這可能很快就會變得複雜。

有了 Azure 地圖服務, 您只需要資料來源中的單一多邊形, 如下列程式碼所示。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [新增快顯](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [新增符號圖層](map-add-pin.md)

> [!div class="nextstepaction"]
> [新增泡泡圖層](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [新增線條圖層](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](map-add-shape.md)

> [!div class="nextstepaction"]
> [新增熱度圖](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)