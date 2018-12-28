---
title: PersonName 預建實體
titleSuffix: Azure Cognitive Services
description: 此文章包含 Language Understanding (LUIS) 中預先建置的 PersonName 實體資訊。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 31dce870e99cbe74e2795a3ba661b0caf92dd48e
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140229"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的 PersonName 預先建置實體
預先建置的 personName 實體會偵測人員名稱。 由於此實體已經定型，因此您不需要將包含 PersonName 的範例語句加入至應用程式意圖。 personName 實體已支援英文與中文[文化特性](luis-reference-prebuilt-entities.md)。

## <a name="resolution-for-personname-entity"></a>personName 實體解析
下列範例說明 **builtin.personName** 實體的解析。

```json
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  }
  "entities": [
    {
      "entity": "Jill Jones",
      "type": "builtin.personName",
      "startIndex": 3,
      "endIndex": 12
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

了解 [email](luis-reference-prebuilt-email.md)、[number](luis-reference-prebuilt-number.md) 及 [ordinal](luis-reference-prebuilt-ordinal.md) 實體相關資訊。 