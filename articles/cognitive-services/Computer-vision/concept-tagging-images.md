---
title: 標記影像 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 使用電腦視覺 API 標記影像的相關概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 0025cdcfaa64a262a5ca54ab4db5a84f6a5768ba
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338908"
---
# <a name="tagging-images"></a>標記影像

電腦視覺會依據超過 2,000 個的可辨識物件、生物、景象及動作傳回標記。 若標記不明確或不屬於常識，API 回應會提供「提示」來釐清標記在已知設定內容中的意涵。 標籤不會組織成分類，且不會有繼承階層存在。 內容標記的集合會形成影像「描述」的基礎，讓此描述顯示為完整句子中已格式化而可供閱讀的語言。 請注意，英文是影像描述目前唯一支援的語言。

在上傳影像或指定影像 URL 之後，電腦視覺的演算法會根據在影像中識別出的物件、生物和動作來輸出標記。 標記並未限定於主體 (例如前景中的人物)，而是包含周遭環境 (室內或室外)、家具、工具、植物、動物、配件和小工具等。

## <a name="image-tagging-example"></a>影像標記範例

下列 JSON 回應說明了在範例影像中偵測到標記視覺功能時，電腦視覺傳回的內容。

![House_Yard](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>後續步驟

了解[分類影像](concept-categorizing-images.md)和[描述影像](concept-describing-images.md)的相關概念。