---
title: 影像分類：電腦視覺
titleSuffix: Azure Cognitive Services
description: 使用電腦視覺 API 將影像分類的相關概念。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 602ea8028cf89b23df692d5c2fb9b781f64bcad4
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341679"
---
# <a name="categorizing-images"></a>將影像分類

除了標記和描述以外，電腦視覺也會傳回舊版本中定義的分類型類別。 這些類別會以父/子承襲階層組織為分類。 所有類別皆採用英文。 類別可單獨使用，或與我們新的標記模型搭配使用。

## <a name="the-86-category-concept"></a>86 類別概念

根據下圖中列出的 86 種概念，將影像進行明確或特定範圍的分類。 如需文字格式的完整分類，請參閱[類別分類](category-taxonomy.md)。

![分析類別](./Images/analyze_categories.png)

## <a name="image-categorization-examples"></a>影像分類範例

下列 JSON 回應說明根據視覺功能進行範例影像分類時，電腦視覺傳回的內容。

![屋頂上的女人](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

下表所列的是一般的影像集和電腦視覺針對每個影像所傳回的類別。

| 映像 | 類別 |
|-------|----------|
| ![家人照片](./Images/family_photo.png) | people_group |
| ![可愛的小狗](./Images/cute_dog.png) | animal_dog |
| ![戶外山景](./Images/mountain_vista.png) | outdoor_mountain |
| ![視覺分析食物 (麵包)](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>後續步驟

了解[標記影像](concept-tagging-images.md)和[描述影像](concept-describing-images.md)的相關概念。