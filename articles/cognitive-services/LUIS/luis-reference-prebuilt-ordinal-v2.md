---
title: 序數 V2 預先建置的實體
titleSuffix: Language Understanding - Azure Cognitive Services
description: 這篇文章包含序數 V2 預先建置的實體資訊中 Language Understanding (LUIS)。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 862b962f5642e01d7ed8250f49d51a6132447083
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486197"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>序數 V2 預先建置的實體，LUIS 應用程式
序數 V2 數目會展開[序數](luis-reference-prebuilt-ordinal.md)提供相對參照，例如`next`， `last`，和`previous`。 這些不會擷取使用序數預先建置的實體。

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>預先建置的序數 V2 實體解析

### <a name="api-version-2x"></a>API 版本 2.x

下列範例示範的解析度**builtin.ordinalV2**實體。

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

### <a name="preview-api-version-3x"></a>預覽 API 版本 3.x

下列 JSON 是以`verbose`參數設定為`false`:

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

下列 JSON 是以`verbose`參數設定為`true`:

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

## <a name="next-steps"></a>後續步驟

深入了解[百分比](luis-reference-prebuilt-percentage.md)，[電話號碼](luis-reference-prebuilt-phonenumber.md)，並[溫度](luis-reference-prebuilt-temperature.md)實體。 
