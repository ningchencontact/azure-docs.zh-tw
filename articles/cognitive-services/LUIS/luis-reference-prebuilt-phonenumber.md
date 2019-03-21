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
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 2a3e84de6d956e4b45945c77c11f789d4455bfe2
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338785"
---
# <a name="phonenumber-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的電話號碼預先建置實體
`phonenumber` 實體會擷取各種不同的電話號碼，包括國碼/區碼。 因為此實體已經定型，所以您不需要將範例語句加入應用程式。 只有 `en-us` 文化特色才支援 `phonenumber` 實體。 

## <a name="types-of-phonenumber"></a>電話號碼類型
電話號碼可從 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) GitHub 存放庫管理

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>解析預先建置的 phonenumber 實體
下列範例顯示解析的 **builtin.phonenumber** 實體。

```json
{
  "query": "my mobile is 00 44 161 1234567",
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
      "entity": "00 44 161 1234567",
      "type": "builtin.phonenumber",
      "startIndex": 13,
      "endIndex": 29,
      "resolution": {
        "value": "00 44 161 1234567"
      }
    }
  ]
}
```


## <a name="next-steps"></a>後續步驟

了解 [percentage](luis-reference-prebuilt-percentage.md)、[number](luis-reference-prebuilt-number.md) 及 [temperature](luis-reference-prebuilt-temperature.md) 實體相關資訊。 
