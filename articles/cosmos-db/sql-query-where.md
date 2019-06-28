---
title: Azure Cosmos DB 中的 WHERE 子句
description: 深入了解 SQL WHERE 子句，適用於 Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 6a942e48ffea7785fe971cc2f8fa66e8569ed672
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342950"
---
# <a name="where-clause"></a>WHERE 子句

選擇性的 WHERE 子句 (`WHERE <filter_condition>`) 指定條件的來源 JSON 項目必須符合將它們包含在結果查詢。 JSON 項目必須指定的條件評估為`true`才能視為結果。 索引層使用 WHERE 子句來判斷可以是部分結果的來源項目的最小的子集。
  
## <a name="syntax"></a>語法
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>引數

- `<filter_condition>`  
  
   指定要傳回的文件必須滿足的條件。  
  
- `<scalar_expression>`  
  
   表示要計算之值的運算式。 請參閱[純量運算式](sql-query-scalar-expressions.md)如需詳細資訊。  
  

## <a name="remarks"></a>備註
  
  為了傳回文件，指定為篩選條件的運算式必須評估為 True。 只有在布林值為 True 的情況下才會滿足條件，任何其他值：未定義、Null、False、數字、陣列或物件都不會滿足條件。 

## <a name="examples"></a>範例

下列查詢會要求包含的項目`id`屬性且值為`AndersenFamily`。 它會排除任何項目，並沒有`id`屬性或其值不符合`AndersenFamily`。

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果為：

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>WHERE 子句中的純量運算式

前一個範例已顯示簡單的相等查詢。 SQL API 也支援各種[純量運算式](sql-query-scalar-expressions.md)。 最常用的是二元和一元運算式。 來源 JSON 物件中的屬性參考也是有效的運算式。

您可以使用下列支援的二元運算子：  

|**運算子類型**  | **值** |
|---------|---------|
|算術 | +、-、*、/、% |
|位元    | \|, &, ^, <<, >>, >>> (zero-fill right shift) |
|邏輯    | AND、OR、NOT      |
|比較 | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|字串     |  \|\| (串連) |

下列查詢會使用二元運算子：

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

您也可以使用一元運算子 +、-、 ~，而不是在查詢中，如下列範例所示：

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

您也可以在查詢中使用屬性參考。 例如，`SELECT * FROM Families f WHERE f.isRegistered`會傳回包含屬性的 JSON 項目`isRegistered`值等於`true`。 任何其他值，例如`false`， `null`， `Undefined`， `<number>`， `<string>`， `<object>`，或`<array>`，從結果中排除的項目。 

## <a name="next-steps"></a>後續步驟

- [開始使用](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [FROM 子句](sql-query-from.md)