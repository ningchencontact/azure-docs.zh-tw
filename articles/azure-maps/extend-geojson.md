---
title: 擴充的 GeoJSON 幾何 |Microsoft Azure 對應
description: 在本文中，您將瞭解 Microsoft Azure 對應如何擴充 GeoJSON 規格，以代表特定的幾何。
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 75ad83411edfdfe7545e8f80df17fea56e317ee0
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911643"
---
# <a name="extended-geojson-geometries"></a>擴充的 GeoJSON 幾何

Azure 提供許多強大的 API，能夠在地理特徵之內/依循地理特徵進行搜尋。
這些 Api 會針對代表地理功能的[GeoJSON 規格][1]進行標準化（例如：州界限、路線）。  

[GeoJSON 規格][1]僅支援下列幾何：

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

某些 Azure 地圖服務 Api （例如：[在幾何內搜尋](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)）接受類似「圓形」的幾何，這不是[GeoJSON 規格][1]的一部分。

本文提供 Azure 地圖服務如何擴充[GeoJSON 規格][1]以代表特定幾何的詳細說明。

## <a name="circle"></a>Circle

[GeoJSON 規格][1]不支援 `Circle` 的幾何。我們使用 `GeoJSON Point Feature` 物件來代表一個圓形。

使用 `GeoJSON Feature` 物件代表的 `Circle` 幾何__必須__包含下列項目：

- 中心

    圓形的中心是使用 `GeoJSON Point` 物件來表示。

- 半徑

    圓形的 `radius` 是以 `GeoJSON Feature` 的屬性表示。 半徑值的單位是_公尺_，而且必須屬於型別 `double`。

- 子類型

    圓形幾何也必須包含 `subType` 屬性。 這個屬性必須是 `GeoJSON Feature`屬性的一部分，且其值應該是_Circle_

#### <a name="example"></a>範例

以下是中心在 (緯度：47.639754，經度：-122.126986) 且半徑等於 100 公尺的圓形，使用 `GeoJSON Feature` 物件：

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

## <a name="rectangle"></a>矩形

[GeoJSON 規格][1]不支援 `Rectangle` 的幾何。我們使用 `GeoJSON Polygon Feature` 物件來代表一個矩形。 矩形擴充功能主要是由 Web SDK 的「繪圖工具」模組所使用。

使用 `GeoJSON Polygon Feature` 物件代表的 `Rectangle` 幾何__必須__包含下列項目：

- 轉角

    矩形的角落會使用 `GeoJSON Polygon` 物件的座標來表示。 應該有五個座標，一個用於每個角落，第五個座標與第1個相同，以關閉多邊形環形。 這些座標會假設為靠開發人員所需對齊和旋轉。

- 子類型

    矩形幾何也必須包含 `subType` 屬性。 這個屬性必須是 `GeoJSON Feature`屬性的一部分，而且其值應該是_矩形_

### <a name="example"></a>範例

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>後續步驟

深入瞭解如何在 Azure 地圖服務中 GeoJSON 資料：

> [!div class="nextstepaction"]
> [地理柵欄 GeoJSON 格式](geofence-geojson.md)

請參閱與 Azure 地圖服務和位置智慧應用程式相關聯的常見技術詞彙詞彙：

> [!div class="nextstepaction"]
> [Azure 地圖服務詞彙](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
