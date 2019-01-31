---
title: 溫度預先建置的實體
titleSuffix: Azure
description: 本文包含 Language Understanding (LUIS) 中預先建置的 temperature 實體資訊。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 921f2bb2c03a24d5ed269452cdc7f14e7a40c73b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207984"
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
