---
title: 預先建置的 URL 實體
titleSuffix: Azure
description: 本文包含 Language Understanding (LUIS) 中預先建置的 url 實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 3e5f6edf213838345c21598213b4fc2065e66335
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884132"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的 URL 預先建置實體
URL 實體會擷取含有網域名稱或 IP 位址的 URL。 因為此實體已經定型，所以您不需要將包含 URL 的範例語句加入應用程式。 只有 `en-us` 文化特色才支援 URL 實體。 

## <a name="types-of-urls"></a>URL 類型
URL 可從 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) GitHub 存放庫管理

## <a name="resolution-for-prebuilt-url-entity"></a>解析預先建置的 URL 實體
下列範例顯示解析的 **builtin.url** 實體。

```json
{
  "query": "http://www.luis.ai is a great cognitive services example of artificial intelligence",
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
      "entity": "http://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

了解 [ordinal](luis-reference-prebuilt-ordinal.md)、[number](luis-reference-prebuilt-number.md) 和 [temperature](luis-reference-prebuilt-temperature.md) 實體相關資訊。
