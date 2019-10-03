---
title: 序數預先建立的實體-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding (LUIS) 中預先建置的 ordinal 實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: d1d69b256c4fc7e7b9d1c84b7c409d01a9f8ce52
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677549"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的序數預先建置實體
序號是一組物件內的數值表示法：`first`、`second`、`third`。 因為此實體已經定型，所以您不需要將包含序數的範例語句加入應用程式意圖。 [多種文化特色](luis-reference-prebuilt-entities.md)都支援 ordinal 實體。 

## <a name="types-of-ordinal"></a>序數類型
序數可從 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) GitHub 存放庫管理

## <a name="resolution-for-prebuilt-ordinal-entity"></a>解析預先建置的 ordinal 實體

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 預測端點回應](#tab/V2)

下列範例顯示解析的 **builtin.ordinal** 實體。

```json
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

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 預測端點回應](#tab/V3)

下列 JSON 是將 `verbose` 參數設定為 `false`：

```json
{
    "query": "Order the second option",
    "prediction": {
        "normalizedQuery": "order the second option",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "ordinal": [
                {
                    "offset": 2,
                    "relativeTo": "start"
                }
            ]
        }
    }
}
```

下列 JSON 是將 `verbose` 參數設定為 `true`：

```json
{
    "query": "Order the second option",
    "prediction": {
        "normalizedQuery": "order the second option",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "ordinal": [
                {
                    "offset": 2,
                    "relativeTo": "start"
                }
            ],
            "$instance": {
                "ordinal": [
                  {
                    "type": "builtin.ordinal",
                    "text": "second",
                    "startIndex": 10,
                    "length": 6,
                    "modelTypeId": 2,
                    "modelType": "Prebuilt Entity Extractor",
                    "recognitionSources": [
                        "model"
                    ]
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

瞭解[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)、[電話號碼](luis-reference-prebuilt-phonenumber.md)和[溫度](luis-reference-prebuilt-temperature.md)實體。 
