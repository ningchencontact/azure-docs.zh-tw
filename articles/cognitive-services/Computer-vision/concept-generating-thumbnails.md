---
title: 產生縮圖：電腦視覺
titleSuffix: Azure Cognitive Services
description: 使用電腦視覺 API 產生影像縮圖的相關概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8881a0c5e809dc75d1b9f4b6f6bd3af7142333cb
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884625"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>使用電腦視覺產生智慧裁剪的縮圖

縮圖是大小縮小的影像表示。 縮圖主要用來以經濟實惠且利於版面配置的方式來表示影像和其他資料。 電腦視覺 API 會使用智慧裁剪，以及搭配影像大小調整，來建立特定影像的直覺式縮圖。

電腦視覺縮圖產生演算法的運作方式如下所示：
1. 從影像中移除雜亂的元素，並找出「關注區域 (area of interest)」&mdash;影像中顯示主要物件的區域。
1. 根據所識別的「關注區域」來裁剪影像。
1. 變更外觀比例以符合目標縮圖尺寸。

## <a name="area-of-interest"></a>關注區域

當您上傳影像時，電腦視覺 API 會分析該影像，以判斷「關注區域」。 然後使用此區域來決定如何裁剪影像。 不過，若未指定的話，裁剪作業一律會符合所需的外觀比例。

您也可以藉由呼叫 **areaOfInterest** API，改為取得此相同「關注區域」的原始週框方塊座標。 接著，您可以使用此資訊來依照喜好修改原始影像。

## <a name="examples"></a>範例

產生的縮圖可能大不相同，取決於您指定的高度、寬度及智慧裁剪方式，如下列影像所示。

![縮圖](./Images/thumbnail-demo.png)

下表顯示的是電腦視覺針對範例影像所產生的典型縮圖。 這些是目標高度和寬度指定為 50 像素，並啟用智慧裁剪功能所產生的縮圖。

| 映像 | 縮圖 |
|-------|-----------|
|![日落時站在山岩上的人](./Images/mountain_vista.png) | ![戶外山景縮圖](./Images/mountain_vista_thumbnail.png) |
|![具有綠色背景的白色花卉](./Images/flower.png) | ![視覺分析花朵縮圖](./Images/flower_thumbnail.png) |
|![公寓大樓屋頂上的女人](./Images/woman_roof.png) | ![屋頂上的女人縮圖](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>後續步驟

深入了解[標記影像](concept-tagging-images.md)和[分類影像](concept-categorizing-images.md)。
