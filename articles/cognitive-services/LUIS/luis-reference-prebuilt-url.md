---
title: URL 預先建立的實體-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding (LUIS) 中預先建置的 url 實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a4b2db4accb638e6e4147a19d94d0a323cfe7c81
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560117"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的 URL 預先建置實體
URL 實體會擷取含有網域名稱或 IP 位址的 URL。 因為此實體已經定型，所以您不需要將包含 URL 的範例語句加入應用程式。 只有 `en-us` 文化特色才支援 URL 實體。 

## <a name="types-of-urls"></a>URL 類型
URL 可從 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) GitHub 存放庫管理

## <a name="resolution-for-prebuilt-url-entity"></a>解析預先建置的 URL 實體

### <a name="api-version-2x"></a>API 2.x 版

下列範例顯示解析的 **builtin.url** 實體。

```json
{
  "query": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.781975448
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.781975448
    }
  ],
  "entities": [
    {
      "entity": "https://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>預覽 API 版本3。x

下列 JSON 是`verbose`將參數設定為`false`:

```json
{
    "query": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
    "prediction": {
        "normalizedQuery": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.421936184
            }
        },
        "entities": {
            "url": [
                "https://www.luis.ai"
            ]
        }
    }
}
```

下列 JSON 是`verbose`將參數設定為`true`:

```json
{
    "query": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
    "prediction": {
        "normalizedQuery": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.421936184
            }
        },
        "entities": {
            "url": [
                "https://www.luis.ai"
            ],
            "$instance": {
                "url": [
                    {
                        "type": "builtin.url",
                        "text": "https://www.luis.ai",
                        "startIndex": 0,
                        "length": 19,
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

了解 [ordinal](luis-reference-prebuilt-ordinal.md)、[number](luis-reference-prebuilt-number.md) 和 [temperature](luis-reference-prebuilt-temperature.md) 實體相關資訊。
