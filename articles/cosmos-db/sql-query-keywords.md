---
title: 適用於 Azure Cosmos DB 的 SQL 關鍵字
description: 適用於 Azure Cosmos DB，以了解 SQL 關鍵字。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: c9024f120e0a55162a1f6dba0cd9cbda97f5eebc
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342852"
---
# <a name="keywords-in-azure-cosmos-db"></a>Azure Cosmos DB 中的關鍵字
這篇文章說明可用於 Azure Cosmos DB SQL 查詢的關鍵字。

## <a name="between"></a>之間

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

## <a name="distinct"></a>DISTINCT

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
## <a name="in"></a> IN

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

SQL API 可讓您[反覆運算 JSON 陣列](sql-query-object-array.md#Iteration)，與透過 in 關鍵字 FROM 來源中加入新的建構。 

## <a name="top"></a>頁首

TOP 關鍵字傳回第一個`N`查詢結果未定義順序的數字。 最佳做法，使用 TOP 與 ORDER BY 子句來限制結果的第一個`N`的已排序的值數目。 結合這些兩個子句是唯一能如預期般指出哪些列頂端的影響。

使用常數值，如下列範例中，或使用參數化的查詢的變數值，您可以使用頂端。

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

## <a name="next-steps"></a>後續步驟

- [開始使用](sql-query-getting-started.md)
- [聯結](sql-query-join.md)
- [子查詢](sql-query-subquery.md)