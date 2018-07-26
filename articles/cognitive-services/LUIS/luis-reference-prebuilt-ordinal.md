---
title: LUIS 預先建置的 ordinal 實體參考 - Azure| Microsoft Docs
titleSuffix: Azure
description: 本文包含 Language Understanding (LUIS) 中預先建置的 ordinal 實體資訊。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 216114ec521e2065cb13cd39b4086f50ec81ba56
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39236749"
---
# <a name="ordinal-entity"></a>Ordinal 實體
序號是一組物件內的數值表示法：`first`、`second`、`third`。 因為此實體已經定型，所以您不需要將包含序數的範例語句加入應用程式意圖。 [多種文化特色](luis-reference-prebuilt-entities.md)都支援 ordinal 實體。 

## <a name="types-of-ordinal"></a>序數類型
序數可從 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) GitHub 儲存機制管理

## <a name="resolution-for-prebuilt-ordinal-entity"></a>解析預先建置的 ordinal 實體
下列範例顯示解析的 **builtin.ordinal** 實體。

```JSON
{
  "query": "Order the second option",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.9993253
  },
  "intents": [
    {
      "intent": "OrderFood",
      "score": 0.9993253
    },
    {
      "intent": "None",
      "score": 0.05046708
    }
  ],
  "entities": [
    {
      "entity": "second",
      "type": "builtin.ordinal",
      "startIndex": 10,
      "endIndex": 15,
      "resolution": {
        "value": "2"
      }
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

了解 [percentage](luis-reference-prebuilt-percentage.md)、[phonenumber](luis-reference-prebuilt-phonenumber.md) 及 [temperature](luis-reference-prebuilt-temperature.md) 實體相關資訊。 