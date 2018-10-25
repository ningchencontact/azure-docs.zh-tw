---
title: 描述影像：電腦視覺
titleSuffix: Azure Cognitive Services
description: 使用電腦視覺 API 描述影像的相關概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 423d1be57bc800108a08a81b72587ca2711bbc3d
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342410"
---
# <a name="describing-images"></a>描述映像

電腦視覺的演算法會分析影像中的內容。 此分析會形成「描述」的基礎，讓此描述顯示為完整句子中可供閱讀的語言。 描述會概略說明影像中的項目。 電腦視覺的演算法會根據在影像中識別出來的視覺功能產生各種描述。 每個描述都會受到評估，並產生信賴分數。 接著會傳回一份清單，並依照信賴分數由高至低排序。

## <a name="image-description-example"></a>影像描述範例

下列 JSON 回應說明了在根據視覺功能描述範例影像時，電腦視覺傳回的內容。

![B&W 建築](./Images/bw_buildings.png)

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