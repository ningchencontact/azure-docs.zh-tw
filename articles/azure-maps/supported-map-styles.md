---
title: Azure 地圖服務中的支援地圖樣式 | Microsoft Docs
description: Azure 地圖服務支援 的地圖樣式
author: walsehgal
ms.author: v-musehg
ms.date: 10/02/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c8edaba8de597e3e76e760e1f5109006338a663c
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238815"
---
# <a name="azure-maps-supported-map-styles"></a>Azure 地圖服務支援的地圖樣式
Azure 地圖服務支援四個不同的內建地圖樣式。 下方列出樣式及其描述。

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