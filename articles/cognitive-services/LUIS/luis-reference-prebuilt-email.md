---
title: LUIS 預先建置的 email 實體參考 - Azure| Microsoft Docs
titleSuffix: Azure
description: 本文包含 Language Understanding (LUIS) 中預先建置的 email 實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 2b11446c84ede0e8ecfce23eda1026919777fc66
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "65072064"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的電子郵件預先建置實體
擷取的電子郵件包含語句中的整個電子郵件地址。 因為此實體已經定型，所以您不需要將包含電子郵件的範例語句加入應用程式意圖。 只有 `en-us` 文化特色才支援 email 實體。 

## <a name="resolution-for-prebuilt-email"></a>解析預先建置的電子郵件

### <a name="api-version-2x"></a>API 版本 2.x

下列範例顯示解析的 **builtin.email** 實體。

```json
{
  "query": "please send the information to patti.owens@microsoft.com",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.811592042
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.811592042
    }
  ],
  "entities": [
    {
      "entity": "patti.owens@microsoft.com",
      "type": "builtin.email",
      "startIndex": 31,
      "endIndex": 55,
      "resolution": {
        "value": "patti.owens@microsoft.com"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>預覽 API 版本 3.x

下列 JSON 是以`verbose`參數設定為`false`:

```json
{
    "query": "please send the information to patti.owens@microsoft.com",
    "prediction": {
        "normalizedQuery": "please send the information to patti.owens@microsoft.com",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5023781
            }
        },
        "entities": {
            "email": [
                "patti.owens@microsoft.com"
            ]
        }
    }
}
```


下列 JSON 是以`verbose`參數設定為`true`:

```json
{
    "query": "please send the information to patti.owens@microsoft.com",
    "prediction": {
        "normalizedQuery": "please send the information to patti.owens@microsoft.com",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5023781
            }
        },
        "entities": {
            "email": [
                "patti.owens@microsoft.com"
            ],
            "$instance": {
                "email": [
                    {
                        "type": "builtin.email",
                        "text": "patti.owens@microsoft.com",
                        "startIndex": 31,
                        "length": 25,
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

了解 [number](luis-reference-prebuilt-number.md)、[ordinal](luis-reference-prebuilt-ordinal.md) 及 [percentage](luis-reference-prebuilt-percentage.md)。 
