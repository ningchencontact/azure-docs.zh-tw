---
title: 適用於 Azure Cosmos DB 的 SQL 查詢
description: 瞭解 Azure Cosmos DB 的 SQL 語法、資料庫概念及 SQL 查詢。 將 SQL 用作 Azure Cosmos DB JSON 查詢語言。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: a5cc6bfca67f3d90467fa2339bc991c1f0bbeadf
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148941"
---
# <a name="sql-query-examples-for-azure-cosmos-db"></a>Azure Cosmos DB 的 SQL 查詢範例

Azure Cosmos DB SQL API 帳戶支持使用 結構化查詢語言 (SQL) 作為 JSON 查詢語言來查詢項。 Azure Cosmos DB 查詢語言的設計目標是：

* 支持用戶最熟悉的、最流行的 SQL 查詢語言，而不是要發明一種新的查詢語言。 SQL 提供正式的編程模型用於對 JSON 項進行豐富查詢。  

* 使用 JavaScript 的編程模型作為查詢語言的基礎。 JavaScript 的類型系統、表達式計算和函數調用是 SQL API 的根。 這些根為關係投影、跨 JSON 項的分層導航、自聯接、空間查詢以及調用完全採用 JavaScript 編寫的用戶定義的函數 (UDF) 等功能提供自然編程模型。

本文基於簡單的 JSON 項來逐步講解一些示例 SQL 查詢。 若要詳細瞭解 Azure Cosmos DB SQL 語言語法，請參閱 [SQL 語法參考](sql-api-query-reference.md)。

## <a id="GettingStarted"></a>開始使用 SQL 查詢

在 Cosmos DB SQL API 帳戶中，創建名為 `Families` 的容器。 在該容器中創建兩個簡單的 JSON 項，然後針對這些項運行幾個簡單的查詢。

### <a name="create-json-items"></a>創建 JSON 項

以下代碼創建兩個有關家庭的簡單 JSON 項。 Andersen 和 Wakefield 家庭的簡單 JSON 項包括父母、孩子及其寵物、地址和註冊信息。 第一個項包含字符串、數字、布爾、數組和嵌套屬性。


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

第二個項使用 `givenName` 和 `familyName`，而不是使用 `firstName` 和 `lastName`。

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

### <a name="query-the-json-items"></a>查詢 JSON 項

嘗試對此 JSON 數據執行一些查詢來瞭解 Azure Cosmos DB 的 SQL 查詢語言的一些重要方面。

以下查詢返回其中的 `id` 字段與 `AndersenFamily` 匹配的項。 由於它是一個 `SELECT *` 查詢，因此該查詢的輸出是完整的 JSON 項。 有關 SELECT 語法的詳細信息，請參閱 [SELECT 語句](sql-api-query-reference.md#select-query)。 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

查詢結果為： 

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

以下查詢將 JSON 輸出的格式重新設置為不同的形式。 當地址中的城市名稱與州名稱相同時，該查詢將使用兩個選定的字段 `Name` 和 `City` 來投影新的 JSON `Family` 對象。 “NY, NY”符合這種情況。

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

查詢結果為：

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

上述示例演示了 Cosmos DB 查詢語言的幾個方面：  

* 由於 SQL API 適用於 JSON 值，因此它可以處理三種形式的實體，而不是行和列。 可以引用任意深度的樹節點（例如 `Node1.Node2.Node3…..Nodem`），類似於 ANSI SQL 中的 `<table>.<column>` 的兩部分引用。

* 由於查詢語言適用於無架構數據，因此，必須動態綁定類型系統。 相同的運算式在不同的項目上可能會產生不同的類型。 查詢的結果是有效的 JSON 值，但不保證它是固定的架構。  

* Azure Cosmos DB 只支援嚴謹的 JSON 項目。 類型系統和表達式僅限於處理 JSON 類型。 有關詳細信息，請參閱 [JSON 規範](https://www.json.org/)。  

* Cosmos DB 容器是 JSON 項目的無結構描述集合。 容器項內部以及跨容器項的關係是按包含關係隱式捕獲的，而不是按主鍵和外鍵關係捕獲的。 此特性對於本文稍後要討論的項內聯接非常重要。

## <a id="SelectClause"></a>SELECT 子句

每個查詢按 ANSI SQL 標準由 SELECT 子句和可選的 FROM 和 WHERE 子句組成。 通常，將會枚舉 FROM 子句中的源，WHERE 子句對該源應用一個篩選器，以檢索 JSON 項的子集。 然後，SELECT 子句在 select 列表中投影請求的 JSON 值。 有關語法的詳細信息，請參閱 [SELECT 語句](sql-api-query-reference.md#select-query)。

以下 SELECT 查詢示例從 `id` 匹配 `AndersenFamily` 的 `Families` 中返回 `address`：

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
可以使用帶引號的屬性運算符 [] 訪問屬性。 例如， `SELECT c.grade` and `SELECT c["grade"]` 是相等的。 此語法很適合用於轉義包含空格和特殊字符的屬性，或者其名稱與 SQL 關鍵字或保留字相同的屬性。

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="nested-properties"></a>巢狀屬性

以下示例投影兩個嵌套屬性：`f.address.state` 和 `f.address.city`。

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

## <a name="json-expressions"></a>JSON 表達式

投影也支持 JSON 表達式，如以下示例所示：

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

在上述示例中，SELECT 子句需要創建一個 JSON 對象；由於該示例未提供鍵，因此子句使用了隱式參數變量名稱 `$1`。 以下查詢返回兩個隱式參數變量：`$1` 和 `$2`。

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

VALUE 關鍵字提供一種只返回 JSON 值的方式。 例如，下面所示的查詢返回標量表達式 `"Hello World"` 而不是 `{$1: "Hello World"}`：

```sql
    SELECT VALUE "Hello World"
```

以下查詢返回不帶 `address` 標籤的 JSON 值：

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

以下示例演示如何返回 JSON 基元值（JSON 樹的葉級別）：


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

可以顯式為查詢中的值指定別名。 如果查詢包含兩個同名的屬性，請使用別名來重命名其中一個或兩個屬性，以便可以在投影的結果中消除其歧義。

如以下示例所示，將第二個值投影為 `NameInfo` 時，用於別名的 AS 關鍵字是可選的：

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

FROM (`FROM <from_specification>`) 子句是可選的，除非稍後在查詢中對源進行篩選或投影。 有關語法的詳細信息，請參閱 [FROM 語法](sql-api-query-reference.md#bk_from_clause)。 `SELECT * FROM Families` 之類的查詢枚舉整個 `Families` 容器。 還可以對容器使用特殊標識符 ROOT，而無需使用容器名稱。

FROM 子句對每個查詢強制實施以下規則：

* 您可以為容器設定別名，例如 `SELECT f.id FROM Families AS f` 或只是 `SELECT f.id FROM Families f`。 此處的 `f` 是 `Families` 的別名。 AS 是可選的關鍵字，用於指定標識符的別名。  

* 指定別名後，無法綁定原始的源名稱。 例如，`SELECT Families.id FROM Families f` 在語法上是無效的，原因是標識符 `Families` 已指定別名，因此不再可以解析。  

* 所有被引用的屬性必須完全限定，以避免在不嚴格遵守架構時出現任何有歧義的綁定。 例如，`SELECT id FROM Families f` 在語法上是無效的，因為未綁定屬性 `id`。

### <a name="get-subitems-by-using-the-from-clause"></a>使用 FROM 子句獲取子項

FROM 子句可將源化簡為更小的子集。 要在每個項中僅枚舉子樹，子根可能會變成源，如以下示例所示：

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

上述查詢使用數組作為源，但你也可以使用對象作為源。 該查詢考慮將源中任何有效的已定義 JSON 值包含在結果中。 下列範例會排除`Families`，沒有`address.state`值。

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

可選的 WHERE 子句 (`WHERE <filter_condition>`) 指定條件，查詢只會將滿足這些條件的源 JSON 項包含在結果中。 JSON 項必須將指定的條件評估為 `true` 才被視作結果。 索引層使用 WHERE 子句來確定可以作為結果的一部分的源項的最小子集。 有關語法的詳細信息，請參閱 [WHERE 語法](sql-api-query-reference.md#bk_where_clause)。

以下查詢請求包含值為 `AndersenFamily` 的 `id` 屬性的項。 它會排除任何不帶 `id` 屬性或值與 `AndersenFamily` 不匹配的項。

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

### <a name="scalar-expressions-in-the-where-clause"></a>WHERE 子句中的標量表達式

前一個範例已顯示簡單的相等查詢。 SQL API 還支持各種[標量表達式](#scalar-expressions)。 最常用的是二元和一元運算式。 來源 JSON 物件中的屬性參考也是有效的運算式。

可以使用以下受支持的二元運算符：  

|**運算子類型**  | **值** |
|---------|---------|
|算術 | +、-、*、/、% |
|位元    | \|, &, ^, <<, >>, >>> (zero-fill right shift) |
|邏輯    | AND、OR、NOT      |
|比較 | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|字串     |  \|\| (串連) |

以下查詢使用二元運算符：

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

還可以在查詢中使用一元運算符 +、-、~ 和 NOT，如以下示例所示：

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

還可以在查詢中使用屬性引用。 例如，`SELECT * FROM Families f WHERE f.isRegistered` 返回包含值等於 `true` 的 `isRegistered` 屬性的 JSON 項。 任何其他值（例如`false`、`null`、`Undefined`、`<number>`、`<string>`、`<object>` 或 `<array>`）會從結果中排除該項。 

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

對於 `>`、`>=`、`!=`、`<` 和 `<=` 等比較運算符，跨類型的比較或者兩個對象或數組之間的比較會生成 `Undefined`。  

如果標量表達式的結果為 `Undefined`，則不會將該項包含在結果中，因為 `Undefined` 不等於 `true`。

### <a name="logical-and-or-and-not-operators"></a>邏輯 (AND、OR 和 NOT) 運算子

邏輯運算子的運算對象是布林值。 下表顯示了這些運算符的邏輯真值表：

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

與在 ANSI SQL 中一樣，可以使用 BETWEEN 關鍵字來對字符串或數字值的範圍表達查詢。 例如，以下查詢返回其中第一個孩子的年級為 1-5（含）的所有項。

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

與在 ANSI-SQL 中不同，你還可以在 FROM 子句中使用 BETWEEN 子句，如以下示例所示。

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

與 ANSI SQL 不同，在 SQL API 中，可以針對混合類型的屬性表達範圍查詢。 例如，在某些項中，`grade` 可能是類似於 `5` 的數字；而在其他一些項中，它可能是類似於 `grade4` 的字符串。 在這些情況下（與在 JavaScript 中一樣），兩個不同類型之間的比較會生成 `Undefined`，因此會跳過該項。

> [!TIP]
> 為了更快地執行查詢，請創建一個索引策略，該策略針對 BETWEEN 子句篩選的任何數字屬性或路徑使用範圍索引類型。

## <a name="in-keyword"></a>IN 關鍵字

使用 IN 關鍵字可以檢查指定的值是否與列表中的任一值匹配。 例如，以下查詢返回 `id` 為 `WakefieldFamily` 或 `AndersenFamily` 的所有家庭項。

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

以下示例返回狀態為任何指定值的所有項：

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="-operator"></a>* 運算符

特殊運算符 * 按原樣投影整個項。 使用時，它必須是唯一投射的欄位。 類似於 `SELECT * FROM Families f` 的查詢是有效的，而 `SELECT VALUE * FROM Families f` 和 `SELECT *, f.id FROM Families f` 是無效的。 [本文中的第一個查詢](#query-the-json-items)使用了 * 運算符。 

## <a name="-and--operators"></a>? 和 ?? 運算符

如同在 C# 和 JavaScript 等編程語言中那樣，可以使用三元 (?) 和聯合 (??) 運算符來生成條件表達式。 

可以使用 ? 運算符即時構造新的 JSON 屬性。 例如，以下查詢將年級分類為 `elementary` 或 `other`：

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

還可以將調用嵌套到 ? 運算符，如以下查詢中所示： 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

與其他查詢運算符一樣， 如果引用的屬性缺失或者要比較的類型不同，則 ? 運算符將會排除項。

查詢半結構化 或混合類型的數據時，可以使用 ?? 運算符有效地檢查項中的屬性。 例如，以下查詢返回 `lastName`（如果存在）或 `surname`（如果 `lastName` 不存在）。

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="TopKeyword"></a>TOP 運算子

TOP 關鍵字以未定義的順序返回前 `N` 個查詢結果。 最佳做法是結合使用 TOP 與 ORDER BY 子句，將結果限制為前 `N` 個有序值。 要預見性地指示哪些行受到 TOP 的影響，只能結合使用這兩個子句。

可以結合一個常量值使用 TOP（如以下示例中所示），或者在參數化查詢中結合一個變量值使用 TOP。 有關詳細信息，請參閱[參數化查詢](#parameterized-queries)部分。

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

與在 ANSI SQL 中一樣，可以在查詢中包含可選的 ORDER BY 子句。 可選的 ASC 或 DESC 參數指定是要按升序還是降序檢索結果。 默認值為 ASC。

例如，以下查詢按居住城市名稱的升序檢索家庭：

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

以下查詢按項的創建日期檢索家庭 `id`。 項 `creationDate` 是一個數字，表示紀元時間，或者自 1970 年 1 月 1 日開始消逝的時間（以秒為單位）。

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

SELECT 子句支持標量表達式，例如常量、算術表達式和邏輯表達式。 以下查詢使用一個標量表達式：


```sql
    SELECT ((2 + 11 % 7)-2)/3
```

結果為：

```json
    [{
      "$1": 1.33333
    }]
```

在以下查詢中，標量表達式的結果是一個布爾值：


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

SQL API 的一個重要功能是創建數組和對象。 以上示例創建了新的 JSON 對象 `AreFromSameCityState`。 還可以構造數組，如以下示例所示：


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

SQL API 支持循環訪問 JSON 數組，它可以通過 FROM 源中的 IN 關鍵字添加一個新的構造。 在以下示例中：

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

下一個查詢循環訪問 `Families` 容器中的 `children`。 輸出的數組與前面的查詢不同。 此示例拆分 `children` 並將結果平展為單個數組：  

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

可以進一步篩選該數組的每個條目，如以下示例所示：

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

還可基於數組迭代的結果進行聚合。 例如，以下查詢計數所有家庭中的孩子數目。

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

在關係數據庫中，跨表聯接是設計規範化架構的邏輯定理。 相比之下，SQL API 使用無架構項的反規範化數據模型，這在邏輯上等效於自聯接。

該語言支持語法 `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`。 此查詢返回一組包含 `N` 值的元組。 每個 Tuple 所擁有的值，都是將所有容器別名在其個別集合上反覆運算所產生的。 換句話說，此查詢會對參與聯結的集合執行完整的交叉乘積。

下列範例示範 JOIN 子句的運作方式。 在以下示例中，由於源中每個項和空集的叉積為空，因此結果為空：

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

在以下示例中，聯接是兩個 JSON 對象、項根 `id` 和子根 `children` 之間的叉積。 `children` 是數組這一事實在聯接中不起作用，因為查詢處理的是作為 `children` 數組的單一根。 由於每個帶有數組的項的叉積僅生成一個項，因此結果僅包含兩個結果。

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

JOIN 子句的 FROM 源是一個迭代器。 因此，以上示例中的流程為：  

1. 展開數組中的每個子元素 `c`。
2. 應用包含項 `f` 的根的叉積，該項包含已在第一個步驟中平展的每個子元素 `c`。
3. 最後，單獨投影根對象 `f` `id` 屬性。

第一個項 (`AndersenFamily`) 僅包含一個 `children` 元素，因此結果集僅包含單個對象。 第二個項 `WakefieldFamily` 包含兩個 `children`，因此，叉積為每個 `children` 元素生成一個對象，共兩個對象。 這兩個項目中的根欄位相同，就像您在交叉乘積中預期地一樣。

JOIN 子句真正實用的地方是通過以其他方式難以投影的形式基於叉積生成元組。 以下示例對元組組合進行篩選，讓用戶選擇元組在整體上滿足的條件。

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

以下示例對前一個示例做了延伸，將會執行雙重聯接。 可將叉積視為下面所示的偽代碼：

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

`AndersenFamily` 中有一個孩子擁有一隻寵物，因此叉積從此家庭生成了一行 (1\*1\*1)。 `WakefieldFamily` 中有兩個孩子，其中只有一個孩子擁有寵物，但這個孩子擁有兩隻寵物。 叉積對此家庭生成了 1\*1\*2 = 2 行。

以下示例根據 `pet` 進行了額外的篩選，這排除了寵物名稱不是 `Shadow` 的所有元組。 可以基於數組生成元組，根據元組的任意元素進行篩選以及投影元素的任何組合。

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

SQL API 支持用戶定義函數 (UDF)。 使用標量 UDF，可以傳入零個或多個參數，並返回單個參數結果。 API 會檢查每個參數 JSON 值是否合法。  

API 擴展了 SQL 語法，支持使用 UDF 的自定義應用程序邏輯。 可將 UDF 註冊到 SQL API，然後在 SQL 查詢中引用它們。 實際上，UDF 是特別設計來透過查詢進行呼叫的。 作為一種定理，UDF 不能像其他 JavaScript 類型（例如存儲過程和觸發器）一樣訪問上下文對象。 查詢是只讀的，可以在主要或次要副本上運行。 與其他 JavaScript 類型不同，UDF 只能在次要副本上運行。

以下示例在 Cosmos DB 數據庫中的某個項容器下註冊一個 UDF。 該示例創建了名為 `REGEX_MATCH` 的 UDF。 它接受兩個 JSON 字符串值：`input` 和 `pattern`，並使用 JavaScript 的 `string.match()` 函數檢查第一個值是否與第二個值中指定的模式相匹配。

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

現在，請在查詢投影中使用此 UDF。 從查詢內部調用 UDF 時，必須使用區分大小寫的前綴 `udf.` 來限定 UDF。

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

可以在篩選器中使用以 `udf.` 前綴限定的 UDF，如以下示例所示：

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

從本質上來說，UDF 是可以在投影和篩選器中使用的有效標量表達式。

為了進一步瞭解 UDF 的強大功能，讓我們查看使用條件邏輯的另一個示例：

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

以下示例運用了 UDF：

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

如果 UDF 參數引用的屬性在 JSON 值中未提供，則會將該參數視為未定義，因此會跳過 UDF 調用。 同樣，如果未定義 UDF 的結果，則不會將此 UDF 包含在結果中。

如以上示例所示，UDF 將 JavaScript 語言的強大功能與 SQL API 相集成。 UDF 提供豐富的可編程接口來執行複雜的過程，並借助內置的 JavaScript 運行時功能來執行條件邏輯。 SQL API 在當前的 WHERE 或 SELECT 子句處理階段，為每個源項的 UDF 提供參數。 結果將無縫整合到總體執行管道中。 總而言之，UDF 是在查詢過程中執行複雜業務邏輯的極佳工具。

## <a id="Aggregates"></a>彙總函式

聚合函數對 SELECT 子句中的一組值執行計算，並返回單個值。 例如，以下查詢返回 `Families` 容器中的項計數。

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

也可以使用 VALUE 關鍵字來僅返回聚合的標量值。 例如，下列查詢會以單一數字傳回值的計數：

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

結果為：

```json
    [ 2 ]
```

還可以將聚合與篩選器結合使用。 例如，以下查詢返回包含 `WA` 州地址的項計數。

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

結果為：

```json
    [ 1 ]
```

SQL API 支持以下聚合函數。 SUM 和 AVG 針對數字值執行運算，COUNT、MIN 和 MAX 可以處理數字、字符串、布爾值和 null。

| 函式 | 描述 |
|-------|-------------|
| COUNT | 以運算式傳回項目的數目。 |
| SUM   | 以運算式傳回所有值的總和。 |
| 最小值   | 以運算式傳回最小值。 |
| MAX   | 以運算式傳回最大值。 |
| 平均值   | 以運算式傳回值的平均。 |

還可以基於數組迭代的結果進行聚合。 有關詳細信息，請參閱[迭代](#Iteration)部分。

> [!NOTE]
> 在 Azure 門戶的數據資源管理器中，聚合查詢可以僅基於一個查詢頁面聚合部分結果。 SDK 跨所有頁面生成單個累計值。 若要使用代碼執行聚合查詢，需要 .NET SDK 1.12.0、.NET Core SDK 1.1.0，或者 Java SDK 1.9.5 或更高版本。
>

## <a id="BuiltinFunctions"></a>內建函數

Cosmos DB 還支持使用許多內置函數進行常見操作，這些函數可以在查詢（如用戶定義的函數 (UDF)）中使用。

| 函式群組 | 作業 |
|---------|----------|
| 數學函數 | ABS、CEILING、EXP、FLOOR、LOG、LOG10、POWER、ROUND、SIGN、SQRT、SQUARE、TRUNC、ACOS、ASIN、ATAN、ATN2、COS、COT、DEGREES、PI、RADIANS、SIN、TAN |
| 類型檢查函數 | IS_ARRAY、IS_BOOL、IS_NULL、IS_NUMBER、IS_OBJECT、IS_STRING、IS_DEFINED、IS_PRIMITIVE |
| 字串函數 | CONCAT、CONTAINS、ENDSWITH、INDEX_OF、LEFT、LENGTH、LOWER、LTRIM、REPLACE、REPLICATE、REVERSE、RIGHT、RTRIM、STARTSWITH、SUBSTRING、UPPER |
| 陣列函數 | ARRAY_CONCAT、ARRAY_CONTAINS、ARRAY_LENGTH 和 ARRAY_SLICE |
| 空間函數 | ST_DISTANCE、ST_WITHIN、ST_INTERSECTS、ST_ISVALID、ST_ISVALIDDETAILED |

如果您目前使用的使用者定義的函數 (UDF) 已提供內建函式，相對應的內建函式會加快執行速度且更有效率。

Cosmos DB 函數與 ANSI SQL 函數之間的主要差別在於，Cosmos DB 函數能夠很好地處理無架構數據和混合架構數據。 例如，如果某個屬性缺失或包含類似於 `unknown` 的非數字值，則會跳過該項，而不是返回錯誤。

### <a name="mathematical-functions"></a>數學函數

每個數學函式都會執行計算，通常以提供來作為引數的輸入值為基礎，並傳回數值。 以下是支援的內建數學函數資料表。

| 使用量 | 描述 |
|----------|--------|
| ABS (num_expr) | 傳回指定之數值運算式的絕對 (正) 值。 |
| CEILING (num_expr) | 傳回大於或等於指定之數值運算式的最小整數值。 |
| FLOOR (num_expr) | 傳回小於或等於指定之數值運算式的最大整數。 |
| EXP (num_expr) | 傳回指定之數值運算式的指數。 |
| LOG (num_expr, base) | 返回指定數值表達式的自然對數，或使用指定底數的對數。 |
| LOG10 (num_expr) | 傳回指定之數值運算式的以 10 為基底的對數值。 |
| ROUND (num_expr) | 傳回數值，四捨五入到最接近的整數值。 |
| TRUNC (num_expr) | 傳回數值，截斷至最接近的整數值。 |
| SQRT (num_expr) | 傳回指定之數值運算式的平方根。 |
| SQUARE (num_expr) | 傳回指定之數值運算式的平方。 |
| POWER (num_expr, num_expr) | 將指定數值運算式的乘冪傳回給指定的值。 |
| SIGN (num_expr) | 傳回指定之數值運算式的正負號值 (-1、0、1)。 |
| ACOS (num_expr) | 傳回角度，以弧度為單位，它的餘弦是指定的數值運算式；也稱為反餘弦值。 |
| ASIN (num_expr) | 傳回角度，以弧度為單位，其正弦函數是指定的數值運算式。 此函數也稱為反正弦值。 |
| ATAN (num_expr) | 傳回角度，以弧度為單位，其正切函數是指定的數值運算式。 此函數也稱為反正切。 |
| ATN2 (num_expr) | 傳回角度，以弧度為單位，正 x 軸和從原點 (y、x) 點的切線之間，其中 x 和 y 是兩個指定之浮點運算式的值。 |
| COS (num_expr) | 在指定運算式中傳回指定角度的三角餘弦函數，以弧度為單位。 |
| COT (num_expr) | 在指定的數值運算式中傳回指定角度的三角餘切函數，以弧度為單位。 |
| DEGREES (num_expr) | 針對以弧度指定的角度，傳回對應的角度 (以度為單位)。 |
| PI () | 傳回 PI 的常數值。 |
| RADIANS (num_expr) | 當您輸入數值運算式 (以度為單位) 時，傳回弧度。 |
| SIN (num_expr) | 在指定運算式中傳回指定角度的三角正弦函數 (以弧度為單位)。 |
| TAN (num_expr) | 在指定運算式中傳回輸入運算式的正切函數。 |

可以運行以下示例所示的查詢：

```sql
    SELECT VALUE ABS(-4)
```

結果如下：

```json
    [4]
```

### <a name="type-checking-functions"></a>類型檢查函數

使用類型檢查函數可以檢查 SQL 查詢中表達式的類型。 當項中的屬性可變或未知時，可以使用類型檢查函數即時確定這些屬性的類型。 以下是支援的內建類型檢查函數的資料表：

| **使用量** | **說明** |
|-----------|------------|
| [IS_ARRAY (expr)](sql-api-query-reference.md#bk_is_array) | 傳回布林值，表示值的類型是否為陣列。 |
| [IS_BOOL (expr)](sql-api-query-reference.md#bk_is_bool) | 返回一個布爾值，它指示值的類型是否為布爾。 |
| [IS_NULL (expr)](sql-api-query-reference.md#bk_is_null) | 返回一個布爾值，它指示值的類型是否為 null。 |
| [IS_NUMBER (expr)](sql-api-query-reference.md#bk_is_number) | 返回一個布爾值，它指示值的類型是否為數字。 |
| [IS_OBJECT (expr)](sql-api-query-reference.md#bk_is_object) | 返回一個布爾值，它指示值的類型是否為 JSON 對象。 |
| [IS_STRING (expr)](sql-api-query-reference.md#bk_is_string) | 返回一個布爾值，它指示值的類型是否為字符串。 |
| [IS_DEFINED (expr)](sql-api-query-reference.md#bk_is_defined) | 返回一個布爾，它指示屬性是否已經分配了值。 |
| [IS_PRIMITIVE (expr)](sql-api-query-reference.md#bk_is_primitive) | 返回一個布爾值，它指示值的類型是字符串、數字、布爾值還是 null。 |

使用這些函數可以運行以下示例所示的查詢：

```sql
    SELECT VALUE IS_NUMBER(-4)
```

結果如下：

```json
    [true]
```

### <a name="string-functions"></a>字串函數

以下標量函數對字符串輸入值執行操作，並返回字符串、數字或布爾值。 以下是內建字串函數的資料表：

| 使用量 | 描述 |
| --- | --- |
| [LENGTH (str_expr)](sql-api-query-reference.md#bk_length) | 傳回指定字串運算式的字元數目。 |
| [CONCAT (str_expr, str_expr [, str_expr])](sql-api-query-reference.md#bk_concat) | 傳回字串，該字串是串連兩個或多個字串值的結果。 |
| [SUBSTRING (str_expr, num_expr, num_expr)](sql-api-query-reference.md#bk_substring) | 返回部分字符串表達式。 |
| [STARTSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_startswith) | 傳回布林值，表示第一個字串運算式是否以第二個字串運算式開頭。 |
| [ENDSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_endswith) | 傳回布林值，表示第一個字串運算式是否以第二個字串運算式結尾。 |
| [CONTAINS (str_expr, str_expr)](sql-api-query-reference.md#bk_contains) | 傳回布林值，表示第一個字串運算式是否包含第二個字串運算式。 |
| [INDEX_OF (str_expr, str_expr)](sql-api-query-reference.md#bk_index_of) | 返回第一個指定的字符串表達式中第一次出現第二個字符串表達式的起始位置，如果未找到字符串，則返回 -1。 |
| [LEFT (str_expr, num_expr)](sql-api-query-reference.md#bk_left) | 返回具有指定字符數的字符串的左側部分。 |
| [RIGHT (str_expr, num_expr)](sql-api-query-reference.md#bk_right) | 返回具有指定字符數的字符串的右側部分。 |
| [LTRIM (str_expr)](sql-api-query-reference.md#bk_ltrim) | 傳回移除開頭空白之後的字串運算式。 |
| [RTRIM (str_expr)](sql-api-query-reference.md#bk_rtrim) | 傳回截斷所有結尾空白之後的字串運算式。 |
| [LOWER (str_expr)](sql-api-query-reference.md#bk_lower) | 傳回將大寫字元資料轉換成小寫之後的字串運算式。 |
| [UPPER (str_expr)](sql-api-query-reference.md#bk_upper) | 傳回將小寫字元資料轉換成大寫之後的字串運算式。 |
| [REPLACE (str_expr, str_expr, str_expr)](sql-api-query-reference.md#bk_replace) | 將出現的所有指定字符串值替換為另一個字符串值。 |
| [REPLICATE (str_expr, num_expr)](sql-api-query-reference.md#bk_replicate) | 將字串值重複指定的次數。 |
| [REVERSE (str_expr)](sql-api-query-reference.md#bk_reverse) | 傳回反向順序的字串值。 |

使用這些函數可以運行如下以下所示的查詢，該查詢返回大寫形式的家庭 `id`：

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

或者，如以下示例所示連接字符串：

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

還可以在 WHERE 子句中使用字符串函數來篩選結果，如以下示例所示：

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

以下標量函數對數組輸入值執行操作，並返回數字值、布爾值或數組值。 以下是內建陣列函數的資料表：

| 使用量 | 描述 |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](sql-api-query-reference.md#bk_array_length) |傳回指定陣列運算式的元素數目。 |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](sql-api-query-reference.md#bk_array_concat) |傳回串連兩個或多個陣列值之結果的陣列。 |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](sql-api-query-reference.md#bk_array_contains) |傳回布林值，表示陣列是否包含指定值。 可以指定要進行完整或部分比對。 |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](sql-api-query-reference.md#bk_array_slice) |傳回陣列運算式的一部分。 |

使用數組函數可以處理 JSON 中的數組。 例如，以下查詢返回其中一個 `parents` 是 `Robin Wakefield` 的所有項 `id`： 

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

您可以指定部分片段來比對陣列內的元素。 以下查詢查找包含 `givenName` 為 `Robin` 的 `parents` 的所有項 `id`：

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

下面是使用 ARRAY_LENGTH 獲取每個家庭的 `children` 數目的另一個示例：

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

Cosmos DB 支持以下用於查詢地理空間的開放地理空間信息聯盟 (OGC) 內置函數： 

| 使用量 | 描述 |
| --- | --- |
| ST_DISTANCE (point_expr、point_expr) | 返回兩個 GeoJSON `Point`、`Polygon` 或 `LineString` 表達式之間的距離。 |
| T_WITHIN (point_expr, polygon_expr) | 返回一個布爾表達式，指示第一個 GeoJSON 對象（`Point`、`Polygon` 或 `LineString`）是否在第二個 GeoJSON 對象（`Point`、`Polygon` 或 `LineString`）的內部。 |
| ST_INTERSECTS (spatial_expr, spatial_expr) | 返回一個布爾表達式，指示兩個指定的 GeoJSON 對象（`Point`、`Polygon` 或 `LineString`）是否相交。 |
| ST_ISVALID | 返回一個布爾值，指示指定的 GeoJSON `Point`、`Polygon` 或 `LineString` 表達式是否有效。 |
| ST_ISVALIDDETAILED | 如果指定的 GeoJSON `Point` 或、`Polygon` 或 `LineString` 表達式有效，則返回包含布爾值的 JSON 值；如果無效，則返回字符串值形式的原因。 |

可以使用空間函數對空間數據執行鄰近查詢。 例如，以下查詢使用 ST_DISTANCE 內置函數返回所有家庭項，且這些文檔在指定位置的 30 公里內：

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

## <a name="parameterized-queries"></a>參數化查詢

Cosmos DB 支持使用帶有常用 @ 表示法的參數進行查詢。 參數化 SQL 為用戶輸入提供可靠的處理和轉義，可防止通過 SQL 注入發生意外的數據洩露。

例如，可以編寫一個將 `lastName` 和 `address.state` 用作參數的查詢，並根據用戶輸入針對 `lastName` 和 `address.state` 的各種值執行此查詢。

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

然後，可將此請求作為參數化 JSON 查詢發送到 Cosmos DB，如下所示：

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

以下示例使用參數化查詢設置 TOP 參數： 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

參數值可以是任何有效的 JSON：字符串、數字、布爾值、null，甚至數組或嵌套的 JSON。 由於 Cosmos DB 是無架構的，因此不會針對任何類型驗證參數。

## <a id="JavaScriptIntegration"></a>JavaScript 整合

Azure Cosmos DB 提供一種編程模型，用於通過存儲過程和觸發器對容器直接執行基於 JavaScript 的應用程序邏輯。 此模型支持：

* 通過在數據庫引擎中深度集成 JavaScript 運行時，對容器中的項執行高性能事務性 CRUD 操作和查詢。
* 控制流、變量範圍和分配的自然建模和將異常處理基元與數據庫事務集成。 

有關 Azure Cosmos DB JavaScript 集成的詳細信息，請參閱 [JavaScript 服務器端 API](#JavaScriptServerSideApi) 部分。

### <a name="operator-evaluation"></a>運算子評估

Cosmos DB 是一個 JSON 數據庫，在 JavaScript 運算符和評估語義方面具有許多相似之處。 就 JSON 支持而言，Cosmos DB 會儘量保留 JavaScript 語義，但運算求值在某些情況下會有所不同。

與傳統 SQL 不同，在 SQL API 中，在該 API 從數據庫檢索出值之前，值的類型往往是未知的。 為了有效率地執行查詢，大部分的運算子都有嚴謹的類型需求。

與 JavaScript 不同，SQL API 不會執行隱式轉換。 例如，類似於 `SELECT * FROM Person p WHERE p.Age = 21` 的查詢會匹配包含 `21` 值的 `Age` 屬性的項。 它不會匹配其 `Age` 屬性可能與 `twenty-one`、`021` 或 `21.0` 等無限變體匹配的任何其他項。 這與 JavaScript 相反，在 JavaScript 中，字符串值會根據 `==` 等運算符隱式轉換為數字。 此 SQL API 行為對於有效的索引匹配至關重要。

## <a id="ExecutingSqlQueries"></a>執行 SQL 查詢

能夠發出 HTTP/HTTPS 請求的任何語言都可以調用 Cosmos DB REST API。 Cosmos DB 還為 .NET、Node.js、JavaScript 和 Python 編程語言提供編程庫。 REST API 和庫全部支持通過 SQL 執行的查詢，.NET SDK 還支持 [LINQ 查詢](#Linq)。

下列範例顯示如何建立查詢，並針對 Cosmos DB 資料庫帳戶提交它。

### <a id="RestAPI"></a>REST API

Cosmos DB 提供透過 HTTP 的開放 RESTful 程式設計模型。 資源模型由 Azure 訂閱預配的數據庫帳戶下的一組資源組成。 該數據庫帳戶由一組數據庫組成，其中的每個數據庫可以包含多個容器，而每個容器又可以包含項、UDF 和其他資源類型。 可以使用穩定的邏輯 URI 對每個 Cosmos DB 資源進行尋址。 一組資源稱作一個源。 

這些資源的基本交互模型是通過 HTTP 謂詞 `GET`、`PUT`、`POST` 和 `DELETE` 及其標準解釋實現的。 使用 `POST` 可以創建新的資源、執行存儲過程或發出 Cosmos DB 查詢。 查詢一律是唯讀作業，而且沒有任何副作用。

以下示例演示了如何根據示例項針對 SQL API 查詢發出 `POST`。 該查詢對 JSON `name` 屬性應用一個簡單的篩選器。 `x-ms-documentdb-isquery` 和 Content-Type: `application/query+json` 標頭表示該操作是一個查詢。 請將 `mysqlapicosmosdb.documents.azure.com:443` 替換為 Cosmos DB 帳戶的 URI。

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

下面這個更複雜的查詢從某個聯接操作返回多個結果：

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

如果查詢的結果無法包含在一頁中，則 REST API 會通過 `x-ms-continuation-token` 響應標頭返回繼續標記。 客戶端可以通過在後續結果中包含該標頭對結果進行分頁。 也可以通過 `x-ms-max-item-count` 數字標頭控制每頁的結果數。 

如果查詢包含 COUNT 等聚合函數，則查詢頁可以僅通過一個結果頁返回部分聚合的值。 客戶端必須對這些結果執行二級聚合才能生成最終結果。 例如，將各個頁中返回的計數求和可以返回總計數。

若要管理查詢的數據一致性策略，請使用 `x-ms-consistency-level` 標頭（如所有的 REST API 請求）。 對於會話一致性，還需要回顯查詢請求中最新的 `x-ms-session-token` Cookie 標頭。 所查詢容器的編製索引原則也會影響查詢結果的一致性。 使用默認的索引策略設置，容器的索引始終與項內容保持同步，且查詢結果將與為數據選擇的一致性匹配。 有關詳細信息，請參閱 [Azure Cosmos DB 一致性級別][consistency-levels]。

如果容器上配置的索引策略不能支持指定的查詢，Azure Cosmos DB 服務器會返回 400“錯誤的請求”。 使用從索引中顯式排除的路徑執行查詢時，將返回此錯誤消息。 可以指定 `x-ms-documentdb-query-enable-scan` 標頭，以便在索引不可用時允許查詢執行掃瞄。

可以通過將 `x-ms-documentdb-populatequerymetrics` 標頭設置為 `true` 來獲取有關查詢執行的詳細指標。 如需詳細資訊，請參閱[適用於 Azure Cosmos DB 的 SQL 查詢計量](sql-api-query-metrics.md)。

### <a id="DotNetSdk"></a>C# (.NET SDK)

.NET SDK 支援 LINQ 和 SQL 查詢。 以下示例演示如何使用 .NET 執行前面所示的篩選查詢：

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

以下示例比較了每個項內等式的兩個屬性，並使用匿名投影。

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

以下示例演示通過 LINQ `SelectMany` 表達的聯接。

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

.NET 客戶端自動循環訪問 `foreach` 塊中所有的查詢結果頁，如前面的示例中所示。 [REST API](#RestAPI) 部分介紹的查詢選項也適用於在 `CreateDocumentQuery` 方法中使用 `FeedOptions` 和 `FeedResponse` 類的 .NET SDK。 可以使用 `MaxItemCount` 設置控制頁數。

您可以建立，以明確地控制分頁`IDocumentQueryable`使用`IQueryable`物件，然後藉由讀取`ResponseContinuationToken`做為傳回的值，並將它們`RequestContinuationToken`在`FeedOptions`。 可以設置 `EnableScanInQuery`，以便在配置的索引策略不支持該查詢時啟用掃瞄。 對於分區容器，可以使用 `PartitionKey` 針對單個分區運行查詢，不過，Azure Cosmos DB 可以自動從查詢文本中提取此信息。 可以使用 `EnableCrossPartitionQuery` 針對多個分區運行查詢。

有關更多包含查詢的 .NET 示例，請參閱 GitHub 中的 [Azure Cosmos DB .NET 示例](https://github.com/Azure/azure-cosmosdb-dotnet)。

### <a id="JavaScriptServerSideApi"></a>JavaScript 伺服器端 API

Cosmos DB 提供一種編程模型，用於通過存儲過程和觸發器對容器直接執行基於 JavaScript 的應用程序邏輯。 然後，在容器級別註冊的 JavaScript 邏輯可以針對給定容器中的、包裝在環境 ACID 事務中的項發出數據庫操作。

以下示例演示了如何在 JavaScript 服務器 API 中使用 `queryDocuments` 從存儲過程和觸發器內部進行查詢。

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

LINQ 是一個 .NET 編程模型，它將計算表達為對對象流的查詢。 Cosmos DB 提供一個用戶端程式庫，可透過協助 JSON 與 .NET 物件之間的轉換，以及 LINQ 查詢子集與 Cosmos DB 查詢的對應，來與 LINQ 互動。

下圖演示了使用 Cosmos DB 的支持性 LINQ 查詢的體系結構。 使用 Cosmos DB 客戶端，可以創建直接查詢 Cosmos DB 查詢提供程序的 `IQueryable` 對象，並將 LINQ 查詢轉換為 Cosmos DB 查詢。 然後，將該查詢傳遞到 Cosmos DB 服務器，後者會檢索一組 JSON 格式的結果。 在客戶端中，JSON 反序列化程序會將結果轉換為 .NET 對象流。

![使用 SQL API 來支援 LINQ 查詢的架構：SQL 語法、JSON 查詢語言、資料庫概念及 SQL 查詢][1]

### <a name="net-and-json-mapping"></a>.NET 和 JSON 對應

.NET 對象與 JSON 項之間的映射是自然的。 每個數據成員字段映射到 JSON 對象，其中的字段名稱映射到對象的 *key* 部分，值以遞歸方式映射到該對象的 *value* 部分。 以下代碼將 `Family` 類映射到 JSON 項，然後創建 `Family` 對象：

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

以上示例將創建以下 JSON 項：

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

Cosmos DB 查詢提供者會盡力將 LINQ 查詢對應至 Cosmos DB SQL 查詢。 以下內容假設你對 LINQ 有一個基本的瞭解。

查詢提供程序類型系統僅支持 JSON 基元類型：數字、布爾值、字符串和 null。 

查詢提供程序支持以下標量表達式：

- 常量值，包括評估查詢時基元數據類型的常量值。
  
- 引用對象或數組元素的屬性的屬性/數組索引表達式。 例如︰
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- 算術表達式，包括針對數值和布爾值運行的常見算術表達式。 有關完整列表，請參閱 [Azure Cosmos DB SQL 規範](https://go.microsoft.com/fwlink/p/?LinkID=510612)。
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- 字符串比較表達式，包括將字符串值與某些常量字符串值進行比較。  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- 對象/數組創建表達式，返回覆合值類型或匿名類型的對象，或此類對象組成的數組。 可以嵌套這些值。
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

### <a id="SupportedLinqOperators"></a>支援的 LINQ 運算子

SQL .NET SDK 隨附的 LINQ 提供程序支持以下運算符：

- **Select**：投影轉換為 SQL SELECT（包括對象構造）。
- **Where**：篩選器轉換為 SQL WHERE，支持 `&&`、`||` 和 `!` 到 SQL 運算符的轉換
- **SelectMany**：可讓陣列回溯到 SQL JOIN 子句。 用於將表達式鏈接或嵌套到對數組元素應用的篩選器。
- **OrderBy** 和 **OrderByDescending**：使用 ASC 或 DESC 轉換為 ORDER BY。
- 彙總的 **Count**、**Sum**、**Min**、**Max** 與 **Average** 運算子，以及其非同步對應項 **CountAsync**、**SumAsync**、**MinAsync**、**MaxAsync** 與 **AverageAsync**。
- **CompareTo**：轉譯為範圍比較。 常用的字串，因為它們不在.NET 中比較。
- **Take**：轉換為 SQL TOP，用於限制查詢中的結果。
- **數學函數**：支持從 .NET `Abs`、`Acos`、`Asin`、`Atan`、`Ceiling`、`Cos`、`Exp`、`Floor`、`Log`、`Log10`、`Pow`、`Round`、`Sign`、`Sin`、`Sqrt`、`Tan` 和 `Truncate` 轉換為等效的 SQL 內置函數。
- **字符串函數**：支持從.NET  `Concat`、`Contains`、`Count`、`EndsWith`、`IndexOf`、`Replace`、`Reverse`、`StartsWith`、`SubString`、`ToLower`、`ToUpper`、`TrimEnd` 和 `TrimStart` 轉換為等效的 SQL 內置函數。
- **數組函數**：支持從 .NET `Concat`、`Contains` 和 `Count` 轉換為等效的 SQL 內置函數。
- **地理空間擴展函數**：支持從存根方法 `Distance`、`IsValid`、`IsValidDetailed` 和 `Within` 轉換為等效的 SQL 內置函數。
- **用戶定義的函數擴展函數**：支持從存根方法 `UserDefinedFunctionProvider.Invoke` 轉換為相應的用戶定義的函數。
- **其他**：支持 `Coalesce` 和條件運算符的轉換。 可以根據上下文將 `Contains` 轉換為字符串 CONTAINS、ARRAY_CONTAINS 或 SQL IN。

### <a name="sql-query-operators"></a>SQL 查詢運算子

以下示例演示了一些標準 LINQ 查詢運算符如何轉換為 Cosmos DB 查詢。

#### <a name="select-operator"></a>Select 運算符

語法為 `input.Select(x => f(x))`，其中 `f` 是純量運算式。

**Select 運算符，示例 1：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Select 運算符，示例 2：** 

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Select 運算符，示例 3：**

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

**Where 運算符，示例 1：**

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
  
**Where 運算符，示例 2：**

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

將上述運算符組合到一起可以構成更強大的查詢。 由於 Cosmos DB 支持嵌套的容器，因此你可以連接或嵌套這種組合。

#### <a name="concatenation"></a>串連

語法為 `input(.|.SelectMany())(.Select()|.Where())*`。 連接的查詢可以使用可選的 `SelectMany` 查詢開頭，後接多個 `Select` 或 `Where` 運算符。

**連接，示例 1：**

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

**連接，示例 2：**

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

**連接，示例 3：**

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

**連接，示例 4：**

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

語法為 `input.SelectMany(x=>x.Q())`，其中 `Q` 是 `Select`、`SelectMany` 或 `Where` 運算符。

嵌套查詢會將內部查詢應用到外部容器的每個元素。 一個重要的功能是內部查詢可以引用外部容器（如自聯接）中元素的字段。

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
- [Javascript 規範](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 
- Graefe, Goetz。 [Query evaluation techniques for large databases](https://dl.acm.org/citation.cfm?id=152611)（適用於大型數據庫的查詢評估技術） 《ACM 計算調查》第 25 期 第 2 卷 (1993)。
- Graefe, G。“用於優化查詢的 Cascades 框架”。 《IEEE 數據工程期刊》 第 18 期 第 3 卷 (1995)。
- Lu、Ooi、Tan。 “並行關係數據庫系統中的查詢處理”。 《IEEE 計算機協會期刊》(1994)。
- Olston、Christopher、Benjamin Reed、Utkarsh Srivastava、Ravi Kumar 和 Andrew Tomkins。 “Pig Latin：並不是很難懂的數據處理語言”。 *SIGMOD* (2008)。

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 簡介][introduction]
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Azure Cosmos DB 一致性級別][consistency-levels]

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
