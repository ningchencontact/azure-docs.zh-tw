---
title: 地理 V2 預建實體-LUIS
titleSuffix: Azure Cognitive Services
description: 此文章包含 Language Understanding (LUIS) 中的 geographyV2 預建實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 2628dd30e09f4c52517ccb0a284cda235fb0c33f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560225"
---
# <a name="geographyv2-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的 GeographyV2 預先建置實體
預建 geographyV2 實體可偵測位置。 因為此實體已經定型，所以您不需要將包含 GeographyV2 的範例語句加入應用程式意圖。 英文[文化特性](luis-reference-prebuilt-entities.md)支援 GeographyV2 實體。

## <a name="subtypes"></a>子類型
地理位置都有子類型：

|Subtype|用途|
|--|--|
|`poi`|感興趣的點|
|`city`|城市名稱|
|`countryRegion`|國家或地區名稱|
|`continent`|洲別名稱|
|`state`|州或省名稱|


## <a name="resolution-for-geographyv2-entity"></a>GeographyV2 實體解析

### <a name="api-version-2x"></a>API 2.x 版

下列範例示範如何解析 **builtin.geographyV2** 實體。

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.8008023
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.8008023
        }
    ],
    "entities": [
        {
            "entity": "the sphinx",
            "type": "builtin.geographyV2.poi",
            "startIndex": 18,
            "endIndex": 27
        },
        {
            "entity": "gizah",
            "type": "builtin.geographyV2.city",
            "startIndex": 32,
            "endIndex": 36
        },
        {
            "entity": "egypt",
            "type": "builtin.geographyV2.countryRegion",
            "startIndex": 38,
            "endIndex": 42
        },
        {
            "entity": "africa",
            "type": "builtin.geographyV2.continent",
            "startIndex": 47,
            "endIndex": 52
        },
        {
            "entity": "texas",
            "type": "builtin.geographyV2.state",
            "startIndex": 72,
            "endIndex": 76
        },
        {
            "entity": "carol",
            "type": "builtin.personName",
            "startIndex": 0,
            "endIndex": 4
        }
    ]
} 
```

### <a name="preview-api-version-3x"></a>預覽 API 版本3。x

下列 JSON 是`verbose`將參數設定為`false`:

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "prediction": {
        "normalizedQuery": "carol is visiting the sphinx in gizah egypt in africa before heading to texas",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5115521
            }
        },
        "entities": {
            "geographyV2": [
                "the sphinx",
                "gizah",
                "egypt",
                "africa",
                "texas"
            ]
        }
    }
}
```

下列 JSON 是`verbose`將參數設定為`true`:

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "prediction": {
        "normalizedQuery": "carol is visiting the sphinx in gizah egypt in africa before heading to texas",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5115521
            }
        },
        "entities": {
            "geographyV2": [
                "the sphinx",
                "gizah",
                "egypt",
                "africa",
                "texas"
            ],
            "$instance": {
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2",
                        "text": "the sphinx",
                        "startIndex": 18,
                        "length": 10,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "gizah",
                        "startIndex": 32,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "egypt",
                        "startIndex": 38,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "africa",
                        "startIndex": 47,
                        "length": 6,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "texas",
                        "startIndex": 72,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>後續步驟

了解 [email](luis-reference-prebuilt-email.md)、[number](luis-reference-prebuilt-number.md) 及 [ordinal](luis-reference-prebuilt-ordinal.md) 實體相關資訊。 
