---
title: LUIS 預先建置的 email 實體參考 - Azure| Microsoft Docs
titleSuffix: Azure
description: 本文包含 Language Understanding (LUIS) 中預先建置的 email 實體資訊。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 6d05f62ad725a89b0a34b21b8a8d36bb8fa464b1
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441006"
---
# <a name="email-entity"></a>Email 實體
擷取的電子郵件包含語句中的整個電子郵件地址。 因為此實體已經定型，所以您不需要將包含電子郵件的範例語句加入應用程式意圖。 只有 `en-us` 文化特色才支援 email 實體。 

## <a name="resolution-for-prebuilt-email"></a>解析預先建置的電子郵件
下列範例顯示解析的 **builtin.email** 實體。

```JSON
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
      "endIndex": 55
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

了解 [number](luis-reference-prebuilt-number.md)、[ordinal](luis-reference-prebuilt-ordinal.md) 及 [percentage](luis-reference-prebuilt-percentage.md)。 