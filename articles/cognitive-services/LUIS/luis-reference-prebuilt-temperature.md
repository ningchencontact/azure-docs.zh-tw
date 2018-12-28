---
title: 溫度預先建置的實體
titleSuffix: Azure
description: 本文包含 Language Understanding (LUIS) 中預先建置的 temperature 實體資訊。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 348030d888383c497d80259b279056d8ff892bfe
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2018
ms.locfileid: "53165512"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的溫度預先建置實體
Temperature 實體會擷取各種溫度類型。 因為此實體已經定型，所以您不需要將包含溫度的範例語句加入應用程式。 [多種文化特色](luis-reference-prebuilt-entities.md)都支援 temperature 實體。 

## <a name="types-of-temperature"></a>溫度類型
溫度可從 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819) GitHub 存放庫管理

## <a name="resolution-for-prebuilt-temperature-entity"></a>解析預先建置的 temperature 實體
下列範例顯示解析的 **builtin.temperature** 實體。

```json
{
  "query": "set the temperature to 30 degrees",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.85310787
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.85310787
    }
  ],
  "entities": [
    {
      "entity": "30 degrees",
      "type": "builtin.temperature",
      "startIndex": 23,
      "endIndex": 32,
      "resolution": {
        "unit": "Degree",
        "value": "30"
      }
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

了解 [percentage](luis-reference-prebuilt-percentage.md)、[number](luis-reference-prebuilt-number.md) 及 [age](luis-reference-prebuilt-age.md) 實體相關資訊。 