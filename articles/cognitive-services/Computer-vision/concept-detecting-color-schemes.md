---
title: 偵測色彩配置：電腦視覺
titleSuffix: Azure Cognitive Services
description: 使用電腦視覺 API 偵測影像中色彩配置的相關概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d882ad89e68936d07ae4d76218c6e3ac450185a8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55151323"
---
# <a name="detect-color-schemes-in-images"></a>偵測影像中的色彩配置

電腦視覺會從影像中擷取色彩。 色彩會依三種不同內容加以分析：主要前景色彩、主要背景色彩、整體影像的主要色彩。 色彩會分為 12 種主要輔色。 這些輔色是黑色、藍色、棕色、灰色、綠色、橙色、粉紅色、紫色、紅色、藍綠色、白色和黃色。 電腦視覺會分析擷取自影像的色彩，透過主要色彩和飽和度的組合，傳回對觀看者而言會呈現出影像最鮮明色彩的輔色。 根據影像中的色彩，簡單的黑白或輔色可用十六進位色彩代碼傳回。 電腦視覺也會傳回布林值，指出影像是否為黑白。

## <a name="color-scheme-detection-examples"></a>色彩配置偵測範例

下列範例說明偵測範例影像的色彩配置時，電腦視覺所傳回的 JSON 回應。 在此情況下，本範例影像不是黑白影像，但主要前景和背景色彩為黑色，而且整體影像的主要色彩為黑白。

![戶外山景](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>主要色彩範例

下表說明電腦視覺傳回的每個範例影像主要前景色彩、主要背景色彩以及影像色彩。

| 映像 | 主要色彩 |
|-------|-----------------|
|![具有綠色背景的白色花卉](./Images/flower.png)| 前景：黑色<br/>後景：白色<br/>色彩：黑色、白色、綠色|
![通過車站的火車](./Images/train_station.png) | 前景：黑色<br/>後景：黑色<br/>色彩：黑色 |

### <a name="accent-color-examples"></a>輔色範例

 下表說明電腦視覺傳回的每個範例影像輔色 (做為十六進位的 HTML 色彩值)。

| 映像 | 輔色 |
|-------|--------------|
|![日落時站在山岩上的人](./Images/mountain_vista.png) | #BB6D10 |
|![具有綠色背景的白色花卉](./Images/flower.png) | #C6A205 |
|![通過車站的火車](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>黑白偵測範例

下表指出電腦視覺傳回的每個範例影像是否為黑白。

| 映像 | 黑白？ |
|-------|----------------|
|![曼哈頓建築物的黑白照片](./Images/bw_buildings.png) | true |
|![藍色房屋和前院](./Images/house_yard.png) | false |

## <a name="next-steps"></a>後續步驟

了解[偵測影像類型](concept-detecting-image-types.md)的相關概念。
