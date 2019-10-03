---
title: 序數 V2 預建實體-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding （LUIS）中的序數 V2 預建實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 48dcbd51190e747859f0172473c94b0caa296071
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677558"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的序數 V2 預建實體
序數 V2 數位會展開[序數](luis-reference-prebuilt-ordinal.md)，以提供如 `next`，`last`，以及 `previous` 的相關參考。 這些不會使用序數預建實體來解壓縮。

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>預先建立的序數 V2 實體解析

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 預測端點回應](#tab/V2)

下列範例顯示**ordinalV2**實體的解決方式。

```json
{
    "query": "what is the second to last choice in the list",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.823669851
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.823669851
        }
    ],
    "entities": [
        {
            "entity": "the second to last",
            "type": "builtin.ordinalV2.relative",
            "startIndex": 8,
            "endIndex": 25,
            "resolution": {
                "offset": "-1",
                "relativeTo": "end"
            }
        }
    ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 預測端點回應](#tab/V3)

下列 JSON 是將 `verbose` 參數設定為 `false`：

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ]
        }
    }
}
```

下列 JSON 是將 `verbose` 參數設定為 `true`：

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ],
            "$instance": {
                "ordinalV2": [
                    {
                        "type": "builtin.ordinalV2.relative",
                        "text": "the second to last",
                        "startIndex": 8,
                        "length": 18,
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

瞭解[百分比](luis-reference-prebuilt-percentage.md)、[電話號碼](luis-reference-prebuilt-phonenumber.md)和[溫度](luis-reference-prebuilt-temperature.md)實體。 
