---
title: Azure Cosmos DB 中的 ORDER BY 子句
description: 瞭解 Azure Cosmos DB 的 SQL ORDER BY 子句。 使用 SQL 做為 Azure Cosmos DB JSON 查詢語言。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 14f61d14b59dca4bcf2e0f4b93e918f101a61833
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326842"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 ORDER BY 子句

選擇性的 ORDER BY 子句會指定查詢所傳回之結果的排序次序。

## <a name="syntax"></a>語法
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>引數
  
- `<sort_specification>`  
  
   指定要排列查詢結果集的屬性或運算式。 排序資料行可以指定為名稱或屬性別名。  
  
   可以指定多個屬性。 屬性名稱必須是唯一的。 ORDER BY 子句中排序屬性的順序會定義排序結果集的組織。 也就是說，結果集是依第一個屬性排序，而該排序清單會依次要屬性進行排序，以此類推。  
  
   ORDER BY 子句中所參考的屬性名稱，必須對應至 select 清單中的屬性，或在 FROM 子句中指定之集合中所定義的屬性，而不會有任何多義性。  
  
- `<sort_expression>`  
  
   指定要對其排序查詢結果集的一或多個屬性或運算式。  
  
- `<scalar_expression>`  
  
   請參閱[純量運算式](sql-query-scalar-expressions.md)一節以取得詳細資料。  
  
- `ASC | DESC`  
  
   指定特定之資料行中的值，應該以遞增或遞減順序排序。 ASC 從最低值到最高值排序。 DESC 從最高值到最低值排序。 ASC 為預設排序順序。 NULL 值會被視為最低的可能值。  
  
## <a name="remarks"></a>備註  
  
   ORDER BY 子句要求索引編制原則必須包含要排序之欄位的索引。 Azure Cosmos DB 查詢執行時間支援針對屬性名稱進行排序，而不是針對計算的屬性。 Azure Cosmos DB 支援多個 ORDER BY 屬性。 若要以多個 ORDER BY 屬性執行查詢，您應該在要排序的欄位上定義[複合索引](index-policy.md#composite-indexes)。

## <a name="examples"></a>範例

例如，以下是以居住城市名稱的遞增順序來抓取家族的查詢：

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

下列查詢會依其專案建立日期的順序，來抓取系列 `id`。 [專案 `creationDate`] 是代表*epoch 時間*的數位，或1970（以秒為單位）之後經過的時間。

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

此外，您可以依多個屬性來排序。 依多個屬性排序的查詢需要[複合索引](index-policy.md#composite-indexes)。 請考量下列查詢：

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

此查詢會依城市名稱的遞增順序來抓取系列 `id`。 如果多個專案具有相同的城市名稱，則查詢會依 `creationDate` 的遞減順序排序。

## <a name="next-steps"></a>後續步驟

- [開始使用](sql-query-getting-started.md)
- [SELECT 子句](sql-query-select.md)
- [OFFSET LIMIT 子句](sql-query-offset-limit.md)
