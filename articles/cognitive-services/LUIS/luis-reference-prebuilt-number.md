---
title: 數字預先建置的實體
titleSuffix: Azure
description: 本文包含 Language Understanding (LUIS) 中預先建置的 number 實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 83f7cc7c0da2682244fa9c4e0e2b153aff2e2380
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339295"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的數字預先建置實體
數值用來量化、表達及描述各項資訊的方式眾多。 本文只涵蓋部分可能的範例。 LUIS 可解譯使用者語句的變化，並傳回一致的數值。 因為此實體已經定型，所以您不需要將包含數字的範例語句加入應用程式意圖。 

## <a name="types-of-number"></a>數字類型
數字可從 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) GitHub 存放庫管理

## <a name="examples-of-number-resolution"></a>數字解析範例

| 語句        | 實體   | 解決方案 |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


LUIS 會在所傳回 JSON 回應的 `resolution` 欄位中，包含 **`builtin.number`** 實體的辨識值。

## <a name="resolution-for-prebuilt-number"></a>解析預先建置的數字
下列範例顯示來自 LUIS 的 JSON 回應，其中包含「兩打」語句的值 24。

```json
{
  "query": "order two dozen eggs",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.105443209
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.105443209
    },
    {
      "intent": "OrderFood",
      "score": 0.9468431361
    },
    {
      "intent": "Help",
      "score": 0.000399122015
    },
  ],
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "subtype": "integer",
        "value": "24"
      }
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

了解 [currency](luis-reference-prebuilt-currency.md)、[ordinal](luis-reference-prebuilt-ordinal.md) 及 [percentage](luis-reference-prebuilt-percentage.md) 相關資訊。 
