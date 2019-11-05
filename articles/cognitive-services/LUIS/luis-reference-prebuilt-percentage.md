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
ms.openlocfilehash: 9e9aa3fc7c0ff76f6b93a8acc67681d1ed7cf4ad
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491227"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的百分比預先建置實體
百分比數字可顯示為分數 `3 1/2` 或百分比 `2%`。 由於此實體已經定型，因此您不需要將包含百分比的範例語句加入至應用程式意圖。 [多種文化特色](luis-reference-prebuilt-entities.md)都支援百分比實體。 

## <a name="types-of-percentage"></a>百分比類型
百分比可從 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114) GitHub 存放庫來管理

## <a name="resolution-for-prebuilt-percentage-entity"></a>解析預先建置的百分比實體

系統會針對查詢傳回下列實體物件：

`set a trigger when my stock goes up 2%`

#### <a name="v3-responsetabv3"></a>[V3 回應](#tab/V3)

下列 JSON 會將 `verbose` 參數設定為 `false`：

```json
"entities": {
    "percentage": [
        2
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 詳細回應](#tab/V3-verbose)
下列 JSON 會將 `verbose` 參數設定為 `true`：

```json
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
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-responsetabv2"></a>[V2 回應](#tab/V2)

下列範例說明 **builtin.percentage** 實體的解析。

```json
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
```
* * * 

## <a name="next-steps"></a>後續步驟

深入瞭解[V3 預測端點](luis-migration-api-v3.md)。

了解 [ordinal](luis-reference-prebuilt-ordinal.md)、[number](luis-reference-prebuilt-number.md) 及 [temperature](luis-reference-prebuilt-temperature.md) 實體相關資訊。 
