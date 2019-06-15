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
ms.openlocfilehash: db396ad06bf46cbbaf486696b68393a6a4214c2f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65230838"
---
# <a name="azure-maps-supported-map-styles"></a>Azure 地圖服務支援的地圖樣式
「Azure 地圖服務」支援數個不同的內建地圖樣式，如下所述。

## <a name="road"></a>路段
**路段**圖是標準的地圖，其中顯示道路、自然和人工景觀，以及這些景觀的標籤。

![路段](./media/supported-map-styles/road.png)

**適用 API：**
* [地圖影像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [地圖底圖](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* JS 地圖控制項
* Android 的地圖控制項

## <a name="satellite"></a>衛星 
**衛星**樣式是衛星和空拍影像的結合。

![衛星](./media/supported-map-styles/satellite.png)

**適用 API：**
* [衛星底圖](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* JS 地圖控制項
* Android 的地圖控制項

## <a name="satelliteroadlabels"></a>satellite_road_labels
此地圖樣式包含道路及覆蓋在衛星和空拍影像上的標籤。

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**適用 API：**
* JS 地圖控制項
* Android 的地圖控制項

## <a name="grayscaledark"></a>grayscale_dark
**深灰階**是路段圖樣式的深色版本。

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**適用 API：**
* JS 地圖控制項 
* Android 的地圖控制項


## <a name="grayscalelight"></a>grayscale_light
**灰階 light**是輕量版的道路地圖樣式。

![灰階光線](./media/supported-map-styles/grayscale_light.png)

**適用 API：**
* JS 地圖控制項
* Android 的地圖控制項


## <a name="night"></a>夜間
**夜間**是路段圖樣式的深色版本，具有彩色的道路和符號。

![夜間](./media/supported-map-styles/night.png)

**適用 API：**
* JS 地圖控制項
* Android 的地圖控制項

## <a name="roadshadedrelief"></a>road_shaded_relief
**路段陰影起伏圖**是以地球等高線完成的「Azure 地圖服務」的主要樣式。

![陰影起伏圖](./media/supported-map-styles/shaded-relief.png)

**適用 API：**
* [地圖底圖](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* JS 地圖控制項
* Android 的地圖控制項
