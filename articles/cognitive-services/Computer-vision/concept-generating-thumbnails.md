---
title: 產生縮圖：電腦視覺
titleSuffix: Azure Cognitive Services
description: 使用電腦視覺 API 產生影像縮圖的相關概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 9cb82b40d1fbec513b0219f26d1959fbd7f64570
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343957"
---
# <a name="generating-thumbnails"></a>產生縮圖

縮圖是完整大小影像的小型表示法。 手機、平板電腦和 PC 等不同裝置需要不同的使用者體驗 (UX) 配置和縮圖大小。 此電腦視覺功能使用智慧裁剪，因此有助於解決此問題。

上傳影像後，電腦視覺會產生高品質縮圖，然後分析該影像內的物件來識別相關區域 (ROI)， 而且可以選擇性裁剪影像，以符合 ROI 需求。 產生的縮圖可以使用與原始影像的外觀比例不同的外觀比例來呈現，以因應您的需求。

縮圖演算法的運作方式如下：

1. 從影像中移除雜亂的元素，並識別主要物件 (相關區域)。
2. 根據已識別的相關區域裁剪影像。
3. 變更外觀比例以符合目標縮圖尺寸。

產生的縮圖可能大不相同，取決於您指定的高度、寬度及智慧裁剪方式，如下列影像所示。

![縮圖](./Images/thumbnail-demo.png)

## <a name="thumbnail-generation-examples"></a>縮圖產生範例

下表顯示的是電腦視覺針對範例影像所產生的典型縮圖。 這些是目標高度和寬度指定為 50 像素，並啟用智慧裁剪功能所產生的縮圖。

| 映像 | 縮圖 |
|-------|-----------|
|![戶外山景](./Images/mountain_vista.png) | ![戶外山景縮圖](./Images/mountain_vista_thumbnail.png) |
|![視覺分析花朵](./Images/flower.png) | ![視覺分析花朵縮圖](./Images/flower_thumbnail.png) |
|![屋頂上的女人](./Images/woman_roof.png) | ![屋頂上的女人縮圖](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>後續步驟

了解[標記影像](concept-tagging-images.md)和[分類影像](concept-categorizing-images.md)的相關概念。