---
title: 適用於 Azure Cosmos DB 的 SQL 查詢
description: 了解 Azure Cosmos DB 的 SQL 語法、資料庫概念及 SQL 查詢。 将 SQL 用作 Azure Cosmos DB JSON 查询语言。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: a5cc6bfca67f3d90467fa2339bc991c1f0bbeadf
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148941"
---
# <a name="sql-query-examples-for-azure-cosmos-db"></a>Azure Cosmos DB 的 SQL 查詢範例

Azure Cosmos DB SQL API 帐户支持使用 结构化查询语言 (SQL) 作为 JSON 查询语言来查询项。 Azure Cosmos DB 查询语言的设计目标是：

* 支持用户最熟悉的、最流行的 SQL 查询语言，而不是要发明一种新的查询语言。 SQL 提供正式的编程模型用于对 JSON 项进行丰富查询。  

* 使用 JavaScript 的编程模型作为查询语言的基础。 JavaScript 的类型系统、表达式计算和函数调用是 SQL API 的根。 这些根为关系投影、跨 JSON 项的分层导航、自联接、空间查询以及调用完全采用 JavaScript 编写的用户定义的函数 (UDF) 等功能提供自然编程模型。

本文基于简单的 JSON 项来逐步讲解一些示例 SQL 查询。 若要详细了解 Azure Cosmos DB SQL 语言语法，请参阅 [SQL 语法参考](sql-api-query-reference.md)。

## <a id="GettingStarted"></a>開始使用 SQL 查詢

在 Cosmos DB SQL API 帐户中，创建名为 `Families` 的容器。 在该容器中创建两个简单的 JSON 项，然后针对这些项运行几个简单的查询。

### <a name="create-json-items"></a>创建 JSON 项

以下代码创建两个有关家庭的简单 JSON 项。 Andersen 和 Wakefield 家庭的简单 JSON 项包括父母、孩子及其宠物、地址和注册信息。 第一个项包含字符串、数字、布尔、数组和嵌套属性。


```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

第二个项使用 `givenName` 和 `familyName`，而不是使用 `firstName` 和 `lastName`。

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female", 
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>查询 JSON 项

尝试对此 JSON 数据执行一些查询来了解 Azure Cosmos DB 的 SQL 查询语言的一些重要方面。

以下查询返回其中的 `id` 字段与 `AndersenFamily` 匹配的项。 由于它是一个 `SELECT *` 查询，因此该查询的输出是完整的 JSON 项。 有关 SELECT 语法的详细信息，请参阅 [SELECT 语句](sql-api-query-reference.md#select-query)。 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

查询结果为： 

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

以下查询将 JSON 输出的格式重新设置为不同的形式。 当地址中的城市名称与州名称相同时，该查询将使用两个选定的字段 `Name` 和 `City` 来投影新的 JSON `Family` 对象。 “NY, NY”符合这种情况。

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

查询结果为：

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

下列查詢會傳回家族中的所有指定之名稱的子系其`id`符合`WakefieldFamily`依據居住城市排序。

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

結果為：

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

上述示例演示了 Cosmos DB 查询语言的几个方面：  

* 由于 SQL API 适用于 JSON 值，因此它可以处理三种形式的实体，而不是行和列。 可以引用任意深度的树节点（例如 `Node1.Node2.Node3…..Nodem`），类似于 ANSI SQL 中的 `<table>.<column>` 的两部分引用。

* 由于查询语言适用于无架构数据，因此，必须动态绑定类型系统。 相同的運算式在不同的項目上可能會產生不同的類型。 查询的结果是有效的 JSON 值，但不保证它是固定的架构。  

* Azure Cosmos DB 只支援嚴謹的 JSON 項目。 类型系统和表达式仅限于处理 JSON 类型。 有关详细信息，请参阅 [JSON 规范](https://www.json.org/)。  

* Cosmos DB 容器是 JSON 項目的無結構描述集合。 容器项内部以及跨容器项的关系是按包含关系隐式捕获的，而不是按主键和外键关系捕获的。 此特性对于本文稍后要讨论的项内联接非常重要。

## <a id="SelectClause"></a>SELECT 子句

每个查询按 ANSI SQL 标准由 SELECT 子句和可选的 FROM 和 WHERE 子句组成。 通常，将会枚举 FROM 子句中的源，WHERE 子句对该源应用一个筛选器，以检索 JSON 项的子集。 然后，SELECT 子句在 select 列表中投影请求的 JSON 值。 有关语法的详细信息，请参阅 [SELECT 语句](sql-api-query-reference.md#select-query)。

以下 SELECT 查询示例从 `id` 匹配 `AndersenFamily` 的 `Families` 中返回 `address`：

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

## <a id="EscapingReservedKeywords"></a>加上引號的屬性存取子
可以使用带引号的属性运算符 [] 访问属性。 例如， `SELECT c.grade` and `SELECT c["grade"]` 是相等的。 此语法很适合用于转义包含空格和特殊字符的属性，或者其名称与 SQL 关键字或保留字相同的属性。

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="nested-properties"></a>巢狀屬性

以下示例投影两个嵌套属性：`f.address.state` 和 `f.address.city`。

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

## <a name="json-expressions"></a>JSON 表达式

投影也支持 JSON 表达式，如以下示例所示：

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

在上述示例中，SELECT 子句需要创建一个 JSON 对象；由于该示例未提供键，因此子句使用了隐式参数变量名称 `$1`。 以下查询返回两个隐式参数变量：`$1` 和 `$2`。

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

## <a id="ValueKeyword"></a>VALUE 關鍵字

VALUE 关键字提供一种只返回 JSON 值的方式。 例如，下面所示的查询返回标量表达式 `"Hello World"` 而不是 `{$1: "Hello World"}`：

```sql
    SELECT VALUE "Hello World"
```

以下查询返回不带 `address` 标签的 JSON 值：

```sql
    SELECT VALUE f.address
    FROM Families f
```

結果為：

```json
    [
      {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan",
        "city": "NY"
      }
    ]
```

以下示例演示如何返回 JSON 基元值（JSON 树的叶级别）：


```sql
    SELECT VALUE f.address.state
    FROM Families f
```

結果為：

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="DistinctKeyword"></a>DISTINCT 關鍵字

DISTINCT 關鍵字會排除在查詢投影中的重複項目。

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

在此範例中，查詢會投射針對每個姓氏的值。

結果為：

```json
[
    "Andersen"
]
```

您也可以投影唯一物件。 在此情況下，[lastName] 欄位不存在於其中兩個文件，所以查詢會傳回空的物件。

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

結果為：

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

DISTINCT 也可用在投影中的子查詢中使用：

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

此查詢會投射陣列，其中包含每個子系 givenName 移除重複項。 這個陣列的別名為 ChildNames，並在外部查詢中進行預計。

結果為：

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

## <a name="aliasing"></a>別名

可以显式为查询中的值指定别名。 如果查询包含两个同名的属性，请使用别名来重命名其中一个或两个属性，以便可以在投影的结果中消除其歧义。

如以下示例所示，将第二个值投影为 `NameInfo` 时，用于别名的 AS 关键字是可选的：

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果為：

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="FromClause"></a>FROM 子句

FROM (`FROM <from_specification>`) 子句是可选的，除非稍后在查询中对源进行筛选或投影。 有关语法的详细信息，请参阅 [FROM 语法](sql-api-query-reference.md#bk_from_clause)。 `SELECT * FROM Families` 之类的查询枚举整个 `Families` 容器。 还可以对容器使用特殊标识符 ROOT，而无需使用容器名称。

FROM 子句对每个查询强制实施以下规则：

* 您可以為容器設定別名，例如 `SELECT f.id FROM Families AS f` 或只是 `SELECT f.id FROM Families f`。 此处的 `f` 是 `Families` 的别名。 AS 是可选的关键字，用于指定标识符的别名。  

* 指定别名后，无法绑定原始的源名称。 例如，`SELECT Families.id FROM Families f` 在语法上是无效的，原因是标识符 `Families` 已指定别名，因此不再可以解析。  

* 所有被引用的属性必须完全限定，以避免在不严格遵守架构时出现任何有歧义的绑定。 例如，`SELECT id FROM Families f` 在语法上是无效的，因为未绑定属性 `id`。

### <a name="get-subitems-by-using-the-from-clause"></a>使用 FROM 子句获取子项

FROM 子句可将源化简为更小的子集。 要在每个项中仅枚举子树，子根可能会变成源，如以下示例所示：

```sql
    SELECT *
    FROM Families.children
```

結果為：

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

上述查询使用数组作为源，但你也可以使用对象作为源。 该查询考虑将源中任何有效的已定义 JSON 值包含在结果中。 下列範例會排除`Families`，沒有`address.state`值。

```sql
    SELECT *
    FROM Families.address.state
```

結果為：

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="WhereClause"></a>WHERE 子句

可选的 WHERE 子句 (`WHERE <filter_condition>`) 指定条件，查询只会将满足这些条件的源 JSON 项包含在结果中。 JSON 项必须将指定的条件评估为 `true` 才被视作结果。 索引层使用 WHERE 子句来确定可以作为结果的一部分的源项的最小子集。 有关语法的详细信息，请参阅 [WHERE 语法](sql-api-query-reference.md#bk_where_clause)。

以下查询请求包含值为 `AndersenFamily` 的 `id` 属性的项。 它会排除任何不带 `id` 属性或值与 `AndersenFamily` 不匹配的项。

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

### <a name="scalar-expressions-in-the-where-clause"></a>WHERE 子句中的标量表达式

前一個範例已顯示簡單的相等查詢。 SQL API 还支持各种[标量表达式](#scalar-expressions)。 最常用的是二元和一元運算式。 來源 JSON 物件中的屬性參考也是有效的運算式。

可以使用以下受支持的二元运算符：  

|**運算子類型**  | **值** |
|---------|---------|
|算術 | +、-、*、/、% |
|位元    | \|, &, ^, <<, >>, >>> (zero-fill right shift) |
|邏輯    | AND、OR、NOT      |
|比較 | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|字串     |  \|\| (串連) |

以下查询使用二元运算符：

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

还可以在查询中使用一元运算符 +、-、~ 和 NOT，如以下示例所示：

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

还可以在查询中使用属性引用。 例如，`SELECT * FROM Families f WHERE f.isRegistered` 返回包含值等于 `true` 的 `isRegistered` 属性的 JSON 项。 任何其他值（例如`false`、`null`、`Undefined`、`<number>`、`<string>`、`<object>` 或 `<array>`）会从结果中排除该项。 

### <a name="equality-and-comparison-operators"></a>相等和比較運算子

下表顯示 SQL API 中任何兩個 JSON 類型之間的相等比較結果。

| **Op** | **未定義** | **Null** | **布林值** | **Number** | **String** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **未定義** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **布林值** | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined |
| **Number** | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined |
| **String** | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined |
| **Object** | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined |
| **Array** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** |

对于 `>`、`>=`、`!=`、`<` 和 `<=` 等比较运算符，跨类型的比较或者两个对象或数组之间的比较会生成 `Undefined`。  

如果标量表达式的结果为 `Undefined`，则不会将该项包含在结果中，因为 `Undefined` 不等于 `true`。

### <a name="logical-and-or-and-not-operators"></a>邏輯 (AND、OR 和 NOT) 運算子

邏輯運算子的運算對象是布林值。 下表显示了这些运算符的逻辑真值表：

**OR 運算子**

| 或 | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Undefined |
| Undefined |True |Undefined |Undefined |

**AND 運算子**

| AND | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |False |Undefined |
| False |False |False |False |
| Undefined |Undefined |False |Undefined |

**NOT 運算子**

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Undefined |Undefined |

## <a name="between-keyword"></a>BETWEEN 關鍵字

与在 ANSI SQL 中一样，可以使用 BETWEEN 关键字来对字符串或数字值的范围表达查询。 例如，以下查询返回其中第一个孩子的年级为 1-5（含）的所有项。

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

与在 ANSI-SQL 中不同，你还可以在 FROM 子句中使用 BETWEEN 子句，如以下示例所示。

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

与 ANSI SQL 不同，在 SQL API 中，可以针对混合类型的属性表达范围查询。 例如，在某些项中，`grade` 可能是类似于 `5` 的数字；而在其他一些项中，它可能是类似于 `grade4` 的字符串。 在这些情况下（与在 JavaScript 中一样），两个不同类型之间的比较会生成 `Undefined`，因此会跳过该项。

> [!TIP]
> 为了更快地执行查询，请创建一个索引策略，该策略针对 BETWEEN 子句筛选的任何数字属性或路径使用范围索引类型。

## <a name="in-keyword"></a>IN 關鍵字

使用 IN 关键字可以检查指定的值是否与列表中的任一值匹配。 例如，以下查询返回 `id` 为 `WakefieldFamily` 或 `AndersenFamily` 的所有家庭项。

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

以下示例返回状态为任何指定值的所有项：

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="-operator"></a>* 运算符

特殊运算符 * 按原样投影整个项。 使用時，它必須是唯一投射的欄位。 类似于 `SELECT * FROM Families f` 的查询是有效的，而 `SELECT VALUE * FROM Families f` 和 `SELECT *, f.id FROM Families f` 是无效的。 [本文中的第一个查询](#query-the-json-items)使用了 * 运算符。 

## <a name="-and--operators"></a>? 和 ?? 运算符

如同在 C# 和 JavaScript 等编程语言中那样，可以使用三元 (?) 和联合 (??) 运算符来生成条件表达式。 

可以使用 ? 运算符即时构造新的 JSON 属性。 例如，以下查询将年级分类为 `elementary` 或 `other`：

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

还可以将调用嵌套到 ? 运算符，如以下查询中所示： 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

与其他查询运算符一样， 如果引用的属性缺失或者要比较的类型不同，则 ? 运算符将会排除项。

查询半结构化 或混合类型的数据时，可以使用 ?? 运算符有效地检查项中的属性。 例如，以下查询返回 `lastName`（如果存在）或 `surname`（如果 `lastName` 不存在）。

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="TopKeyword"></a>TOP 運算子

TOP 关键字以未定义的顺序返回前 `N` 个查询结果。 最佳做法是结合使用 TOP 与 ORDER BY 子句，将结果限制为前 `N` 个有序值。 要预见性地指示哪些行受到 TOP 的影响，只能结合使用这两个子句。

可以结合一个常量值使用 TOP（如以下示例中所示），或者在参数化查询中结合一个变量值使用 TOP。 有关详细信息，请参阅[参数化查询](#parameterized-queries)部分。

```sql
    SELECT TOP 1 *
    FROM Families f
```

結果為：

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a id="OrderByClause"></a>ORDER BY 子句

与在 ANSI SQL 中一样，可以在查询中包含可选的 ORDER BY 子句。 可选的 ASC 或 DESC 参数指定是要按升序还是降序检索结果。 默认值为 ASC。

例如，以下查询按居住城市名称的升序检索家庭：

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

以下查询按项的创建日期检索家庭 `id`。 项 `creationDate` 是一个数字，表示纪元时间，或者自 1970 年 1 月 1 日开始消逝的时间（以秒为单位）。

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

## <a id="OffsetLimitClause"></a>位移 LIMIT 子句

位移的限制是選擇性的子句來略過，則採用多個查詢的值。 位移的計數和限制計數所需位移限制子句中。

ORDER BY 子句搭配使用位移的限制時，結果集藉由略過產生，並對已排序的值。 如果沒有 ORDER BY 子句使用時，它會導致的值決定的順序。

例如，以下是會略過第一個值，並傳回第二個值 （以依據居住城市名稱的順序） 的查詢：

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

結果為：

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

以下是查詢，會略過第一個值，並傳回第二個值 （而不會排序）：

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

結果為：

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```




## <a name="scalar-expressions"></a>純量運算式

SELECT 子句支持标量表达式，例如常量、算术表达式和逻辑表达式。 以下查询使用一个标量表达式：


```sql
    SELECT ((2 + 11 % 7)-2)/3
```

結果為：

```json
    [{
      "$1": 1.33333
    }]
```

在以下查询中，标量表达式的结果是一个布尔值：


```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

結果為：

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="object-and-array-creation"></a>物件和陣列建立

SQL API 的一个重要功能是创建数组和对象。 以上示例创建了新的 JSON 对象 `AreFromSameCityState`。 还可以构造数组，如以下示例所示：


```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

結果為：

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```


## <a id="Iteration"></a>反覆運算

SQL API 支持循环访问 JSON 数组，它可以通过 FROM 源中的 IN 关键字添加一个新的构造。 在以下示例中：

```sql
    SELECT *
    FROM Families.children
```

結果為：

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

下一个查询循环访问 `Families` 容器中的 `children`。 输出的数组与前面的查询不同。 此示例拆分 `children` 并将结果平展为单个数组：  

```sql
    SELECT *
    FROM c IN Families.children
```

結果為：

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

可以进一步筛选该数组的每个条目，如以下示例所示：

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

結果為：

```json
    [{
      "givenName": "Lisa"
    }]
```

还可基于数组迭代的结果进行聚合。 例如，以下查询计数所有家庭中的孩子数目。

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

結果為：

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a id="Joins"></a>聯結

在关系数据库中，跨表联接是设计规范化架构的逻辑定理。 相比之下，SQL API 使用无架构项的反规范化数据模型，这在逻辑上等效于自联接。

该语言支持语法 `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`。 此查询返回一组包含 `N` 值的元组。 每個 Tuple 所擁有的值，都是將所有容器別名在其個別集合上反覆運算所產生的。 換句話說，此查詢會對參與聯結的集合執行完整的交叉乘積。

下列範例示範 JOIN 子句的運作方式。 在以下示例中，由于源中每个项和空集的叉积为空，因此结果为空：

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

結果如下：

```json
    [{
    }]
```

在以下示例中，联接是两个 JSON 对象、项根 `id` 和子根 `children` 之间的叉积。 `children` 是数组这一事实在联接中不起作用，因为查询处理的是作为 `children` 数组的单一根。 由于每个带有数组的项的叉积仅生成一个项，因此结果仅包含两个结果。

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

結果為：

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

下列範例示範較傳統的聯結：

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

結果為：

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

JOIN 子句的 FROM 源是一个迭代器。 因此，以上示例中的流程为：  

1. 展开数组中的每个子元素 `c`。
2. 应用包含项 `f` 的根的叉积，该项包含已在第一个步骤中平展的每个子元素 `c`。
3. 最后，单独投影根对象 `f` `id` 属性。

第一个项 (`AndersenFamily`) 仅包含一个 `children` 元素，因此结果集仅包含单个对象。 第二个项 `WakefieldFamily` 包含两个 `children`，因此，叉积为每个 `children` 元素生成一个对象，共两个对象。 這兩個項目中的根欄位相同，就像您在交叉乘積中預期地一樣。

JOIN 子句真正实用的地方是通过以其他方式难以投影的形式基于叉积生成元组。 以下示例对元组组合进行筛选，让用户选择元组在整体上满足的条件。

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

結果為：

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

以下示例对前一个示例做了延伸，将会执行双重联接。 可将叉积视为下面所示的伪代码：

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` 中有一个孩子拥有一只宠物，因此叉积从此家庭生成了一行 (1\*1\*1)。 `WakefieldFamily` 中有两个孩子，其中只有一个孩子拥有宠物，但这个孩子拥有两只宠物。 叉积对此家庭生成了 1\*1\*2 = 2 行。

以下示例根据 `pet` 进行了额外的筛选，这排除了宠物名称不是 `Shadow` 的所有元组。 可以基于数组生成元组，根据元组的任意元素进行筛选以及投影元素的任何组合。

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

結果為：

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a id="UserDefinedFunctions"></a>使用者定義函數 (Udf)

SQL API 支持用户定义函数 (UDF)。 使用标量 UDF，可以传入零个或多个参数，并返回单个参数结果。 API 会检查每个参数 JSON 值是否合法。  

API 扩展了 SQL 语法，支持使用 UDF 的自定义应用程序逻辑。 可将 UDF 注册到 SQL API，然后在 SQL 查询中引用它们。 實際上，UDF 是特別設計來透過查詢進行呼叫的。 作为一种定理，UDF 不能像其他 JavaScript 类型（例如存储过程和触发器）一样访问上下文对象。 查询是只读的，可以在主要或次要副本上运行。 与其他 JavaScript 类型不同，UDF 只能在次要副本上运行。

以下示例在 Cosmos DB 数据库中的某个项容器下注册一个 UDF。 该示例创建了名为 `REGEX_MATCH` 的 UDF。 它接受两个 JSON 字符串值：`input` 和 `pattern`，并使用 JavaScript 的 `string.match()` 函数检查第一个值是否与第二个值中指定的模式相匹配。

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

现在，请在查询投影中使用此 UDF。 从查询内部调用 UDF 时，必须使用区分大小写的前缀 `udf.` 来限定 UDF。

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

結果為：

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

可以在筛选器中使用以 `udf.` 前缀限定的 UDF，如以下示例所示：

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

結果為：

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

从本质上来说，UDF 是可以在投影和筛选器中使用的有效标量表达式。

为了进一步了解 UDF 的强大功能，让我们查看使用条件逻辑的另一个示例：

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

以下示例运用了 UDF：

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

結果為：

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

如果 UDF 参数引用的属性在 JSON 值中未提供，则会将该参数视为未定义，因此会跳过 UDF 调用。 同样，如果未定义 UDF 的结果，则不会将此 UDF 包含在结果中。

如以上示例所示，UDF 将 JavaScript 语言的强大功能与 SQL API 相集成。 UDF 提供丰富的可编程接口来执行复杂的过程，并借助内置的 JavaScript 运行时功能来执行条件逻辑。 SQL API 在当前的 WHERE 或 SELECT 子句处理阶段，为每个源项的 UDF 提供参数。 结果将无缝整合到总体执行管道中。 总而言之，UDF 是在查询过程中执行复杂业务逻辑的极佳工具。

## <a id="Aggregates"></a>彙總函式

聚合函数对 SELECT 子句中的一组值执行计算，并返回单个值。 例如，以下查询返回 `Families` 容器中的项计数。

```sql
    SELECT COUNT(1)
    FROM Families f
```

結果為：

```json
    [{
        "$1": 2
    }]
```

也可以使用 VALUE 关键字来仅返回聚合的标量值。 例如，下列查詢會以單一數字傳回值的計數：

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

結果為：

```json
    [ 2 ]
```

还可以将聚合与筛选器结合使用。 例如，以下查询返回包含 `WA` 州地址的项计数。

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

結果為：

```json
    [ 1 ]
```

SQL API 支持以下聚合函数。 SUM 和 AVG 针对数字值执行运算，COUNT、MIN 和 MAX 可以处理数字、字符串、布尔值和 null。

| 函式 | 描述 |
|-------|-------------|
| COUNT | 以運算式傳回項目的數目。 |
| SUM   | 以運算式傳回所有值的總和。 |
| 最小值   | 以運算式傳回最小值。 |
| MAX   | 以運算式傳回最大值。 |
| 平均值   | 以運算式傳回值的平均。 |

还可以基于数组迭代的结果进行聚合。 有关详细信息，请参阅[迭代](#Iteration)部分。

> [!NOTE]
> 在 Azure 门户的数据资源管理器中，聚合查询可以仅基于一个查询页面聚合部分结果。 SDK 跨所有页面生成单个累计值。 若要使用代码执行聚合查询，需要 .NET SDK 1.12.0、.NET Core SDK 1.1.0，或者 Java SDK 1.9.5 或更高版本。
>

## <a id="BuiltinFunctions"></a>內建函數

Cosmos DB 还支持使用许多内置函数进行常见操作，这些函数可以在查询（如用户定义的函数 (UDF)）中使用。

| 函式群組 | 作業 |
|---------|----------|
| 數學函數 | ABS、CEILING、EXP、FLOOR、LOG、LOG10、POWER、ROUND、SIGN、SQRT、SQUARE、TRUNC、ACOS、ASIN、ATAN、ATN2、COS、COT、DEGREES、PI、RADIANS、SIN、TAN |
| 类型检查函数 | IS_ARRAY、IS_BOOL、IS_NULL、IS_NUMBER、IS_OBJECT、IS_STRING、IS_DEFINED、IS_PRIMITIVE |
| 字串函數 | CONCAT、CONTAINS、ENDSWITH、INDEX_OF、LEFT、LENGTH、LOWER、LTRIM、REPLACE、REPLICATE、REVERSE、RIGHT、RTRIM、STARTSWITH、SUBSTRING、UPPER |
| 陣列函數 | ARRAY_CONCAT、ARRAY_CONTAINS、ARRAY_LENGTH 和 ARRAY_SLICE |
| 空間函數 | ST_DISTANCE、ST_WITHIN、ST_INTERSECTS、ST_ISVALID、ST_ISVALIDDETAILED |

如果您目前使用的使用者定義的函數 (UDF) 已提供內建函式，相對應的內建函式會加快執行速度且更有效率。

Cosmos DB 函数与 ANSI SQL 函数之间的主要差别在于，Cosmos DB 函数能够很好地处理无架构数据和混合架构数据。 例如，如果某个属性缺失或包含类似于 `unknown` 的非数字值，则会跳过该项，而不是返回错误。

### <a name="mathematical-functions"></a>數學函數

每個數學函式都會執行計算，通常以提供來作為引數的輸入值為基礎，並傳回數值。 以下是支援的內建數學函數資料表。

| 使用量 | 描述 |
|----------|--------|
| ABS (num_expr) | 傳回指定之數值運算式的絕對 (正) 值。 |
| CEILING (num_expr) | 傳回大於或等於指定之數值運算式的最小整數值。 |
| FLOOR (num_expr) | 傳回小於或等於指定之數值運算式的最大整數。 |
| EXP (num_expr) | 傳回指定之數值運算式的指數。 |
| LOG (num_expr, base) | 返回指定数值表达式的自然对数，或使用指定底数的对数。 |
| LOG10 (num_expr) | 傳回指定之數值運算式的以 10 為基底的對數值。 |
| ROUND (num_expr) | 傳回數值，四捨五入到最接近的整數值。 |
| TRUNC (num_expr) | 傳回數值，截斷至最接近的整數值。 |
| SQRT (num_expr) | 傳回指定之數值運算式的平方根。 |
| SQUARE (num_expr) | 傳回指定之數值運算式的平方。 |
| POWER (num_expr, num_expr) | 將指定數值運算式的乘冪傳回給指定的值。 |
| SIGN (num_expr) | 傳回指定之數值運算式的正負號值 (-1、0、1)。 |
| ACOS (num_expr) | 傳回角度，以弧度為單位，它的餘弦是指定的數值運算式；也稱為反餘弦值。 |
| ASIN (num_expr) | 傳回角度，以弧度為單位，其正弦函數是指定的數值運算式。 此函數也稱為反正弦值。 |
| ATAN (num_expr) | 傳回角度，以弧度為單位，其正切函數是指定的數值運算式。 此函数也称为反正切。 |
| ATN2 (num_expr) | 傳回角度，以弧度為單位，正 x 軸和從原點 (y、x) 點的切線之間，其中 x 和 y 是兩個指定之浮點運算式的值。 |
| COS (num_expr) | 在指定運算式中傳回指定角度的三角餘弦函數，以弧度為單位。 |
| COT (num_expr) | 在指定的數值運算式中傳回指定角度的三角餘切函數，以弧度為單位。 |
| DEGREES (num_expr) | 針對以弧度指定的角度，傳回對應的角度 (以度為單位)。 |
| PI () | 傳回 PI 的常數值。 |
| RADIANS (num_expr) | 當您輸入數值運算式 (以度為單位) 時，傳回弧度。 |
| SIN (num_expr) | 在指定運算式中傳回指定角度的三角正弦函數 (以弧度為單位)。 |
| TAN (num_expr) | 在指定運算式中傳回輸入運算式的正切函數。 |

可以运行以下示例所示的查询：

```sql
    SELECT VALUE ABS(-4)
```

結果如下：

```json
    [4]
```

### <a name="type-checking-functions"></a>类型检查函数

使用类型检查函数可以检查 SQL 查询中表达式的类型。 当项中的属性可变或未知时，可以使用类型检查函数即时确定这些属性的类型。 以下是支援的內建類型檢查函數的資料表：

| **使用量** | **說明** |
|-----------|------------|
| [IS_ARRAY (expr)](sql-api-query-reference.md#bk_is_array) | 傳回布林值，表示值的類型是否為陣列。 |
| [IS_BOOL (expr)](sql-api-query-reference.md#bk_is_bool) | 返回一个布尔值，它指示值的类型是否为布尔。 |
| [IS_NULL (expr)](sql-api-query-reference.md#bk_is_null) | 返回一个布尔值，它指示值的类型是否为 null。 |
| [IS_NUMBER (expr)](sql-api-query-reference.md#bk_is_number) | 返回一个布尔值，它指示值的类型是否为数字。 |
| [IS_OBJECT (expr)](sql-api-query-reference.md#bk_is_object) | 返回一个布尔值，它指示值的类型是否为 JSON 对象。 |
| [IS_STRING (expr)](sql-api-query-reference.md#bk_is_string) | 返回一个布尔值，它指示值的类型是否为字符串。 |
| [IS_DEFINED (expr)](sql-api-query-reference.md#bk_is_defined) | 返回一个布尔，它指示属性是否已经分配了值。 |
| [IS_PRIMITIVE (expr)](sql-api-query-reference.md#bk_is_primitive) | 返回一个布尔值，它指示值的类型是字符串、数字、布尔值还是 null。 |

使用这些函数可以运行以下示例所示的查询：

```sql
    SELECT VALUE IS_NUMBER(-4)
```

結果如下：

```json
    [true]
```

### <a name="string-functions"></a>字串函數

以下标量函数对字符串输入值执行操作，并返回字符串、数字或布尔值。 以下是內建字串函數的資料表：

| 使用量 | 描述 |
| --- | --- |
| [LENGTH (str_expr)](sql-api-query-reference.md#bk_length) | 傳回指定字串運算式的字元數目。 |
| [CONCAT (str_expr, str_expr [, str_expr])](sql-api-query-reference.md#bk_concat) | 傳回字串，該字串是串連兩個或多個字串值的結果。 |
| [SUBSTRING (str_expr, num_expr, num_expr)](sql-api-query-reference.md#bk_substring) | 返回部分字符串表达式。 |
| [STARTSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_startswith) | 傳回布林值，表示第一個字串運算式是否以第二個字串運算式開頭。 |
| [ENDSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_endswith) | 傳回布林值，表示第一個字串運算式是否以第二個字串運算式結尾。 |
| [CONTAINS (str_expr, str_expr)](sql-api-query-reference.md#bk_contains) | 傳回布林值，表示第一個字串運算式是否包含第二個字串運算式。 |
| [INDEX_OF (str_expr, str_expr)](sql-api-query-reference.md#bk_index_of) | 返回第一个指定的字符串表达式中第一次出现第二个字符串表达式的起始位置，如果未找到字符串，则返回 -1。 |
| [LEFT (str_expr, num_expr)](sql-api-query-reference.md#bk_left) | 返回具有指定字符数的字符串的左侧部分。 |
| [RIGHT (str_expr, num_expr)](sql-api-query-reference.md#bk_right) | 返回具有指定字符数的字符串的右侧部分。 |
| [LTRIM (str_expr)](sql-api-query-reference.md#bk_ltrim) | 傳回移除開頭空白之後的字串運算式。 |
| [RTRIM (str_expr)](sql-api-query-reference.md#bk_rtrim) | 傳回截斷所有結尾空白之後的字串運算式。 |
| [LOWER (str_expr)](sql-api-query-reference.md#bk_lower) | 傳回將大寫字元資料轉換成小寫之後的字串運算式。 |
| [UPPER (str_expr)](sql-api-query-reference.md#bk_upper) | 傳回將小寫字元資料轉換成大寫之後的字串運算式。 |
| [REPLACE (str_expr, str_expr, str_expr)](sql-api-query-reference.md#bk_replace) | 将出现的所有指定字符串值替换为另一个字符串值。 |
| [REPLICATE (str_expr, num_expr)](sql-api-query-reference.md#bk_replicate) | 將字串值重複指定的次數。 |
| [REVERSE (str_expr)](sql-api-query-reference.md#bk_reverse) | 傳回反向順序的字串值。 |

使用这些函数可以运行如下以下所示的查询，该查询返回大写形式的家庭 `id`：

```sql
    SELECT VALUE UPPER(Families.id)
    FROM Families
```

結果為：

```json
    [
        "WAKEFIELDFAMILY",
        "ANDERSENFAMILY"
    ]
```

或者，如以下示例所示连接字符串：

```sql
    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families
```

結果為：

```json
    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "Seattle,WA"
    }]
```

还可以在 WHERE 子句中使用字符串函数来筛选结果，如以下示例所示：

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")
```

結果為：

```json
    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]
```

### <a name="array-functions"></a>陣列函數

以下标量函数对数组输入值执行操作，并返回数字值、布尔值或数组值。 以下是內建陣列函數的資料表：

| 使用量 | 描述 |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](sql-api-query-reference.md#bk_array_length) |傳回指定陣列運算式的元素數目。 |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](sql-api-query-reference.md#bk_array_concat) |傳回串連兩個或多個陣列值之結果的陣列。 |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](sql-api-query-reference.md#bk_array_contains) |傳回布林值，表示陣列是否包含指定值。 可以指定要進行完整或部分比對。 |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](sql-api-query-reference.md#bk_array_slice) |傳回陣列運算式的一部分。 |

使用数组函数可以处理 JSON 中的数组。 例如，以下查询返回其中一个 `parents` 是 `Robin Wakefield` 的所有项 `id`： 

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })
```

結果如下：

```json
    [{
      "id": "WakefieldFamily"
    }]
```

您可以指定部分片段來比對陣列內的元素。 以下查询查找包含 `givenName` 为 `Robin` 的 `parents` 的所有项 `id`：

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)
```

結果如下：

```json
    [{
      "id": "WakefieldFamily"
    }]
```

下面是使用 ARRAY_LENGTH 获取每个家庭的 `children` 数目的另一个示例：

```sql
    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 
```

結果為：

```json
    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]
```

### <a name="spatial-functions"></a>空間函數

Cosmos DB 支持以下用于查询地理空间的开放地理空间信息联盟 (OGC) 内置函数： 

| 使用量 | 描述 |
| --- | --- |
| ST_DISTANCE (point_expr、point_expr) | 返回两个 GeoJSON `Point`、`Polygon` 或 `LineString` 表达式之间的距离。 |
| T_WITHIN (point_expr, polygon_expr) | 返回一个布尔表达式，指示第一个 GeoJSON 对象（`Point`、`Polygon` 或 `LineString`）是否在第二个 GeoJSON 对象（`Point`、`Polygon` 或 `LineString`）的内部。 |
| ST_INTERSECTS (spatial_expr, spatial_expr) | 返回一个布尔表达式，指示两个指定的 GeoJSON 对象（`Point`、`Polygon` 或 `LineString`）是否相交。 |
| ST_ISVALID | 返回一个布尔值，指示指定的 GeoJSON `Point`、`Polygon` 或 `LineString` 表达式是否有效。 |
| ST_ISVALIDDETAILED | 如果指定的 GeoJSON `Point` 或、`Polygon` 或 `LineString` 表达式有效，则返回包含布尔值的 JSON 值；如果无效，则返回字符串值形式的原因。 |

可以使用空间函数对空间数据执行邻近查询。 例如，以下查询使用 ST_DISTANCE 内置函数返回所有家庭项，且这些文档在指定位置的 30 公里内：

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

結果如下：

```json
    [{
      "id": "WakefieldFamily"
    }]
```

如需有關 Cosmos DB 中的地理空間支援詳細資訊，請參閱[使用 Azure Cosmos DB 中的地理空間資料](geospatial.md)。 

## <a name="parameterized-queries"></a>参数化查询

Cosmos DB 支持使用带有常用 @ 表示法的参数进行查询。 参数化 SQL 为用户输入提供可靠的处理和转义，可防止通过 SQL 注入发生意外的数据泄露。

例如，可以编写一个将 `lastName` 和 `address.state` 用作参数的查询，并根据用户输入针对 `lastName` 和 `address.state` 的各种值执行此查询。

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

然后，可将此请求作为参数化 JSON 查询发送到 Cosmos DB，如下所示：

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

以下示例使用参数化查询设置 TOP 参数： 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

参数值可以是任何有效的 JSON：字符串、数字、布尔值、null，甚至数组或嵌套的 JSON。 由于 Cosmos DB 是无架构的，因此不会针对任何类型验证参数。

## <a id="JavaScriptIntegration"></a>JavaScript 整合

Azure Cosmos DB 提供一种编程模型，用于通过存储过程和触发器对容器直接执行基于 JavaScript 的应用程序逻辑。 此模型支持：

* 通过在数据库引擎中深度集成 JavaScript 运行时，对容器中的项执行高性能事务性 CRUD 操作和查询。
* 控制流、变量范围和分配的自然建模和将异常处理基元与数据库事务集成。 

有关 Azure Cosmos DB JavaScript 集成的详细信息，请参阅 [JavaScript 服务器端 API](#JavaScriptServerSideApi) 部分。

### <a name="operator-evaluation"></a>運算子評估

Cosmos DB 是一个 JSON 数据库，在 JavaScript 运算符和评估语义方面具有许多相似之处。 就 JSON 支持而言，Cosmos DB 会尽量保留 JavaScript 语义，但运算求值在某些情况下会有所不同。

与传统 SQL 不同，在 SQL API 中，在该 API 从数据库检索出值之前，值的类型往往是未知的。 為了有效率地執行查詢，大部分的運算子都有嚴謹的類型需求。

与 JavaScript 不同，SQL API 不会执行隐式转换。 例如，类似于 `SELECT * FROM Person p WHERE p.Age = 21` 的查询会匹配包含 `21` 值的 `Age` 属性的项。 它不会匹配其 `Age` 属性可能与 `twenty-one`、`021` 或 `21.0` 等无限变体匹配的任何其他项。 这与 JavaScript 相反，在 JavaScript 中，字符串值会根据 `==` 等运算符隐式转换为数字。 此 SQL API 行为对于有效的索引匹配至关重要。

## <a id="ExecutingSqlQueries"></a>執行 SQL 查詢

能够发出 HTTP/HTTPS 请求的任何语言都可以调用 Cosmos DB REST API。 Cosmos DB 还为 .NET、Node.js、JavaScript 和 Python 编程语言提供编程库。 REST API 和库全部支持通过 SQL 执行的查询，.NET SDK 还支持 [LINQ 查询](#Linq)。

下列範例顯示如何建立查詢，並針對 Cosmos DB 資料庫帳戶提交它。

### <a id="RestAPI"></a>REST API

Cosmos DB 提供透過 HTTP 的開放 RESTful 程式設計模型。 资源模型由 Azure 订阅预配的数据库帐户下的一组资源组成。 该数据库帐户由一组数据库组成，其中的每个数据库可以包含多个容器，而每个容器又可以包含项、UDF 和其他资源类型。 可以使用稳定的逻辑 URI 对每个 Cosmos DB 资源进行寻址。 一组资源称作一个源。 

这些资源的基本交互模型是通过 HTTP 谓词 `GET`、`PUT`、`POST` 和 `DELETE` 及其标准解释实现的。 使用 `POST` 可以创建新的资源、执行存储过程或发出 Cosmos DB 查询。 查詢一律是唯讀作業，而且沒有任何副作用。

以下示例演示了如何根据示例项针对 SQL API 查询发出 `POST`。 该查询对 JSON `name` 属性应用一个简单的筛选器。 `x-ms-documentdb-isquery` 和 Content-Type: `application/query+json` 标头表示该操作是一个查询。 请将 `mysqlapicosmosdb.documents.azure.com:443` 替换为 Cosmos DB 帐户的 URI。

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

結果為：

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

下面这个更复杂的查询从某个联接操作返回多个结果：

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": [] 
    }
```

結果為： 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

如果查询的结果无法包含在一页中，则 REST API 会通过 `x-ms-continuation-token` 响应标头返回继续标记。 客户端可以通过在后续结果中包含该标头对结果进行分页。 也可以通过 `x-ms-max-item-count` 数字标头控制每页的结果数。 

如果查询包含 COUNT 等聚合函数，则查询页可以仅通过一个结果页返回部分聚合的值。 客户端必须对这些结果执行二级聚合才能生成最终结果。 例如，将各个页中返回的计数求和可以返回总计数。

若要管理查询的数据一致性策略，请使用 `x-ms-consistency-level` 标头（如所有的 REST API 请求）。 对于会话一致性，还需要回显查询请求中最新的 `x-ms-session-token` Cookie 标头。 所查詢容器的編製索引原則也會影響查詢結果的一致性。 使用默认的索引策略设置，容器的索引始终与项内容保持同步，且查询结果将与为数据选择的一致性匹配。 有关详细信息，请参阅 [Azure Cosmos DB 一致性级别][consistency-levels]。

如果容器上配置的索引策略不能支持指定的查询，Azure Cosmos DB 服务器会返回 400“错误的请求”。 使用从索引中显式排除的路径执行查询时，将返回此错误消息。 可以指定 `x-ms-documentdb-query-enable-scan` 标头，以便在索引不可用时允许查询执行扫描。

可以通过将 `x-ms-documentdb-populatequerymetrics` 标头设置为 `true` 来获取有关查询执行的详细指标。 如需詳細資訊，請參閱[適用於 Azure Cosmos DB 的 SQL 查詢計量](sql-api-query-metrics.md)。

### <a id="DotNetSdk"></a>C# (.NET SDK)

.NET SDK 支援 LINQ 和 SQL 查詢。 以下示例演示如何使用 .NET 执行前面所示的筛选查询：

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

以下示例比较了每个项内等式的两个属性，并使用匿名投影。

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

以下示例演示通过 LINQ `SelectMany` 表达的联接。

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

.NET 客户端自动循环访问 `foreach` 块中所有的查询结果页，如前面的示例中所示。 [REST API](#RestAPI) 部分介绍的查询选项也适用于在 `CreateDocumentQuery` 方法中使用 `FeedOptions` 和 `FeedResponse` 类的 .NET SDK。 可以使用 `MaxItemCount` 设置控制页数。

您可以建立，以明確地控制分頁`IDocumentQueryable`使用`IQueryable`物件，然後藉由讀取`ResponseContinuationToken`做為傳回的值，並將它們`RequestContinuationToken`在`FeedOptions`。 可以设置 `EnableScanInQuery`，以便在配置的索引策略不支持该查询时启用扫描。 对于分区容器，可以使用 `PartitionKey` 针对单个分区运行查询，不过，Azure Cosmos DB 可以自动从查询文本中提取此信息。 可以使用 `EnableCrossPartitionQuery` 针对多个分区运行查询。

有关更多包含查询的 .NET 示例，请参阅 GitHub 中的 [Azure Cosmos DB .NET 示例](https://github.com/Azure/azure-cosmosdb-dotnet)。

### <a id="JavaScriptServerSideApi"></a>JavaScript 伺服器端 API

Cosmos DB 提供一种编程模型，用于通过存储过程和触发器对容器直接执行基于 JavaScript 的应用程序逻辑。 然后，在容器级别注册的 JavaScript 逻辑可以针对给定容器中的、包装在环境 ACID 事务中的项发出数据库操作。

以下示例演示了如何在 JavaScript 服务器 API 中使用 `queryDocuments` 从存储过程和触发器内部进行查询。

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a id="Linq"></a>LINQ to SQL API

LINQ 是一个 .NET 编程模型，它将计算表达为对对象流的查询。 Cosmos DB 提供一個用戶端程式庫，可透過協助 JSON 與 .NET 物件之間的轉換，以及 LINQ 查詢子集與 Cosmos DB 查詢的對應，來與 LINQ 互動。

下图演示了使用 Cosmos DB 的支持性 LINQ 查询的体系结构。 使用 Cosmos DB 客户端，可以创建直接查询 Cosmos DB 查询提供程序的 `IQueryable` 对象，并将 LINQ 查询转换为 Cosmos DB 查询。 然后，将该查询传递到 Cosmos DB 服务器，后者会检索一组 JSON 格式的结果。 在客户端中，JSON 反序列化程序会将结果转换为 .NET 对象流。

![使用 SQL API 來支援 LINQ 查詢的架構：SQL 語法、JSON 查詢語言、資料庫概念及 SQL 查詢][1]

### <a name="net-and-json-mapping"></a>.NET 和 JSON 對應

.NET 对象与 JSON 项之间的映射是自然的。 每个数据成员字段映射到 JSON 对象，其中的字段名称映射到对象的 *key* 部分，值以递归方式映射到该对象的 *value* 部分。 以下代码将 `Family` 类映射到 JSON 项，然后创建 `Family` 对象：

```csharp
    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };
```

以上示例将创建以下 JSON 项：

```json
    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                "givenName": "Jesse",
                "gender": "female",
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller",
              "givenName": "Lisa",
              "gender": "female",
              "grade": 8
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };
```

### <a name="linq-to-sql-translation"></a>LINQ 至 SQL 轉譯

Cosmos DB 查詢提供者會盡力將 LINQ 查詢對應至 Cosmos DB SQL 查詢。 以下内容假设你对 LINQ 有一个基本的了解。

查询提供程序类型系统仅支持 JSON 基元类型：数字、布尔值、字符串和 null。 

查询提供程序支持以下标量表达式：

- 常量值，包括评估查询时基元数据类型的常量值。
  
- 引用对象或数组元素的属性的属性/数组索引表达式。 例如︰
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- 算术表达式，包括针对数值和布尔值运行的常见算术表达式。 有关完整列表，请参阅 [Azure Cosmos DB SQL 规范](https://go.microsoft.com/fwlink/p/?LinkID=510612)。
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- 字符串比较表达式，包括将字符串值与某些常量字符串值进行比较。  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- 对象/数组创建表达式，返回复合值类型或匿名类型的对象，或此类对象组成的数组。 可以嵌套这些值。
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

### <a id="SupportedLinqOperators"></a>支援的 LINQ 運算子

SQL .NET SDK 随附的 LINQ 提供程序支持以下运算符：

- **Select**：投影转换为 SQL SELECT（包括对象构造）。
- **Where**：筛选器转换为 SQL WHERE，支持 `&&`、`||` 和 `!` 到 SQL 运算符的转换
- **SelectMany**：可讓陣列回溯到 SQL JOIN 子句。 用于将表达式链接或嵌套到对数组元素应用的筛选器。
- **OrderBy** 和 **OrderByDescending**：使用 ASC 或 DESC 转换为 ORDER BY。
- 彙總的 **Count**、**Sum**、**Min**、**Max** 與 **Average** 運算子，以及其非同步對應項 **CountAsync**、**SumAsync**、**MinAsync**、**MaxAsync** 與 **AverageAsync**。
- **CompareTo**：轉譯為範圍比較。 常用的字串，因為它們不在.NET 中比較。
- **Take**：转换为 SQL TOP，用于限制查询中的结果。
- **数学函数**：支持从 .NET `Abs`、`Acos`、`Asin`、`Atan`、`Ceiling`、`Cos`、`Exp`、`Floor`、`Log`、`Log10`、`Pow`、`Round`、`Sign`、`Sin`、`Sqrt`、`Tan` 和 `Truncate` 转换为等效的 SQL 内置函数。
- **字符串函数**：支持从.NET  `Concat`、`Contains`、`Count`、`EndsWith`、`IndexOf`、`Replace`、`Reverse`、`StartsWith`、`SubString`、`ToLower`、`ToUpper`、`TrimEnd` 和 `TrimStart` 转换为等效的 SQL 内置函数。
- **数组函数**：支持从 .NET `Concat`、`Contains` 和 `Count` 转换为等效的 SQL 内置函数。
- **地理空间扩展函数**：支持从存根方法 `Distance`、`IsValid`、`IsValidDetailed` 和 `Within` 转换为等效的 SQL 内置函数。
- **用户定义的函数扩展函数**：支持从存根方法 `UserDefinedFunctionProvider.Invoke` 转换为相应的用户定义的函数。
- **其他**：支持 `Coalesce` 和条件运算符的转换。 可以根据上下文将 `Contains` 转换为字符串 CONTAINS、ARRAY_CONTAINS 或 SQL IN。

### <a name="sql-query-operators"></a>SQL 查詢運算子

以下示例演示了一些标准 LINQ 查询运算符如何转换为 Cosmos DB 查询。

#### <a name="select-operator"></a>Select 运算符

語法為 `input.Select(x => f(x))`，其中 `f` 是純量運算式。

**Select 运算符，示例 1：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Select 运算符，示例 2：** 

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Select 运算符，示例 3：**

- **LINQ Lambda 運算式**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

#### <a name="selectmany-operator"></a>SelectMany 運算子

語法為 `input.SelectMany(x => f(x))`，其中 `f` 是傳回容器類型的純量運算式。

- **LINQ Lambda 運算式**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

#### <a name="where-operator"></a>Where 運算子

語法為 `input.Where(x => f(x))`，其中 `f` 是傳回布林值的純量運算式。

**Where 运算符，示例 1：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Where 运算符，示例 2：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

### <a name="composite-sql-queries"></a>複合 SQL 查詢

将上述运算符组合到一起可以构成更强大的查询。 由于 Cosmos DB 支持嵌套的容器，因此你可以连接或嵌套这种组合。

#### <a name="concatenation"></a>串連

語法為 `input(.|.SelectMany())(.Select()|.Where())*`。 连接的查询可以使用可选的 `SelectMany` 查询开头，后接多个 `Select` 或 `Where` 运算符。

**连接，示例 1：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**连接，示例 2：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**连接，示例 3：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**连接，示例 4：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

#### <a name="nesting"></a>巢狀

语法为 `input.SelectMany(x=>x.Q())`，其中 `Q` 是 `Select`、`SelectMany` 或 `Where` 运算符。

嵌套查询会将内部查询应用到外部容器的每个元素。 一个重要的功能是内部查询可以引用外部容器（如自联接）中元素的字段。

**嵌套，示例 1：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**嵌套，示例 2：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**嵌套，示例 3：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```

## <a id="References"></a>參考

- [Azure Cosmos DB SQL 規格](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [Javascript 规范](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 
- Graefe, Goetz。 [Query evaluation techniques for large databases](https://dl.acm.org/citation.cfm?id=152611)（适用于大型数据库的查询评估技术） 《ACM 计算调查》第 25 期 第 2 卷 (1993)。
- Graefe, G。“用于优化查询的 Cascades 框架”。 《IEEE 数据工程期刊》 第 18 期 第 3 卷 (1995)。
- Lu、Ooi、Tan。 “并行关系数据库系统中的查询处理”。 《IEEE 计算机协会期刊》(1994)。
- Olston、Christopher、Benjamin Reed、Utkarsh Srivastava、Ravi Kumar 和 Andrew Tomkins。 “Pig Latin：并不是很难懂的数据处理语言”。 *SIGMOD* (2008)。

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 簡介][introduction]
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Azure Cosmos DB 一致性级别][consistency-levels]

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
