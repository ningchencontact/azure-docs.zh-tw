---
title: 複合實體類型
titleSuffix: Language Understanding - Azure Cognitive Services
description: 複合實體是由其他實體所組成, 例如預建實體、簡單、正則運算式和列出實體。 個別實體會構成一個完整的提體。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: 55dcc5666e63b8a87ddcb13696991fe02843fbbd
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480299"
---
# <a name="composite-entity"></a>複合實體 

複合實體是由其他實體所組成, 例如預建實體、簡單、正則運算式和列出實體。 個別實體會構成一個完整的提體。 

**此實體適用于資料:**

* 彼此相關。 
* 在語句的內容中彼此相關。
* 使用各種實體類型。
* 需要由用戶端應用程式當作一個資訊單位進行分組和處理。
* 有各種使用者語句需要使用機器學習。

![複合實體](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>範例 JSON

請考慮使用預建`number`和`Location::ToLocation`下列語句的複合實體:

`book 2 tickets to paris`

請注意，數字 `2` 與 ToLocation `paris` 之間有不屬於任何實體的單字。 [LUIS](luis-reference-regions.md) 網站中用於所標示語句的綠色底線表示複合實體。

![複合實體](./media/luis-concept-data-extraction/composite-entity.png)

複合實體會在 `compositeEntities` 陣列中傳回，而複合項目內的所有實體則會一併在 `entities` 陣列中傳回：

```JSON

"entities": [
    {
    "entity": "2 tickets to cairo",
    "type": "ticketInfo",
    "startIndex": 0,
    "endIndex": 17,
    "score": 0.67200166
    },
    {
    "entity": "2",
    "type": "builtin.number",
    "startIndex": 0,
    "endIndex": 0,
    "resolution": {
        "subtype": "integer",
        "value": "2"
    }
    },
    {
    "entity": "cairo",
    "type": "builtin.geographyV2",
    "startIndex": 13,
    "endIndex": 17
    }
],
"compositeEntities": [
    {
    "parentType": "ticketInfo",
    "value": "2 tickets to cairo",
    "children": [
        {
        "type": "builtin.geographyV2",
        "value": "cairo"
        },
        {
        "type": "builtin.number",
        "value": "2"
        }
    ]
    }
]
```    

|資料物件|實體名稱|值|
|--|--|--|
|預先建置的實體 - number|"builtin.number"|"2"|
|預先建立的實體-GeographyV2|"Location::ToLocation"|"paris"|

## <a name="next-steps"></a>後續步驟

在本[教學](luis-tutorial-composite-entity.md)課程中, 新增**複合實體**以將各種類型的已解壓縮資料組合成單一包含實體。 用戶端應用程式可藉由組合資料，輕鬆地擷取不同資料類型的相關資料。
