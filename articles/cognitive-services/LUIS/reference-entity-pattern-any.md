---
title: Pattern. 任何實體類型-LUIS
titleSuffix: Azure Cognitive Services
description: Pattern.any 是僅用於模式範本語句的可變長度預留位置，用來標記實體開始及結束的位置。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: cda6c724a36a73dc34c2bf8e7158e3e3ec92d46b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563225"
---
# <a name="patternany-entity"></a>Pattern.any 實體 

Pattern.any 是僅用於模式範本語句的可變長度預留位置，用來標記實體開始及結束的位置。  

Pattern.any 實體需要在[模式](luis-how-to-model-intent-pattern.md)範本裡的範例 (而不是意圖裡使用者所提供的範例) 中進行標記。

**實體適用于下列情況:**

* 實體的結尾可能會與語句的其餘文字混淆。 

## <a name="usage"></a>使用量

如果用戶端應用程式要根據書名來搜尋書籍，pattern.any 會擷取完整的書名。 為搜尋此書而使用 pattern.any 的範本語句是 `Was {BookTitle} written by an American this year[?]`。 

下表中，每個資料列都有兩個版本的語句。 最上層的語句是 LUIS 最初看到語句的方式。 這並不清楚書籍標題開始和結束的位置。 底部的語句會使用模式。任何實體都會標記實體的開頭和結尾。 

|語句搭配以粗體顯示的實體|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Was **The Man Who Mistook His Wife for a Hat and Other Clinical Tales** written by an American this year?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Was **Half Asleep in Frog Pajamas** written by an American this year?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Was **The Particular Sadness of Lemon Cake:A Novel** written by an American this year?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>Was **There's A Wocket In My Pocket!** written by an American this year?|
||

## <a name="example-json"></a>範例 JSON

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

在本[教學](luis-tutorial-pattern-any.md)課程中, 使用**模式。任何**實體都會從語句中解壓縮資料, 其中語句的格式正確, 而資料的結尾可能會與語句的其餘單字輕鬆混淆。