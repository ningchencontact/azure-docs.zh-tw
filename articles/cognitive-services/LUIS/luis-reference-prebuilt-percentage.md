---
title: 百分比預先建立的實體-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding (LUIS) 中預先建置的百分比實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 0005d679819f7516e1ded6294030cd42813e085f
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677523"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的百分比預先建置實體
百分比數字可顯示為分數 `3 1/2` 或百分比 `2%`。 由於此實體已經定型，因此您不需要將包含百分比的範例語句加入至應用程式意圖。 [多種文化特色](luis-reference-prebuilt-entities.md)都支援百分比實體。 

## <a name="types-of-percentage"></a>百分比類型
百分比可從 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) GitHub 存放庫來管理

## <a name="resolution-for-prebuilt-percentage-entity"></a>解析預先建置的百分比實體

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 預測端點回應](#tab/V2)

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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 預測端點回應](#tab/V3)

下列 JSON 是將 `verbose` 參數設定為 `false`：

```json
{
    "query": "set a trigger when my stock goes up 2%",
    "prediction": {
        "normalizedQuery": "set a trigger when my stock goes up 2%",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.541765451
            }
        },
        "entities": {
            "percentage": [
                2
            ]
        }
    }
}
```

下列 JSON 是將 `verbose` 參數設定為 `true`：

```json
{
    "query": "set a trigger when my stock goes up 2%",
    "prediction": {
        "normalizedQuery": "set a trigger when my stock goes up 2%",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.541765451
            }
        },
        "entities": {
            "percentage": [
                2
            ],
            "$instance": {
                "percentage": [
                    {
                        "type": "builtin.percentage",
                        "text": "2%",
                        "startIndex": 36,
                        "length": 2,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

* * * 

## <a name="next-steps"></a>後續步驟

深入瞭解[V3 預測端點](luis-migration-api-v3.md)。

了解 [ordinal](luis-reference-prebuilt-ordinal.md)、[number](luis-reference-prebuilt-number.md) 和 [temperature](luis-reference-prebuilt-temperature.md) 實體相關資訊。 
