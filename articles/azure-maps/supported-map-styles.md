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
ms.openlocfilehash: 1aad2284c0f64c92efaefe3f9145d95c4aabec67
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839442"
---
# <a name="azure-maps-supported-map-styles"></a>Azure 地圖服務支援的地圖樣式
「Azure 地圖服務」支援數個不同的內建地圖樣式，如下所述。

## <a name="road"></a>路段
**路段**圖是標準的地圖，其中顯示道路、自然和人工景觀，以及這些景觀的標籤。

![路段](./media/supported-map-styles/road.png)

**適用 API：**
* [地圖影像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [地圖底圖](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK 地圖控制項
* Android 地圖控制項

## <a name="blank-and-blank_accessible"></a>空白和 blank_accessible

[**空白**] 和 [ **blank_accessible** ] 地圖樣式提供要在其上視覺化資料的空白畫布。 **Blank_accessible**樣式將繼續提供螢幕閱讀程式更新, 其中包含地圖所在位置的位置詳細資料, 即使沒有顯示基底地圖也一樣。

> [!Note]
> 在 web SDK 中, 您可以藉由設定地圖 DIV 元素的 CSS `background-color`樣式來變更地圖的背景色彩。

**適用 API：**
* Web SDK 地圖控制項

## <a name="satellite"></a>衛星 
**衛星**樣式是衛星和空拍影像的結合。

![衛星](./media/supported-map-styles/satellite.png)

**適用 API：**
* [衛星底圖](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK 地圖控制項
* Android 地圖控制項

## <a name="satellite_road_labels"></a>satellite_road_labels
此地圖樣式包含道路及覆蓋在衛星和空拍影像上的標籤。

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**適用 API：**
* Web SDK 地圖控制項
* Android 地圖控制項

## <a name="grayscale_dark"></a>grayscale_dark
**深灰階**是路段圖樣式的深色版本。

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**適用 API：**
* Web SDK 地圖控制項 
* Android 地圖控制項


## <a name="grayscale_light"></a>grayscale_light
「**灰階光線**」是道路地圖樣式的輕量版本。

![灰階 light](./media/supported-map-styles/grayscale_light.png)

**適用 API：**
* Web SDK 地圖控制項
* Android 地圖控制項


## <a name="night"></a>夜間
**夜間**是路段圖樣式的深色版本，具有彩色的道路和符號。

![夜間](./media/supported-map-styles/night.png)

**適用 API：**
* Web SDK 地圖控制項
* Android 地圖控制項

## <a name="road_shaded_relief"></a>road_shaded_relief
**路段陰影起伏圖**是以地球等高線完成的「Azure 地圖服務」的主要樣式。

![陰影起伏圖](./media/supported-map-styles/shaded-relief.png)

**適用 API：**
* [地圖底圖](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK 地圖控制項
* Android 地圖控制項
