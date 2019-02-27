---
title: 描述影像：電腦視覺
titleSuffix: Azure Cognitive Services
description: 電腦視覺 API 的影像描述功能相關概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 91618b211fdd869daf74491b175d6359ffa3f30c
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312186"
---
# <a name="describe-images-with-human-readable-language"></a>描述映包含人類可讀語言的影像

電腦視覺可以分析影像，並產生人類看得懂的句子來描述其內容。 該演算法實際上根據不同的視覺功能傳回多個描述，且每個描述都給予信賴分數。 最後的輸出是從最高到最低信賴分數排序的描述清單。

## <a name="image-description-example"></a>影像描述範例

下列 JSON 回應說明了在根據視覺功能描述範例影像時，電腦視覺傳回的內容。

![曼哈頓建築物的黑白照片](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>後續步驟

了解[標記影像](concept-tagging-images.md)和[分類影像](concept-categorizing-images.md)的相關概念。
