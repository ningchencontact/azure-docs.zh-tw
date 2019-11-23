---
title: Azure 地圖服務中的支援地圖樣式 | Microsoft Docs
description: Azure 地圖服務支援 的地圖樣式
author: walsehgal
ms.author: v-musehg
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 5368aec04eb0a57654adf5b0d5914282a979dcfd
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325404"
---
# <a name="azure-maps-supported-map-styles"></a>Azure 地圖服務支援的地圖樣式
「Azure 地圖服務」支援數個不同的內建地圖樣式，如下所述。

## <a name="road"></a>路段
**路段**圖是標準的地圖，其中顯示道路、自然和人工景觀，以及這些景觀的標籤。

![路段](./media/supported-map-styles/road.png)

**適用 API：**
* [地圖影像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [地圖底圖](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK map control
* Android map control

## <a name="blank-and-blank_accessible"></a>blank and blank_accessible

The **blank** and **blank_accessible** map styles provide a blank canvas on which to visualize data on. The **blank_accessible** style will continue to provide screen reader updates with location details of where the map is located, even though the base map is not displayed.

> [!Note]
> In the web SDK you can change the background color of the map by setting the CSS `background-color` style of map DIV element.

**適用 API：**
* Web SDK map control

## <a name="satellite"></a>衛星 
**衛星**樣式是衛星和空拍影像的結合。

![衛星](./media/supported-map-styles/satellite.png)

**適用 API：**
* [衛星底圖](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK map control
* Android map control

## <a name="satellite_road_labels"></a>satellite_road_labels
此地圖樣式包含道路及覆蓋在衛星和空拍影像上的標籤。

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**適用 API：**
* Web SDK map control
* Android map control

## <a name="grayscale_dark"></a>grayscale_dark
**深灰階**是路段圖樣式的深色版本。

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**適用 API：**
* [地圖影像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [地圖底圖](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK map control 
* Android map control


## <a name="grayscale_light"></a>grayscale_light
**grayscale light** is a light version of the road map style.

![grayscale light](./media/supported-map-styles/grayscale_light.png)

**適用 API：**
* Web SDK map control
* Android map control


## <a name="night"></a>夜間
**夜間**是路段圖樣式的深色版本，具有彩色的道路和符號。

![夜間](./media/supported-map-styles/night.png)

**適用 API：**
* Web SDK map control
* Android map control

## <a name="road_shaded_relief"></a>road_shaded_relief
**路段陰影起伏圖**是以地球等高線完成的「Azure 地圖服務」的主要樣式。

![陰影起伏圖](./media/supported-map-styles/shaded-relief.png)

**適用 API：**
* [地圖底圖](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK map control
* Android map control


## <a name="next-steps"></a>後續步驟

Learn about how to set a map style in Azure Maps:

> [!div class="nextstepaction"]
> [選擇地圖樣式](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
