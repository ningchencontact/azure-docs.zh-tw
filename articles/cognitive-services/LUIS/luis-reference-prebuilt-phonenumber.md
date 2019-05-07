---
title: 電話號碼預先建置的實體
titleSuffix: Azure
description: 本文包含 Language Understanding (LUIS) 中預先建置的 phonenumber 實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 43d0b855c25ed10b074d99b247ee56dc2ba7769b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146186"
---
# <a name="phone-number-prebuilt-entity-for-a-luis-app"></a>電話號碼預先建置的實體 LUIS 應用程式
`phonenumber` 實體會擷取各種不同的電話號碼，包括國碼/區碼。 因為此實體已經定型，所以您不需要將範例語句加入應用程式。 只有 `en-us` 文化特色才支援 `phonenumber` 實體。 

## <a name="types-of-a-phone-number"></a>類型的電話號碼
`Phonenumber` 從管理[辨識文字](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml)GitHub 存放庫

## <a name="resolution-for-this-prebuilt-entity"></a>這個預先建置的實體解析

### <a name="api-version-2x"></a>API 版本 2.x

下列範例顯示解析的 **builtin.phonenumber** 實體。

```json
{
  "query": "my mobile is 1 (800) 642-7676",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
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
}
```

### <a name="preview-api-version-3x"></a>預覽 API 版本 3.x

下列 JSON 是以`verbose`參數設定為`false`:

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
        "entities": {
            "phonenumber": [
                "1 (800) 642-7676"
            ]
        }
    }
}
```

下列 JSON 是以`verbose`參數設定為`true`:

```json
{
    "query": "my mobile is 1 (800) 642-7676",
    "prediction": {
        "normalizedQuery": "my mobile is 1 (800) 642-7676",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.592748761
            }
        },
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
                        "score": 1,
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

了解 [percentage](luis-reference-prebuilt-percentage.md)、[number](luis-reference-prebuilt-number.md) 及 [temperature](luis-reference-prebuilt-temperature.md) 實體相關資訊。 
