---
title: Azure Cosmos DB 中的 SELECT 子句
description: 適用於 Azure Cosmos DB，以了解 SQL SELECT 子句。 使用 SQL 作為 Azure Cosmos DB JSON 查詢語言。
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 84d0212f7f212b4554b506726e027fe51f795eea
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342534"
---
# <a name="select-clause"></a>SELECT 子句

每個查詢都包含 SELECT 子句和選擇性[FROM](sql-query-from.md)並[其中](sql-query-where.md)子句，根據 ANSI SQL 標準。 一般而言，會列舉 FROM 子句中的來源，而 WHERE 子句來擷取 JSON 項目子集在來源上套用篩選。 SELECT 子句，然後投射選取清單中要求的 JSON 值。

## <a name="syntax"></a>語法

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | [DISTINCT] <object_property_list>   
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
## <a name="arguments"></a>引數
  
- `<select_specification>`  

  要針對此結果集選取的屬性或值。  
  
- `'*'`  

  指定應該在不做出任何變更的情況下指定該值。 特別是若已處理的值是物件，則會擷取所有屬性。  
  
- `<object_property_list>`  
  
  指定要擷取的屬性清單。 每個傳回的值都會是具備指定屬性的物件。  
  
- `VALUE`  

  指定應該擷取 JSON 值，而非擷取完整的 JSON 物件。 這與 `<property_list>` 不同，不會在物件中包裝預估的值。  
 
- `DISTINCT`
  
  指定應移除重複項目之預計屬性。  

- `<scalar_expression>`  

  表示要計算之值的運算式。 請參閱[純量運算式](sql-query-scalar-expressions.md)一節以取得詳細資料。  

## <a name="remarks"></a>備註

`SELECT *` 語法只有在 FROM 子句已明顯宣告一個別名時才會有效。 `SELECT *` 提供一個身分識別投影，在無需投影的情況下會非常實用。 SELECT * 只有在已指定 FROM 子句且只導入單一輸入來源時才有效。  
  
請注意，`SELECT <select_list>` 和 `SELECT *` 為「語法捷徑」，可以使用如下所示的簡單 SELECT 陳述式另外表示。  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   相當於：  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   相當於：  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>範例

下列的 SELECT 查詢範例會傳回`address`從`Families`其`id`符合`AndersenFamily`:

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

### <a name="quoted-property-accessor"></a>加上引號的屬性存取子
您可以使用引號括住的屬性運算子 [] 的屬性。 例如， `SELECT c.grade` and `SELECT c["grade"]` 是相等的。 此語法適合用來逸出此屬性，包含空格、 特殊字元，或具有相同名稱的 SQL 關鍵字或保留的字。

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>巢狀屬性

下列範例專案的兩個巢狀的屬性`f.address.state`和`f.address.city`。

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果為：

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```
### <a name="json-expressions"></a>JSON 運算式

投射也支援 JSON 運算式，如下列範例所示：

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果為：

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

在上述範例中，SELECT 子句需要建立 JSON 物件，以及由於此範例提供沒有索引鍵，子句會使用隱含的引數的變數名稱`$1`。 下列查詢會傳回兩個隱含引數變數：`$1`和`$2`。

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果為：

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>後續步驟

- [開始使用](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [WHERE 子句](sql-query-where.md)