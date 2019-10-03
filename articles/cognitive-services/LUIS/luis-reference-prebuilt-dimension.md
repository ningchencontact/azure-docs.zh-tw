---
title: 維度預先建立的實體-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding (LUIS) 中預先建置的 dimension 實體資訊。
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 15ba8ad4d3bcf00024a0c7b14b004de08d37621a
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677654"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的維度預先建置實體
預先建置的 dimension 實體可偵測各種維度類型，無論 LUIS 應用程式文化特色為何。 因為此實體已經定型，所以您不需要將包含維度的範例語句加入應用程式意圖。 [多種文化特色](luis-reference-prebuilt-entities.md)都支援 dimension 實體。 

## <a name="types-of-dimension"></a>維度類型

維度可從 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) GitHub 存放庫管理


## <a name="resolution-for-dimension-entity"></a>解析 dimension 實體

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 預測端點回應](#tab/V2)

下列範例顯示解析的 **builtin.dimension** 實體。

```json
{
  "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.762141049
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.762141049
    }
  ],
  "entities": [
    {
      "entity": "10 1/2 miles",
      "type": "builtin.dimension",
      "startIndex": 19,
      "endIndex": 30,
      "resolution": {
        "unit": "Mile",
        "value": "10.5"
      }
    }
  ]
}
```


#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 預測端點回應](#tab/V3)

下列 JSON 是將 `verbose` 參數設定為 `false`：

```json
{
    "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
    "prediction": {
        "normalizedQuery": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.400049
            }
        },
        "entities": {
            "dimension": [
                {
                    "number": 10.5,
                    "unit": "Mile"
                }
            ]
        }
    }
}
```

下列 JSON 是將 `verbose` 參數設定為 `true`：

```json
{
    "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
    "prediction": {
        "normalizedQuery": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.400049
            }
        },
        "entities": {
            "dimension": [
                {
                    "number": 10.5,
                    "unit": "Mile"
                }
            ],
            "$instance": {
                "dimension": [
                    {
                        "type": "builtin.dimension",
                        "text": "10 1/2 miles",
                        "startIndex": 19,
                        "length": 12,
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

了解 [email](luis-reference-prebuilt-email.md)、[number](luis-reference-prebuilt-number.md) 及 [ordinal](luis-reference-prebuilt-ordinal.md) 實體相關資訊。 
