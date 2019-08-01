---
title: 簡單實體類型-LUIS
titleSuffix: Azure Cognitive Services
description: 簡單實體是描述單一概念並從機器學習內容學習到的一般實體。 因為簡單實體通常是名稱 (例如公司名稱、產品名稱或其他類別的名稱), 所以在使用簡單實體來提升所使用名稱的信號時, 請新增片語清單。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: c1514b6cd512924a162a524d11e888055fa06514
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563199"
---
# <a name="simple-entity"></a>簡單實體 

簡單實體是描述單一概念並從機器學習內容學習到的一般實體。 由於簡單實體通常是名稱，例如公司名稱、產品名稱或其他類別的名稱，因此，使用簡單實體時可加入[片語清單](luis-concept-feature.md)，以提升所用名稱所代表的信號。 

**實體適用于下列情況:**

* 格式不一致但表示相同內容的資料。 

![簡單實體](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>範例 JSON

`Bob Jones wants 3 meatball pho`

在先前的語句中，`Bob Jones` 被標記為簡單的 `Customer` 實體。

從端點傳回的資料會包含實體名稱、從語句探索到的文字，所探索文字的位置，以及分數：

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|資料物件|實體名稱|值|
|--|--|--|
|簡單實體|`Customer`|`bob jones`|

## <a name="next-steps"></a>後續步驟

在本[教學](luis-quickstart-primary-and-secondary-data.md)課程中, 使用**簡單實體**, 從語句中將員工作業名稱的機器學習資料解壓縮。 若要增加解壓縮精確度, 請新增簡單實體特定詞彙的[片語清單](luis-concept-feature.md)。