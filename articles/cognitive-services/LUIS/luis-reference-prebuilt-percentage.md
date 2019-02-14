---
title: 百分比預先建置的實體
titleSuffix: Azure
description: 本文包含 Language Understanding (LUIS) 中預先建置的百分比實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 0e06857fe45b81bcb6696c29b6a3e07a6587a95b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874663"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的百分比預先建置實體
百分比數字可顯示為分數 `3 1/2` 或百分比 `2%`。 由於此實體已經定型，因此您不需要將包含百分比的範例語句加入至應用程式意圖。 [多種文化特色](luis-reference-prebuilt-entities.md)都支援百分比實體。 

## <a name="types-of-percentage"></a>百分比類型
百分比可從 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) GitHub 存放庫來管理

## <a name="resolution-for-prebuilt-percentage-entity"></a>解析預先建置的百分比實體
下列範例說明 **builtin.percentage** 實體的解析。

```json
{
  "query": "set a trigger when my stock goes up 2%",
  "topScoringIntent": {
    "intent": "SetTrigger",
    "score": 0.971157849
  },
  "intents": [
    {
      "intent": "SetTrigger",
      "score": 0.971157849
    }
  ],
  "entities": [
    {
      "entity": "2%",
      "type": "builtin.percentage",
      "startIndex": 36,
      "endIndex": 37,
      "resolution": {
        "value": "2%"
      }
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

了解 [ordinal](luis-reference-prebuilt-ordinal.md)、[number](luis-reference-prebuilt-number.md) 和 [temperature](luis-reference-prebuilt-temperature.md) 實體相關資訊。 
