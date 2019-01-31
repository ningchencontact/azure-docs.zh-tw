---
title: 年齡預先建置的實體
titleSuffix: Azure
description: 本文包含 Language Understanding (LUIS) 中預先建置的 age 實體資訊。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 7644f2c4f4479ae234dc2d4b501be17455230bde
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217844"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的年齡預先建置實體
預先建置的 age 實體會以年數、月數、週數及天數的方式擷取年齡值。 因為此實體已經定型，所以您不需要將包含年齡的範例語句加入應用程式意圖。 [多種文化特色](luis-reference-prebuilt-entities.md)都支援 age 實體。 

## <a name="types-of-age"></a>年齡類型
年齡可從 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) GitHub 存放庫管理

## <a name="resolution-for-prebuilt-age-entity"></a>解析預先建置的 age 實體
下列範例顯示解析的 **builtin.age** 實體。

```json
{
  "query": "A 90 day old utilities bill is quite late.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8236133
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8236133
    }
  ],
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

了解 [currency](luis-reference-prebuilt-currency.md)、[datetimeV2](luis-reference-prebuilt-datetimev2.md) 及 [dimension](luis-reference-prebuilt-dimension.md) 實體相關資訊。 
