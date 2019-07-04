---
title: 偵測臉部 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 了解電腦視覺 API 的臉部偵測功能的相關概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 699192aba87bb009d7dbddddcc9579883bb71db9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60368102"
---
# <a name="face-detection-with-computer-vision"></a>使用電腦視覺的物件偵測

電腦視覺會偵測影像中的人臉，並針對偵測到的臉部產生年齡、性別和矩形。 

> [!NOTE]
> Azure [臉部](/azure/cognitive-services/face/)服務也提供此功能。 如需更詳細的臉部分析，請參閱此替代方案，包括臉部辨識和姿勢偵測。 

## <a name="face-detection-examples"></a>臉部偵測範例

下列範例使用包含單一人臉的影像，示範電腦視覺傳回的 JSON 回應。

![視覺分析屋頂女人的臉部](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

接下來的範例使用包含多張人臉的影像來示範傳回的 JSON 回應。

![視覺分析家人合照的臉部](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="next-steps"></a>後續步驟

如需使用臉部偵測功能之方式的詳細資訊，請參閱[分析影像](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)參考文件。
