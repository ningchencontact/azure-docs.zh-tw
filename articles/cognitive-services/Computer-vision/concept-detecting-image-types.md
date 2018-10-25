---
title: 偵測影像類型：電腦視覺
titleSuffix: Azure Cognitive Services
description: 使用電腦視覺 API 偵測影像類型的相關概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: ecdbdd60af41ee14070f2f45dfe50875e38ac3b3
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341992"
---
# <a name="detecting-image-types"></a>偵測影像類型

電腦視覺可以指出影像是否為美工圖案、依等級評估可能性，或是否為線條繪畫等，來分析影像的內容類型。

## <a name="detecting-clip-art"></a>偵測美工圖案

電腦視覺分析影像，並依照 0 到 3 的等級評估該影像是美工圖案的可能性，如下表所述。

| 值 | 意義 |
|-------|---------|
| 0 | 非美工圖案 |
| 1 | 不明確 |
| 2 | 一般美工圖案 |
| 3 | 良好美工圖案 |

### <a name="clip-art-detection-examples"></a>美工圖案偵測範例

下列 JSON 回應說明了當評估該範例影像是否為美工圖案的可能性時，電腦視覺傳回的內容。

![視覺分析起司美工圖案](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![視覺分析住家庭院](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>偵測線條繪畫

電腦視覺分析影像並傳回布林值，指出影像是否為線條繪畫。

### <a name="line-drawing-detection-examples"></a>線條繪畫偵測範例

下列 JSON 回應說明了在指出該範例影像是否為線條繪圖時，電腦視覺傳回的內容。

![視覺分析獅子繪圖](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![視覺分析花朵](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>後續步驟

了解[標記影像](concept-tagging-images.md)和[分類影像](concept-categorizing-images.md)的相關概念。