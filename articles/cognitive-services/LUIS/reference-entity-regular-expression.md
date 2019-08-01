---
title: 正則運算式實體類型-LUIS
titleSuffix: Azure Cognitive Services
description: 規則運算式最適用於未經處理的語句文字。 這會忽略大小寫並忽略文化特性變體。  在字元等級而非權杖等級的拼字檢查修改之後，才會套用規則運算式比對。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: 82cce359f2161800c53ccce7cdb0342bba759d43
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559934"
---
# <a name="regular-expression-entity"></a>規則運算式實體 

正則運算式實體會根據您所提供的正則運算式模式來解壓縮實體。

規則運算式最適用於未經處理的語句文字。 這會忽略大小寫並忽略文化特性變體。  在字元等級而非權杖等級的拼字檢查修改之後，才會套用規則運算式比對。 如果規則運算式太複雜 (例如使用許多方括號)，您便無法將運算式新增到模型中。 使用部分, 但不是所有的[.Net Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions)程式庫。 

**實體適用于下列情況:**

* 以任何一致的變化來一致地格式化的資料。
* 規則運算式不需要 2 個層級以上的巢狀結構。 

![規則運算式實體](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>使用考慮

正則運算式可能符合您預期的比對。 其中一個範例是數值字組比對`one` , 例如和。 `two` 例如, 下列 RegEx 會比對數位`one`和其他數位:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
``` 

這個 RegEx 運算式也會比對以這些數位結尾的任何單字, `phone`例如。 若要修正這類問題, 請確定 RegEx 相符專案會考慮單字界限。 在下列 RegEx 中, 會使用此範例中使用字邊界的 RegEx:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>範例 JSON

當使用`kb[0-9]{6}`做為正則運算式實體定義時, 下列 JSON 回應是一個範例語句, 其中包含針對查詢`When was kb123456 published?`所傳回的正則運算式實體:

```JSON
{
  "query": "when was kb123456 published?",
  "topScoringIntent": {
    "intent": "FindKBArticle",
    "score": 0.933641255
  },
  "intents": [
    {
      "intent": "FindKBArticle",
      "score": 0.933641255
    },
    {
      "intent": "None",
      "score": 0.04397359
    }
  ],
  "entities": [
    {
      "entity": "kb123456",
      "type": "KB number",
      "startIndex": 9,
      "endIndex": 16
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

在本[教學](luis-quickstart-intents-regex-entity.md)課程中, 您可以使用**正則運算式**實體來建立應用程式, 以從語句中解壓縮一致格式的資料。