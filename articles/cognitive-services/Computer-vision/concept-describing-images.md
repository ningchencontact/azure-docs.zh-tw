---
title: 描述影像：電腦視覺
titleSuffix: Azure Cognitive Services
description: 電腦視覺 API 的影像描述功能相關概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 933c4e4a6731a492a5aa461de7c782af422fb6fb
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55162696"
---
# <a name="describe-images-with-human-readable-language"></a>描述映包含人類可讀語言的影像

電腦視覺的演算法會分析影像中的內容。 此分析會形成「描述」的基礎，讓此描述顯示為完整句子中可供閱讀的語言。 描述會概略說明影像中的項目。 電腦視覺的演算法會根據在影像中識別出來的視覺功能產生各種描述。 每個描述都會受到評估，並產生信賴分數。 接著會傳回一份清單，並依照信賴分數由高至低排序。

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
