---
title: Azure Cosmos DB 中的 ORDER BY 子句
description: 適用於 Azure Cosmos DB，以了解 SQL ORDER BY 子句。 使用 SQL 作為 Azure Cosmos DB JSON 查詢語言。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: d0a1ed33d5848c3ed8d5f83af8b320d77fe0dc65
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342736"
---
# <a name="order-by-clause"></a>ORDER BY 子句

選擇性的 ORDER BY 子句指定查詢所傳回的結果的排序順序。

## <a name="syntax"></a>語法
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>引數
  
- `<sort_specification>`  
  
   指定要排列查詢結果集的屬性或運算式。 排序資料行可以指定為名稱或屬性的別名。  
  
   可以指定多個屬性。 屬性名稱必須是唯一的。 ORDER BY 子句中的排序屬性的順序會定義排序的結果集的組織。 也就是說，結果集是依第一個屬性排序，而該排序清單會依次要屬性進行排序，以此類推。  
  
   ORDER BY 子句中參考的屬性名稱必須對應至選取清單中的其中一個屬性或任何模稜兩可之 FROM 子句中指定的集合中定義的屬性。  
  
- `<sort_expression>`  
  
   指定一或多個屬性或運算式，用來排序查詢結果集。  
  
- `<scalar_expression>`  
  
   請參閱[純量運算式](sql-query-scalar-expressions.md)一節以取得詳細資料。  
  
- `ASC | DESC`  
  
   指定特定之資料行中的值，應該以遞增或遞減順序排序。 ASC 從最低值到最高值排序。 DESC 從最高值到最低值排序。 ASC 為預設排序順序。 NULL 值會被視為最低的可能值。  
  
## <a name="remarks"></a>備註  
  
   ORDER BY 子句需要編製索引原則，包含要排序的欄位索引。 Azure Cosmos DB 查詢執行階段支援排序針對屬性名稱，而不是針對計算的屬性。 Azure Cosmos DB 支援多個 ORDER BY 屬性。 若要執行查詢時使用多個 ORDER BY 屬性，您應該定義[複合式索引](index-policy.md#composite-indexes)根據正在排序的欄位。

## <a name="examples"></a>範例

例如，以下是擷取家族的依據居住城市名稱的遞增順序的查詢：

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

結果為：

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

下列查詢會擷取家族`id`中其項目建立日期的順序。 項目`creationDate`數字代表*epoch 時間*，或自 1970 年 1 月 1 日以秒為單位的經過時間。

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

結果為：

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

此外，您可以依多個屬性。 依照多個屬性來排序查詢需要[複合式索引](index-policy.md#composite-indexes)。 請考量下列查詢：

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

此查詢會擷取家族`id`縣 （市） 名稱的遞增順序。 如果多個項目有相同的城市名稱，查詢會依`creationDate`以遞減順序。

## <a name="next-steps"></a>後續步驟

- [開始使用](sql-query-getting-started.md)
- [SELECT 子句](sql-query-select.md)
- [位移 LIMIT 子句](sql-query-offset-limit.md)
