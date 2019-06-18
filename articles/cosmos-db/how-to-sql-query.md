---
title: 適用於 Azure Cosmos DB 的 SQL 查詢
description: 了解 Azure Cosmos DB 的 SQL 語法、資料庫概念及 SQL 查詢。 使用 SQL 作為 Azure Cosmos DB JSON 查詢語言。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: mjbrown
ms.openlocfilehash: 943ed63aed0f64ae6cbd62c52731c6ec73ddd0bd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388489"
---
# <a name="sql-query-examples-for-azure-cosmos-db"></a>Azure Cosmos DB 的 SQL 查詢範例

Azure Cosmos DB SQL API 帳戶支援使用結構化查詢語言 (SQL) 作為 JSON 查詢語言的查詢項目。 Azure Cosmos DB 查詢語言的設計目的是要：

* 支援 SQL，其中一種最熟悉且熱門查詢語言，而不是發明新的查詢語言。 SQL 進行豐富的查詢提供正式的程式設計模型，透過 JSON 項目。  

* 使用 JavaScript 的程式設計模型做為基礎的查詢語言。 JavaScript 的類型系統、 運算式評估和函式引動過程是 SQL API 的根目錄。 這些根目錄提供自然程式設計模型功能，像是關聯式投射、 跨 JSON 項目，階層式導覽自我聯結、 空間查詢，以及叫用的使用者定義函數 (Udf) 完全以 JavaScript 撰寫。

這篇文章會引導您執行一些範例 SQL 查詢，在簡單的 JSON 項目。 若要深入了解 Azure Cosmos DB SQL 語言語法，請參閱[SQL 語法參考](sql-api-query-reference.md)。

## <a id="GettingStarted"></a>開始使用 SQL 查詢

在您的 Cosmos DB SQL API 帳戶，建立容器，稱為`Families`。 在容器中，建立兩個簡單的 JSON 項目，並對它們執行一些簡單的查詢。

### <a name="create-json-items"></a>建立 JSON 項目

下列程式碼會建立兩個簡單的 JSON 項目，有關系列。 Andersen 和另一個給 Wakefield 家族的簡單 JSON 項目包含父代、 子系和其寵物、 位址和註冊資訊。 第一個項目具有字串、 數字、 布林值、 陣列和巢狀的屬性。


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

第二個項目會使用`givenName`並`familyName`而不是`firstName`和`lastName`。

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

### <a name="query-the-json-items"></a>查詢 JSON 項目

嘗試一些查詢，對 JSON 資料，以了解一些 Azure Cosmos DB 的 SQL 查詢語言的重要層面。

下列查詢傳回的項目所在`id`欄位相符項目`AndersenFamily`。 因為它是`SELECT *`查詢，查詢的輸出是完整的 JSON 項目。 如需 SELECT 語法的詳細資訊，請參閱[SELECT 陳述式](sql-api-query-reference.md#select-query)。 

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

下列查詢中，會重新格式化 JSON 輸出成不同的圖形。 查詢評估新的 JSON`Family`具有兩個選取的欄位物件`Name`和`City`、 地址的縣 （市） 時的狀態相同。 "NY，NY"會比對此案例。

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

下列查詢會傳回家族中的所有指定之名稱的子系其`id`符合`WakefieldFamily`依據縣 （市） 排序。

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

上述範例會示範 Cosmos DB 查詢語言的數個層面：  

* SQL API 處理 JSON 值，因為它處理樹狀結構形狀的實體，而不是資料列和資料行。 您可以像參考中任意深度，樹狀節點`Node1.Node2.Node3…..Nodem`類似的兩個部分參考`<table>.<column>`ANSI SQL 中。

* 因為查詢語言會使用無結構描述的資料，必須以動態方式繫結的型別系統。 相同的運算式在不同的項目上可能會產生不同的類型。 查詢的結果是有效的 JSON 值，但不一定是固定的結構描述。  

* Azure Cosmos DB 只支援嚴謹的 JSON 項目。 型別系統和運算式會限制為只能處理 JSON 類型。 如需詳細資訊，請參閱 < [JSON 規格](https://www.json.org/)。  

* Cosmos DB 容器是 JSON 項目的無結構描述集合。 內含項目，以隱含方式會擷取關聯的區域內和跨容器項目不是由主索引鍵和外部索引鍵關係。 這項功能是很重要的內部項目聯結在本文稍後討論。

## <a id="SelectClause"></a>SELECT 子句

每個查詢都會包含 SELECT 子句以及選擇性的 FROM 和 WHERE 子句，根據 ANSI SQL 標準。 一般而言，會列舉 FROM 子句中的來源，而 WHERE 子句來擷取 JSON 項目子集在來源上套用篩選。 SELECT 子句，然後投射選取清單中要求的 JSON 值。 如需有關語法的詳細資訊，請參閱 < [SELECT 陳述式](sql-api-query-reference.md#select-query)。

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

## <a id="EscapingReservedKeywords"></a>加上引號的屬性存取子
您可以使用引號括住的屬性運算子 [] 的屬性。 例如， `SELECT c.grade` and `SELECT c["grade"]` 是相等的。 此語法適合用來逸出此屬性，包含空格、 特殊字元，或具有相同名稱的 SQL 關鍵字或保留的字。

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="nested-properties"></a>巢狀屬性

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

## <a name="json-expressions"></a>JSON 運算式

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

## <a id="ValueKeyword"></a>VALUE 關鍵字

VALUE 關鍵字可用來傳回 JSON 值本身。 例如，如下所示的查詢會傳回純量運算式`"Hello World"`而不是`{$1: "Hello World"}`:

```sql
    SELECT VALUE "Hello World"
```

下列查詢會傳回 JSON 值，而不`address`標籤：

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

下列範例示範如何傳回 JSON 基本值 （JSON 樹狀結構的分葉層級）：


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

您可以明確別名在查詢中的值。 如果查詢有兩個具有相同名稱的屬性，使用別名重新命名一或兩個屬性，因此它們來區分投射的結果。

As 關鍵字，可用的別名是選擇性的在下列範例所示，當第二個值投射為`NameInfo`:

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

FROM (`FROM <from_specification>`) 子句是選擇性的除非來源篩選，或稍後在查詢中進行預計。 如需有關語法的詳細資訊，請參閱 <<c0> [ 語法從](sql-api-query-reference.md#bk_from_clause)。 查詢喜歡`SELECT * FROM Families`列舉整個`Families`容器。 您也可以使用特殊識別碼 ROOT 容器，而不是使用容器名稱。

FROM 子句會強制執行每個查詢的下列規則：

* 您可以為容器設定別名，例如 `SELECT f.id FROM Families AS f` 或只是 `SELECT f.id FROM Families f`。 以下`f`別名`Families`。 因為是一個選擇性的關鍵字，別名的識別碼。  

* 別名處理之後，無法繫結原始來源名稱。 例如，`SELECT Families.id FROM Families f`的語法無效因為識別碼`Families`已別名，而且不能再解析。  

* 所有參考的屬性必須是完整名稱，以避免在遵循嚴格的結構描述不存在任何模稜兩可繫結。 例如，`SELECT id FROM Families f`的語法無效因為屬性`id`沒有繫結。

### <a name="get-subitems-by-using-the-from-clause"></a>取得子項目的使用 FROM 子句

FROM 子句可以減少至較小的子集的來源。 若要列舉每個項目樹狀子目錄，則子根目錄可能會變得來源，如下列範例所示：

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

上述查詢陣列做為來源，但您也可以使用物件做為來源。 查詢會視為在來源中的結果中包含任何有效且已定義的 JSON 值。 下列範例會排除`Families`，沒有`address.state`值。

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

選擇性的 WHERE 子句 (`WHERE <filter_condition>`) 指定條件的來源 JSON 項目必須符合將它們包含在結果查詢。 JSON 項目必須指定的條件評估為`true`才能視為結果。 索引層使用 WHERE 子句來判斷可以是部分結果的來源項目的最小的子集。 如需有關語法的詳細資訊，請參閱 < [WHERE 語法](sql-api-query-reference.md#bk_where_clause)。

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

前一個範例已顯示簡單的相等查詢。 SQL API 也支援各種[純量運算式](#scalar-expressions)。 最常用的是二元和一元運算式。 來源 JSON 物件中的屬性參考也是有效的運算式。

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

### <a name="equality-and-comparison-operators"></a>相等和比較運算子

下表顯示 SQL API 中任何兩個 JSON 類型之間的相等比較結果。

| **Op** | **未定義** | **Null** | **布林值** | **Number** | **String** | **物件** | **Array** |
|---|---|---|---|---|---|---|---|
| **未定義** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **布林值** | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined |
| **Number** | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined |
| **String** | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined |
| **物件** | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined |
| **Array** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** |

比較運算子，例如`>`， `>=`， `!=`， `<`，並`<=`，比較類型，或兩個物件或陣列產生`Undefined`。  

純量運算式的結果是否`Undefined`，在結果中，不包含的項目，因為`Undefined`不等於`true`。

### <a name="logical-and-or-and-not-operators"></a>邏輯 (AND、OR 和 NOT) 運算子

邏輯運算子的運算對象是布林值。 下表顯示這些運算子的邏輯真值表：

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

如同 ANSI SQL 中，您可以使用 BETWEEN 關鍵字來表示查詢字串或數值的範圍。 例如，下列查詢會傳回在其中第一個子系的成績等級是 1-5，內含的所有項目。

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

不同於 ANSI sql 中，您也可以使用 BETWEEN 子句在 FROM 子句中，如下列範例所示。

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

在 SQL API 中，不同於 ANSI SQL 中，您可以表示針對混合類型的屬性範圍查詢。 例如，`grade`可能的數字的按讚`5`某些項目和字串，例如`grade4`中其他人。 在這些情況下，和在 JavaScript 中，兩種不同類型之間的比較會導致`Undefined`，因此會略過的項目。

> [!TIP]
> 更快速的查詢執行時間，建立使用範圍索引類型 BETWEEN 子句篩選任何數值屬性或路徑編製索引原則。

## <a name="in-keyword"></a>IN 關鍵字

您可以使用 IN 關鍵字來檢查指定的值是否符合清單中的任何值。 例如，下列查詢會傳回所有的系列項目所在`id`已`WakefieldFamily`或`AndersenFamily`。

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

下列範例會傳回所有項目狀態是任何一個指定的值：

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="-operator"></a>* 運算子

特殊運算子 * 專案整個項目，因為是。 使用時，它必須是唯一投射的欄位。 查詢喜歡`SELECT * FROM Families f`有效，但`SELECT VALUE * FROM Families f`和`SELECT *, f.id FROM Families f`無效。 [這篇文章中第一次查詢](#query-the-json-items)使用 * 運算子。 

## <a name="-and--operators"></a>? 和?? 運算子

您可以使用三元 （？） 和聯合 （？） 運算子來建立條件運算式，如所示的程式語言，例如C#和 JavaScript。 

您可以使用？ 建構新的 JSON 屬性，即時的運算子。 比方說，下列查詢會分類成的成績等級`elementary`或`other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

您也可以巢狀呼叫嗎？ 「 運算子 」，如下列查詢所示： 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

如同其他查詢運算子，嗎？ 如果參考的屬性遺失或是要比較的類型不同，運算子會排除項目。

使用?? 針對半結構化或混合類型的資料查詢時，有效率地檢查屬性的項目中的運算子。 例如，下列查詢會傳回`lastName`如果有的話，或是`surname`如果`lastName`不存在。

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="TopKeyword"></a>TOP 運算子

TOP 關鍵字傳回第一個`N`查詢結果未定義順序的數字。 最佳做法，使用 TOP 與 ORDER BY 子句來限制結果的第一個`N`的已排序的值數目。 結合這些兩個子句是唯一能如預期般指出哪些列頂端的影響。

使用常數值，如下列範例中，或使用參數化的查詢的變數值，您可以使用頂端。 如需詳細資訊，請參閱 <<c0> [ 參數化查詢](#parameterized-queries)一節。

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

ANSI SQL，如同您可以在查詢中包含選擇性的 ORDER BY 子句。 選擇性的 ASC 或 DESC 引數會指定是否要擷取結果按照遞增或遞減順序。 ASC 是預設值。

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

## <a id="OffsetLimitClause"></a>位移 LIMIT 子句

位移的限制是選擇性的子句來略過，則採用多個查詢的值。 位移的計數和限制計數所需位移限制子句中。 目前只能在單一分割區內查詢支援這個子句，跨資料分割查詢不支援它。 

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

SELECT 子句支援純量運算式，例如常數、 算術運算式和邏輯運算式。 下列查詢會使用純量運算式：


```sql
    SELECT ((2 + 11 % 7)-2)/3
```

結果為：

```json
    [{
      "$1": 1.33333
    }]
```

在下列查詢中，純量運算式的結果會是布林值：


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

SQL API 的主要功能是陣列和物件的建立。 前一個範例會建立新的 JSON 物件， `AreFromSameCityState`。 您也可以建構陣列，如下列範例所示：


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

下列 SQL 查詢會使用子查詢中的陣列內的另一個範例。 這所有不同的查詢取得指定陣列中的子系的名稱。

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```


## <a id="Iteration"></a>反覆運算

SQL API 使用 IN 關鍵字，FROM 來源中透過加入新的建構逐一查看 JSON 陣列，提供支援。 在下列範例中：

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

下一個查詢執行反覆運算`children`在`Families`容器。 輸出陣列與之前查詢不同。 此範例會將`children`，並將結果簡化成單一陣列：  

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

您可以篩選進一步比較陣列中的每個個別項目，如下列範例所示：

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

您也可以對陣列反覆運算的結果彙總。 例如，下列查詢會計算所有系列的子系數目：

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

在關聯式資料庫中，在資料表之間的聯結是邏輯的必然結果，設計正規化結構描述。 相較之下，SQL API 使用反正規化的資料模型的無結構描述的項目，也就是邏輯的對等*自我聯結*。

語言支援的語法`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`。 此查詢會傳回一組 tuple`N`值。 每個 Tuple 所擁有的值，都是將所有容器別名在其個別集合上反覆運算所產生的。 換句話說，此查詢會對參與聯結的集合執行完整的交叉乘積。

下列範例示範 JOIN 子句的運作方式。 在下列範例中，結果是空的因為每個項目的來源的交叉乘積，空集合是空的：

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

在下列範例中，聯結是兩個 JSON 物件，根項目之間的交叉乘積`id`而`children`子根目錄之間。 事實上，`children`是因為它會處理單一根節點的陣列不是有效的聯結，`children`陣列。 結果會包含只有兩個結果，因為每個項目的陣列的交叉乘積會產生剛好只有一個項目。

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

FROM 來源的聯結子句是迭代器。 因此，在上述範例中的流程為︰  

1. 展開每個子項目`c`陣列中。
2. 適用於與根項目的交叉乘積`f`每個子項目`c`扁平化的第一個步驟。
3. 最後，專案的根物件`f``id`單獨的屬性。

第一個項目中， `AndersenFamily`，只會包含一個`children`元件，所以結果集包含單一物件。 第二個項目中， `WakefieldFamily`，包含兩個`children`，因此，交叉乘積會產生兩個物件，其中每個`children`項目。 這兩個項目中的根欄位相同，就像您在交叉乘積中預期地一樣。

JOIN 子句中的 「 實際 」 公用程式來形成 tuple 取自很難專案圖形中的交叉乘積。 下列 tuple 可讓使用者選擇整體 tuple 滿足的條件的組合上的篩選器範例。

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

前述範例中的下列擴充功能會執行雙重聯結。 您可以將交叉乘積視為下列虛擬程式碼：

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

`AndersenFamily` 有一個小孩養了一隻寵物，讓交叉乘積會產生一個資料列 (1\*1\*1) 從這一系列。 `WakefieldFamily` 有兩個小孩，只是其中一個對具有寵物清單，但子系有兩隻寵物。 此系列的交叉乘積會產生 1\*1\*2 = 2 的資料列。

在下一個範例中，沒有額外的篩選`pet`，這會排除寵物名稱不是的其中的所有 tuple `Shadow`。 您可以從陣列，篩選的任何元素的元組，建置 tuple 和專案項目的任何組合。

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

SQL API 提供使用者定義函數 (Udf) 支援。 使用純量 Udf，您可以傳入零個或多個引數，並傳回單一引數的結果。 API 會檢查每個引數為合法的 JSON 值。  

API 會擴充 SQL 語法，可支援使用 Udf 的自訂應用程式邏輯。 您可以向 SQL API 註冊 Udf，然後在 SQL 查詢中參考它們。 實際上，UDF 是特別設計來透過查詢進行呼叫的。 因此，Udf 無法存取內容物件，如同其他 JavaScript 類型，例如預存程序和觸發程序。 查詢是唯讀的而且可以在主要或次要複本上執行。 Udf，不同於其他 JavaScript 類型，專為次要複本上執行。

下列範例會註冊 Cosmos DB 資料庫中的項目容器下的 UDF。 此範例會建立的 UDF，其名稱是`REGEX_MATCH`。 它接受兩個 JSON 字串值，`input`並`pattern`，並檢查的第一個相符項目中第二個指定的模式使用 JavaScript 的`string.match()`函式。

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

現在，使用此 UDF 在查詢投影中。 您必須以區分大小寫的前置詞來限定 Udf`udf.`呼叫從查詢中時。

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

您可以使用來限定 UDF`udf.`內篩選條件，如下列範例所示的前置詞：

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

基本上，Udf 是投影和篩選中，您可以使用的有效純量運算式。

若要展開的 Udf，看看另一個範例中使用條件式邏輯：

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

下列範例會執行 UDF:

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

如果參照的屬性的 UDF 參數中未提供的 JSON 值，參數會被視為未定義，而且會略過 UDF 叫用。 同樣地，如果 UDF 的結果是未定義，它不是包含在結果中。

如上述範例所示，Udf 可將 JavaScript 語言的強大整合搭配 SQL API。 Udf 提供豐富的可程式化介面，以執行複雜的程序、 條件式邏輯的內建 JavaScript 執行階段功能協助。 SQL API 的每個來源項目中在目前的位置或 SELECT 子句提供的引數的 Udf 處理階段。 結果會順暢地納入整體執行管線中。 簡言之，Udf 是絕佳的工具，來執行複雜商務邏輯，做為查詢的一部分。

## <a id="Aggregates"></a>彙總函式

彙總函式的一組 SELECT 子句中的值執行計算，並傳回單一值。 例如，下列查詢會傳回內的項目計數`Families`容器：

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

您也可以使用 VALUE 關鍵字來傳回彙總的純量值。 例如，下列查詢會以單一數字傳回值的計數：

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

結果為：

```json
    [ 2 ]
```

您也可以使用篩選器結合彙總。 例如，下列查詢會傳回的位址狀態的項目計數`WA`。

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

結果為：

```json
    [ 1 ]
```

SQL API 支援下列彙總函式。 SUM 和 AVG 處理數字的值，並計數、 最小和最大上數字、 字串、 布林值，以及 null 值的工作。

| 函式 | 描述 |
|-------|-------------|
| COUNT | 以運算式傳回項目的數目。 |
| SUM   | 以運算式傳回所有值的總和。 |
| 最小值   | 以運算式傳回最小值。 |
| 最大值   | 以運算式傳回最大值。 |
| 平均   | 以運算式傳回值的平均。 |

您也可以對陣列反覆運算的結果彙總。 如需詳細資訊，請參閱 <<c0> [ 反覆項目](#Iteration)一節。

> [!NOTE]
> 在 Azure 入口網站的 資料總管 中，彙總查詢可能會彙總只能有一個查詢 頁面上的部分結果。 SDK 會產生所有頁面的單一累計值。 若要執行彙總查詢，使用程式碼，您需要.NET SDK 1.12.0、.net、.NET Core SDK 1.1.0 或 Java SDK 1.9.5 或更高版本。
>

## <a id="BuiltinFunctions"></a>內建函數

Cosmos DB 也支援數個內建函數的一般作業，您可以使用查詢內，例如使用者定義函數 (Udf)。

| 函式群組 | 作業 |
|---------|----------|
| 數學函數 | ABS、CEILING、EXP、FLOOR、LOG、LOG10、POWER、ROUND、SIGN、SQRT、SQUARE、TRUNC、ACOS、ASIN、ATAN、ATN2、COS、COT、DEGREES、PI、RADIANS、SIN、TAN |
| 類型檢查函式 | IS_ARRAY、IS_BOOL、IS_NULL、IS_NUMBER、IS_OBJECT、IS_STRING、IS_DEFINED、IS_PRIMITIVE |
| 字串函數 | CONCAT、CONTAINS、ENDSWITH、INDEX_OF、LEFT、LENGTH、LOWER、LTRIM、REPLACE、REPLICATE、REVERSE、RIGHT、RTRIM、STARTSWITH、SUBSTRING、UPPER |
| 陣列函數 | ARRAY_CONCAT、ARRAY_CONTAINS、ARRAY_LENGTH 和 ARRAY_SLICE |
| 空間函數 | ST_DISTANCE、ST_WITHIN、ST_INTERSECTS、ST_ISVALID、ST_ISVALIDDETAILED |

如果您目前使用的使用者定義的函數 (UDF) 已提供內建函式，相對應的內建函式會加快執行速度且更有效率。

Cosmos DB 函式和 ANSI SQL 函式的主要差異是 Cosmos DB 函數的設計適用於無結構描述和混合的結構描述的資料。 比方說，如果屬性已遺失，或具有非數值例如`unknown`，項目會略過而不是傳回錯誤。

### <a name="mathematical-functions"></a>數學函數

每個數學函式都會執行計算，通常以提供來作為引數的輸入值為基礎，並傳回數值。 以下是支援的內建數學函數資料表。

| 使用量 | 描述 |
|----------|--------|
| ABS (num_expr) | 傳回指定之數值運算式的絕對 (正) 值。 |
| CEILING (num_expr) | 傳回大於或等於指定之數值運算式的最小整數值。 |
| FLOOR (num_expr) | 傳回小於或等於指定之數值運算式的最大整數。 |
| EXP (num_expr) | 傳回指定之數值運算式的指數。 |
| LOG (num_expr, base) | 傳回指定之數值運算式或使用指定的基底的對數的自然對數。 |
| LOG10 (num_expr) | 傳回指定之數值運算式的以 10 為基底的對數值。 |
| ROUND (num_expr) | 傳回數值，四捨五入到最接近的整數值。 |
| TRUNC (num_expr) | 傳回數值，截斷至最接近的整數值。 |
| SQRT (num_expr) | 傳回指定之數值運算式的平方根。 |
| SQUARE (num_expr) | 傳回指定之數值運算式的平方。 |
| POWER (num_expr, num_expr) | 將指定數值運算式的乘冪傳回給指定的值。 |
| SIGN (num_expr) | 傳回指定之數值運算式的正負號值 (-1、0、1)。 |
| ACOS (num_expr) | 傳回角度，以弧度為單位，它的餘弦是指定的數值運算式；也稱為反餘弦值。 |
| ASIN (num_expr) | 傳回角度，以弧度為單位，其正弦函數是指定的數值運算式。 此函數也稱為反正弦值。 |
| ATAN (num_expr) | 傳回角度，以弧度為單位，其正切函數是指定的數值運算式。 此函式也稱為反正切值。 |
| ATN2 (num_expr) | 傳回角度，以弧度為單位，正 x 軸和從原點 (y、x) 點的切線之間，其中 x 和 y 是兩個指定之浮點運算式的值。 |
| COS (num_expr) | 在指定運算式中傳回指定角度的三角餘弦函數，以弧度為單位。 |
| COT (num_expr) | 在指定的數值運算式中傳回指定角度的三角餘切函數，以弧度為單位。 |
| DEGREES (num_expr) | 針對以弧度指定的角度，傳回對應的角度 (以度為單位)。 |
| PI () | 傳回 PI 的常數值。 |
| RADIANS (num_expr) | 當您輸入數值運算式 (以度為單位) 時，傳回弧度。 |
| SIN (num_expr) | 在指定運算式中傳回指定角度的三角正弦函數 (以弧度為單位)。 |
| TAN (num_expr) | 在指定運算式中傳回輸入運算式的正切函數。 |

您可以執行查詢，如下列範例所示：

```sql
    SELECT VALUE ABS(-4)
```

結果如下：

```json
    [4]
```

### <a name="type-checking-functions"></a>類型檢查函式

類型檢查函數可讓您檢查 SQL 查詢中的運算式類型。 若要判斷在即時的項目屬性的型別在變數或未知時，您可以使用類型檢查函式。 以下是支援的內建類型檢查函數的資料表：

| **使用量** | **說明** |
|-----------|------------|
| [IS_ARRAY (expr)](sql-api-query-reference.md#bk_is_array) | 傳回布林值，表示值的類型是否為陣列。 |
| [IS_BOOL (expr)](sql-api-query-reference.md#bk_is_bool) | 傳回布林值，表示值的類型是否為布林值。 |
| [IS_NULL (expr)](sql-api-query-reference.md#bk_is_null) | 傳回布林值，表示值的類型是否為 null。 |
| [IS_NUMBER (expr)](sql-api-query-reference.md#bk_is_number) | 傳回布林值，表示值的類型是否為數字。 |
| [IS_OBJECT (expr)](sql-api-query-reference.md#bk_is_object) | 傳回布林值，表示值的類型是否為 JSON 物件。 |
| [IS_STRING (expr)](sql-api-query-reference.md#bk_is_string) | 傳回布林值，表示值的類型是否為字串。 |
| [IS_DEFINED (expr)](sql-api-query-reference.md#bk_is_defined) | 傳回布林值，表示屬性是否已經指派值。 |
| [IS_PRIMITIVE (expr)](sql-api-query-reference.md#bk_is_primitive) | 傳回布林值，指出值的型別是否為字串、 數字、 布林值或 null。 |

您可以使用這些函式，來執行查詢，如下列範例所示：

```sql
    SELECT VALUE IS_NUMBER(-4)
```

結果如下：

```json
    [true]
```

### <a name="string-functions"></a>字串函數

下列純量函數會對字串輸入值執行作業，並傳回字串、 數值或布林值。 以下是內建字串函數的資料表：

| 使用量 | 描述 |
| --- | --- |
| [LENGTH (str_expr)](sql-api-query-reference.md#bk_length) | 傳回指定字串運算式的字元數目。 |
| [CONCAT (str_expr, str_expr [, str_expr])](sql-api-query-reference.md#bk_concat) | 傳回字串，該字串是串連兩個或多個字串值的結果。 |
| [SUBSTRING (str_expr, num_expr, num_expr)](sql-api-query-reference.md#bk_substring) | 傳回字串運算式的一部分。 |
| [STARTSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_startswith) | 傳回布林值，表示第一個字串運算式是否以第二個字串運算式開頭。 |
| [ENDSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_endswith) | 傳回布林值，表示第一個字串運算式是否以第二個字串運算式結尾。 |
| [CONTAINS (str_expr, str_expr)](sql-api-query-reference.md#bk_contains) | 傳回布林值，表示第一個字串運算式是否包含第二個字串運算式。 |
| [INDEX_OF (str_expr, str_expr)](sql-api-query-reference.md#bk_index_of) | 傳回第二個第一次出現的開始位置字串內第一個指定的字串運算式，則為-1 的運算式，如果找不到字串。 |
| [LEFT (str_expr, num_expr)](sql-api-query-reference.md#bk_left) | 傳回具有指定字元數目的字串左側部分。 |
| [RIGHT (str_expr, num_expr)](sql-api-query-reference.md#bk_right) | 傳回具有指定字元數目的字串右側部分。 |
| [LTRIM (str_expr)](sql-api-query-reference.md#bk_ltrim) | 傳回移除開頭空白之後的字串運算式。 |
| [RTRIM (str_expr)](sql-api-query-reference.md#bk_rtrim) | 傳回截斷所有結尾空白之後的字串運算式。 |
| [LOWER (str_expr)](sql-api-query-reference.md#bk_lower) | 傳回將大寫字元資料轉換成小寫之後的字串運算式。 |
| [UPPER (str_expr)](sql-api-query-reference.md#bk_upper) | 傳回將小寫字元資料轉換成大寫之後的字串運算式。 |
| [REPLACE (str_expr, str_expr, str_expr)](sql-api-query-reference.md#bk_replace) | 使用其他字串值取代指定的字串值的所有項目。 |
| [REPLICATE (str_expr, num_expr)](sql-api-query-reference.md#bk_replicate) | 將字串值重複指定的次數。 |
| [REVERSE (str_expr)](sql-api-query-reference.md#bk_reverse) | 傳回反向順序的字串值。 |

使用這些函式，您可以執行查詢，如下所示，它會傳回家族`id`大寫：

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

或串連字串，例如，在此範例中：

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

您也可以使用在 WHERE 子句來篩選結果，例如在下列範例中的字串函數：

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

下列純量函數會對陣列輸入值執行作業，並傳回數值、 布林值或陣列值。 以下是內建陣列函數的資料表：

| 使用量 | 描述 |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](sql-api-query-reference.md#bk_array_length) |傳回指定陣列運算式的元素數目。 |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](sql-api-query-reference.md#bk_array_concat) |傳回串連兩個或多個陣列值之結果的陣列。 |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](sql-api-query-reference.md#bk_array_contains) |傳回布林值，表示陣列是否包含指定值。 可以指定要進行完整或部分比對。 |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](sql-api-query-reference.md#bk_array_slice) |傳回陣列運算式的一部分。 |

您可以使用陣列函式來操作 JSON 中的陣列。 例如，以下是查詢，傳回所有項目`id`s 其中一個的`parents`是`Robin Wakefield`: 

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

您可以指定部分片段來比對陣列內的元素。 下列查詢會尋找所有的項目`id`具有`parents`具有`givenName`的`Robin`:

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

以下是另一個範例會使用 ARRAY_LENGTH 取得的數字的`children`每個系列：

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

Cosmos DB 支援下列開放地理空間協會 (OGC) 的內建函式的地理空間查詢： 

| 使用量 | 描述 |
| --- | --- |
| ST_DISTANCE (point_expr、point_expr) | 傳回兩個 GeoJSON 之間的距離`Point`， `Polygon`，或`LineString`運算式。 |
| T_WITHIN (point_expr, polygon_expr) | 傳回布林運算式，指出是否第一個 GeoJSON 物件 (`Point`， `Polygon`，或`LineString`) 內的第二個 GeoJSON 物件 (`Point`， `Polygon`，或`LineString`)。 |
| ST_INTERSECTS (spatial_expr, spatial_expr) | 傳回布林運算式，指出是否兩個指定的 GeoJSON 物件 (`Point`， `Polygon`，或`LineString`) 相交。 |
| ST_ISVALID | 傳回布林值，指出是否指定的 GeoJSON `Point`， `Polygon`，或`LineString`運算式是否有效。 |
| ST_ISVALIDDETAILED | 傳回 JSON 值，包含布林值，如果指定的 GeoJSON `Point`， `Polygon`，或`LineString`運算式是否有效，但如果是無效的做為字串值的原因。 |

您可以使用空間函式來執行對空間資料的鄰近性查詢。 例如，以下是查詢，傳回所有使用 ST_DISTANCE 內建函式的指定位置的 30 公里內的系列項目：

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

## <a name="parameterized-queries"></a>參數化的查詢

Cosmos DB 支援查詢使用類似 @ 標記法來表示的參數。 參數化的 SQL 提供穩固的處理和使用者輸入的逸出，並避免意外洩露資料透過 SQL 插入式攻擊。

比方說，您可以在其中撰寫查詢： 接受`lastName`並`address.state`做為參數，並執行的各種值`lastName`和`address.state`根據使用者輸入。

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

然後，您可以傳送到 Cosmos DB 此要求做為參數化 JSON 查詢，如下所示：

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

下列範例會設定 TOP 的引數的參數化查詢： 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

參數值可以是任何有效的 JSON： 字串、 數字、 布林值，null，甚至是陣列或巢狀 JSON。 由於 Cosmos DB 是無結構描述，不會對任何類型驗證參數。

## <a id="JavaScriptIntegration"></a>JavaScript 整合

Azure Cosmos DB 提供一個程式設計模型直接在容器上執行 JavaScript 型應用程式邏輯使用預存程序和觸發程序。 此模型支援：

* 高效能交易式 CRUD 操作和查詢項目，在容器中，藉由 JavaScript 執行階段資料庫引擎內深入整合。
* 控制流程、 變數範圍，並指派和例外狀況處理基本項目與資料庫交易的整合自然模型化。 

如需有關 Azure Cosmos DB JavaScript 整合的詳細資訊，請參閱 < [JavaScript 伺服器端 API](#JavaScriptServerSideApi)一節。

### <a name="operator-evaluation"></a>運算子評估

由於 JSON 資料庫，cosmos DB，來描繪 JavaScript 運算子和評估語意。 Cosmos DB 會嘗試保留 JSON 支援方面的 JavaScript 語意，但在某些情況下的作業評估偏差。

在 SQL API 中，不同於傳統的 SQL 類型的值是通常不知道直到 API 從資料庫擷取的值。 為了有效率地執行查詢，大部分的運算子都有嚴謹的類型需求。

不同於 JavaScript，SQL API 不會執行隱含轉換。 查詢執行個體，例如`SELECT * FROM Person p WHERE p.Age = 21`符合 包含的項目`Age`屬性的值為`21`。 它不符合任何其他項目其`Age`屬性會比對可能是無限的變化，像是`twenty-one`， `021`，或`21.0`。 這對照於 JavaScript，其中字串值隱含轉換成數字，根據運算子，例如： `==`。 此 SQL API 的行為，請務必進行有效率的索引比對。

## <a id="ExecutingSqlQueries"></a>執行 SQL 查詢

任何能發出 HTTP/HTTPS 要求的語言都可以呼叫 Cosmos DB REST API。 Cosmos DB 也會提供.NET、 Node.js、 JavaScript 和 Python 程式設計語言的程式設計程式庫。 所有 REST API 和程式庫都支援透過 SQL、 查詢和.NET SDK 也支援[LINQ 查詢](#Linq)。

下列範例顯示如何建立查詢，並針對 Cosmos DB 資料庫帳戶提交它。

### <a id="RestAPI"></a>REST API

Cosmos DB 提供透過 HTTP 的開放 RESTful 程式設計模型。 資源模型包含一組資源，以資料庫帳戶的 Azure 訂用帳戶佈建。 資料庫帳戶包含一組*資料庫*，其中每一個可以包含多個*容器*，其中又包含*項目*，Udf 和其他資源類型。 每個 Cosmos DB 資源是可利用邏輯和穩定 URI 定址。 一組資源稱為*摘要*。 

與這些資源的基本互動模型是透過 HTTP 指令動詞`GET`， `PUT`， `POST`，和`DELETE`，具有其標準解譯。 使用`POST`若要建立新的資源，請執行預存程序，或發出 Cosmos DB 查詢。 查詢一律是唯讀作業，而且沒有任何副作用。

下列範例會顯示`POST`SQL API 查詢的範例項目。 查詢會對 JSON 中的簡單篩選`name`屬性。 `x-ms-documentdb-isquery`和 內容類型：`application/query+json`標頭表示作業是查詢。 取代`mysqlapicosmosdb.documents.azure.com:443`與 Cosmos DB 帳戶的 URI。

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

接下來，更複雜的查詢會從聯結傳回多個結果：

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

如果查詢的結果無法放入單一頁面中，REST API 會傳回接續 token 透過`x-ms-continuation-token`回應標頭。 用戶端可以藉由在後續的結果中包括標頭分頁結果。 您也可以控制每個透過頁面的結果數目`x-ms-max-item-count`標頭。 

如果查詢的計數等彙總函式，[查詢] 頁面可能透過只有一頁結果會傳回部分彙總的值。 用戶端必須對這些結果來產生最終的結果執行第二層彙總。 例如，對個別頁面，以傳回總計數中傳回的計數加總。

若要管理查詢的資料一致性原則，請使用`x-ms-consistency-level`如同所有的 REST API 要求的標頭。 工作階段一致性，也需要回應的最新`x-ms-session-token`在查詢要求的 cookie 標頭。 所查詢容器的編製索引原則也會影響查詢結果的一致性。 與編製索引原則設定適用於容器的預設值，索引一律具有最新項目內容中，而且查詢結果會符合針對資料所選擇的一致性。 如需詳細資訊，請參閱 < [Azure Cosmos DB 一致性層級][consistency-levels]。

如果在容器上設定的索引原則無法支援指定的查詢，Azure Cosmos DB 伺服器就會傳回 400 「 錯誤要求 」。 此錯誤訊息傳回查詢的明確排除編製索引的路徑。 您可以指定`x-ms-documentdb-query-enable-scan`標頭以允許索引無法使用時，執行掃描的查詢。

您也可以設定查詢執行取得詳細的計量`x-ms-documentdb-populatequerymetrics`標頭`true`。 如需詳細資訊，請參閱[適用於 Azure Cosmos DB 的 SQL 查詢計量](sql-api-query-metrics.md)。

### <a id="DotNetSdk"></a>C# (.NET SDK)

.NET SDK 支援 LINQ 和 SQL 查詢。 下列範例示範如何執行上述的篩選條件查詢，使用.NET:

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

下列範例會比較每個項目，內相等的兩個屬性，並使用匿名投射。

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

下一個範例顯示聯結，透過 LINQ 表示`SelectMany`。

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

.NET 用戶端自動逐一查看查詢結果中的所有頁面`foreach`區塊中使用，如上述範例所示。 中導入的查詢選項[REST API](#RestAPI)一節中也會有.NET SDK 中，使用`FeedOptions`並`FeedResponse`中的類別`CreateDocumentQuery`方法。 您可以使用來控制的頁面數目`MaxItemCount`設定。

您可以建立，以明確地控制分頁`IDocumentQueryable`使用`IQueryable`物件，然後藉由讀取`ResponseContinuationToken`做為傳回的值，並將它們`RequestContinuationToken`在`FeedOptions`。 您可以設定`EnableScanInQuery`啟用掃描時所設定的索引編製原則不支援的查詢。 您可以使用資料分割的容器，`PartitionKey`來針對單一資料分割，執行查詢，雖然 Azure Cosmos DB 可以從自動擷取查詢文字。 您可以使用`EnableCrossPartitionQuery`針對多個分割區執行查詢。

如需使用查詢的多個.NET 範例，請參閱[Azure Cosmos DB.NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)在 GitHub 中。

### <a id="JavaScriptServerSideApi"></a>JavaScript 伺服器端 API

Cosmos DB 會提供一個程式設計模型，直接對容器中，使用預存程序和觸發程序中執行 JavaScript 型應用程式邏輯。 在容器層級註冊的 JavaScript 邏輯接著可以發出指定的容器，包裝在環境 ACID 交易中的項目上的資料庫作業。

下列範例示範如何使用`queryDocuments`在 JavaScript 伺服器 API，可進行查詢，從預存程序和觸發程序內：

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

LINQ 是.NET 的程式設計模型，運算表示為物件資料流的查詢。 Cosmos DB 提供一個用戶端程式庫，可透過協助 JSON 與 .NET 物件之間的轉換，以及 LINQ 查詢子集與 Cosmos DB 查詢的對應，來與 LINQ 互動。

下圖顯示使用 Cosmos DB 支援 LINQ 查詢的架構。 使用 Cosmos DB 用戶端，您可以建立`IQueryable`直接查詢 Cosmos DB 查詢提供者，並將 LINQ 查詢轉譯成 Cosmos DB 查詢的物件。 然後，將查詢傳遞至 Cosmos DB 伺服器，它會擷取一組以 JSON 格式的結果。 JSON 還原序列化程式會將結果轉換成.NET 物件，用戶端上的資料流。

![使用 SQL API 來支援 LINQ 查詢的架構：SQL 語法、JSON 查詢語言、資料庫概念及 SQL 查詢][1]

### <a name="net-and-json-mapping"></a>.NET 和 JSON 對應

.NET 物件和 JSON 項目之間的對應是自然的。 每個資料成員欄位對應至 JSON 物件，其中的欄位名稱對應至*金鑰*物件和值遞迴的部分會對應至*值*物件的一部分。 下列 code map `Family` JSON 項目，類別，然後建立`Family`物件：

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

上述範例會建立下列的 JSON 項目：

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

Cosmos DB 查詢提供者會盡力將 LINQ 查詢對應至 Cosmos DB SQL 查詢。 下列說明假設 LINQ 有基本的熟悉。

查詢提供者型別系統僅支援 JSON 基本類型： 數值、 布林值，字串、 值和 null。 

查詢提供者支援下列的純量運算式：

- 常數值，包括評估查詢時的基本資料類型的常數值。
  
- 參考的物件或陣列元素屬性的屬性/陣列索引運算式。 例如:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- 算術運算式，包括數值和布林值的一般算術運算式。 完整清單，請參閱 < [Azure Cosmos DB SQL 規格](https://go.microsoft.com/fwlink/p/?LinkID=510612)。
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- 字串比較運算式，包括比較字串值以某個常數字串值。  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- 物件/陣列建立運算式，傳回物件的複合值類型或匿名型別或這類物件的陣列。 您可以巢狀處理這些值。
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

### <a id="SupportedLinqOperators"></a>支援的 LINQ 運算子

隨附於 SQL.NET SDK 之 LINQ 提供者支援下列運算子：

- **Select**：投影會轉譯為 SQL SELECT，包括物件建構。
- **Where**：篩選會轉譯為 SQL WHERE，並支援之間的轉譯`&&`， `||`，和`!`SQL 運算子
- **SelectMany**：可讓陣列回溯到 SQL JOIN 子句。 使用鏈結或巢狀運算式來篩選陣列項目。
- **OrderBy**並**OrderByDescending**:會轉譯為 ORDER BY ASC 或 DESC。
- 彙總的 **Count**、**Sum**、**Min**、**Max** 與 **Average** 運算子，以及其非同步對應項 **CountAsync**、**SumAsync**、**MinAsync**、**MaxAsync** 與 **AverageAsync**。
- **CompareTo**：轉譯為範圍比較。 常用的字串，因為它們不在.NET 中比較。
- **Take**：會轉譯為 SQL TOP，來限制查詢結果。
- **數學函式**:支援從.NET 轉譯`Abs`， `Acos`， `Asin`， `Atan`， `Ceiling`， `Cos`， `Exp`， `Floor`， `Log`， `Log10`， `Pow`， `Round`， `Sign`， `Sin`， `Sqrt`， `Tan`，和`Truncate`對等的 SQL 內建函式。
- **字串函數**:支援從.NET 轉譯`Concat`， `Contains`， `Count`， `EndsWith`，`IndexOf`， `Replace`， `Reverse`， `StartsWith`， `SubString`， `ToLower`， `ToUpper`， `TrimEnd`，和`TrimStart`對等的 SQL 內建函式。
- **陣列函數**:支援從.NET 轉譯`Concat`， `Contains`，和`Count`對等的 SQL 內建函式。
- **地理空間擴充程式函式**:支援從虛設常式方法轉譯`Distance`， `IsValid`， `IsValidDetailed`，和`Within`對等的 SQL 內建函式。
- **使用者定義函式延伸模組函數**:支援從 stub 方法轉譯`UserDefinedFunctionProvider.Invoke`的對應的使用者定義函式。
- **其他**：支援的轉譯`Coalesce`和條件式運算子。 可以翻譯`Contains`到字串 CONTAINS、 ARRAY_CONTAINS 或 SQL IN，視內容而定。

### <a name="sql-query-operators"></a>SQL 查詢運算子

下列範例會說明一些標準 LINQ 查詢運算子轉譯成 Cosmos DB 查詢的方式。

#### <a name="select-operator"></a>選取運算子

語法為 `input.Select(x => f(x))`，其中 `f` 是純量運算式。

**選取運算子，範例 1:**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**選取運算子，範例 2:** 

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**選取運算子，範例 3:**

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

**其中運算子，範例 1:**

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
  
**其中運算子，範例 2:**

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

您可以撰寫上述的運算子，以形成功能更強大的查詢。 由於 Cosmos DB 支援巢狀的容器，您可以串連或巢狀組合。

#### <a name="concatenation"></a>串連

語法為 `input(.|.SelectMany())(.Select()|.Where())*`。 串連的查詢可以使用選擇性啟動`SelectMany`查詢，其後接著多個`Select`或`Where`運算子。

**串連，範例 1:**

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

**串連，範例 2:**

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

**串連，範例 3:**

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

**串連，範例 4:**

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

語法是`input.SelectMany(x=>x.Q())`何處`Q`是`Select`， `SelectMany`，或`Where`運算子。

巢狀的查詢適用於外部容器的每個項目內部的查詢。 一個重要的功能是內部的查詢可以參考外部容器，例如自我聯結中的項目欄位。

**巢狀結構，範例 1:**

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

**範例 2 的巢狀：**

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

**範例 3 的巢狀：**

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
- [Javascript 規格](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 
- Graefe Goetz。 [大型資料庫的查詢評估技術](https://dl.acm.org/citation.cfm?id=152611)。 *運算問卷的 ACM* 25，沒有。 2 (1993).
- Graefe，g。「 查詢最佳化的串聯，聯集架構。 」 *IEEE 資料 eng。L。* 18，沒有。 3 (1995).
- Lu，Ooi，Tan。 [平行關聯式資料庫系統中的處理查詢]。 *IEEE Computer Society 按*(1994)。
- Olston、 Christopher、 Benjamin Reed、 Utkarsh Srivastava、 Ravi Kumar、 和 Andrew Tomkins。 「 Pig Latin:不讓外部索引語言進行資料處理 」。 *SIGMOD* (2008)。

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 簡介][introduction]
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Azure Cosmos DB 一致性層級][consistency-levels]

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
