---
title: 電話號碼預先建立的實體-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding (LUIS) 中預先建置的 phonenumber 實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 1cc7469bf6b29ed864fac3955dc8770aa879f84d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499536"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的電話號碼預建實體
`phonenumber` 實體會擷取各種不同的電話號碼，包括國碼/區碼。 因為此實體已經定型，所以您不需要將範例語句加入應用程式。 只有 `phonenumber` 文化特色才支援 `en-us` 實體。 

## <a name="types-of-a-phone-number"></a>電話號碼的類型
`Phonenumber` 是從辨識器[文字](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)的 GitHub 存放庫進行管理

## <a name="resolution-for-this-prebuilt-entity"></a>此預建實體的解決方式

系統會針對查詢傳回下列實體物件：

`my mobile is 1 (800) 642-7676`

#### <a name="v3-responsetabv3"></a>[V3 回應](#tab/V3)

下列 JSON 會將 `verbose` 參數設定為 `false`：

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 詳細回應](#tab/V3-verbose)
下列 JSON 會將 `verbose` 參數設定為 `true`：

```json
"entities": {
    "phonenumber": [
        "1 (800) 642-7676"
    ],
    "$instance": {

        "phonenumber": [
            {
                "type": "builtin.phonenumber",
                "text": "1 (800) 642-7676",
                "startIndex": 13,
                "length": 16,
                "score": 1.0,
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

下列範例顯示解析的 **builtin.phonenumber** 實體。

```json
"entities": [
    {
        "entity": "1 (800) 642-7676",
        "type": "builtin.phonenumber",
        "startIndex": 13,
        "endIndex": 28,
        "resolution": {
            "score": "1",
            "value": "1 (800) 642-7676"
        }
    }
]
```
* * * 

## <a name="next-steps"></a>後續步驟

深入瞭解[V3 預測端點](luis-migration-api-v3.md)。

了解 [percentage](luis-reference-prebuilt-percentage.md)、[number](luis-reference-prebuilt-number.md) 及 [temperature](luis-reference-prebuilt-temperature.md) 實體相關資訊。 
