---
title: Azure 地圖服務中的支援地圖樣式 | Microsoft Docs
description: Azure 地圖服務支援 的地圖樣式
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 923b9fef75fa610ee817a86ce0b4d6b322153f01
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113426"
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

## <a name="satellite"></a>衛星 
**衛星**樣式是衛星和空拍影像的結合。

![衛星](./media/supported-map-styles/satellite.png)

**適用 API：**
* [衛星底圖](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* JS 地圖控制項

## <a name="satelliteroadlabels"></a>satellite_road_labels
此地圖樣式包含道路及覆蓋在衛星和空拍影像上的標籤。

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**適用 API：**
* JS 地圖控制項

## <a name="grayscaledark"></a>grayscale_dark
**深灰階**是路段圖樣式的深色版本。

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**適用 API：**
* JS 地圖控制項 

## <a name="night"></a>夜間
**夜間**是路段圖樣式的深色版本，具有彩色的道路和符號。

![夜間](./media/supported-map-styles/night.png)

**適用 API：**
* JS 地圖控制項

## <a name="roadshadedrelief"></a>road_shaded_relief
**路段陰影起伏圖**是以地球等高線完成的「Azure 地圖服務」的主要樣式。

![陰影起伏圖](./media/supported-map-styles/shaded-relief.png)

**適用 API：**
* JS 地圖控制項
* [地圖底圖](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
