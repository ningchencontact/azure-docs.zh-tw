---
title: 清單實體類型
titleSuffix: Language Understanding - Azure Cognitive Services
description: 清單實體代表一組固定的封閉式相關字組及其同義字。 LUIS 並不會探索清單實體的額外值。 使用 [建議] 功能, 根據目前的清單查看新單字的建議。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: c278a72327d476be8963b10db5e8231b6d859a4a
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480305"
---
# <a name="list-entity"></a>清單實體 

清單實體代表一組固定的封閉式相關字組及其同義字。 LUIS 並不會探索清單實體的額外值。 使用**建議**功能，以根據目前的清單查看適用於新字組的建議。 如果有多個清單實體具有相同的值，則在端點查詢中會傳回每個實體。 

清單實體不是機器學習。 它是全文相符的項目。 LUIS 會將與任何清單中項目相符的項目，在回應中標示為實體。 

**當文字資料時, 實體就很適合:**

* 是已知的組合。
* 不常變更。 如果您需要經常變更清單, 或想要讓清單自我展開, 使用片語清單提升的簡單實體是較好的選擇。 
* 此組合不會超過此實體類型的最大 LUIS [界限](luis-boundaries.md)。
* 語句中的文字是與同義字或正式名稱完全相符的項目。 LUIS 不會將清單用於完全相符之文字項目以外的範圍。 不會使用清單實體解析模糊比對、不區分大小寫、詞幹分析、複數和其他變化。 若要管理變化，請考慮使用[模式](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance)並搭配選擇性的文字語法。

![清單實體](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>範例 JSON

假設應用程式具有一個名為 `Cities` 的清單，其中可允許城市名稱的各種變化，包括機場城市 (Sea-tac)、機場代碼 (SEA)、郵遞區號 (98101) 及電話區碼 (206)。

|清單項目|項目同義字|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`、`roissy`、`ory`、`75001`、`1`、`+33`|

`book 2 tickets to paris`

在上述語句中，`paris` 一字會對應至 `Cities` 清單實體中的 paris 項目。 此清單實體會同時比對項目的正規化名稱及項目同義字。

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 22,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

使用 Paris 同義字的另一個範例語句：

`book 2 tickets to roissy`

```JSON
"entities": [
  {
    "entity": "roissy",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 23,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

|資料物件|實體名稱|值|
|--|--|--|
|簡單實體|`Customer`|`bob jones`|

## <a name="next-steps"></a>後續步驟

在本[教學](luis-quickstart-intent-and-list-entity.md)課程中, 您將瞭解如何使用**清單實體**, 從已知專案清單中解壓縮完全相符的文字。 
