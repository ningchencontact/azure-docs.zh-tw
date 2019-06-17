---
title: 適用於 Azure Cosmos DB 的 SQL 子查詢
description: 深入了解 SQL 子查詢以及其在 Azure Cosmos DB 中的常見使用案例
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: tisande
ms.openlocfilehash: 68465f4ca195930ce08bb579e68d0227e9c18dd6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242851"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>適用於 Azure Cosmos DB 的 SQL 子查詢範例

子查詢是巢狀於另一個查詢的查詢。 子查詢也稱為內部查詢或內部選取。 包含子查詢的陳述式通常稱為外部查詢。

此文章說明 SQL 子查詢以及其在 Azure Cosmos DB 中的常見使用案例。

## <a name="types-of-subqueries"></a>類型的子查詢

有兩種類型的子查詢：

* **相互關聯**:子查詢會參考來自外部查詢的值。 針對外部查詢處理每個資料列中，子查詢會評估一次。
* **非相互關聯**:獨立於外部查詢之子查詢。 它可以在其本身，而無需依賴外部的查詢上執行。

> [!NOTE]
> Azure Cosmos DB 支援相互關聯的子查詢。

子查詢可以進一步分類為基礎的資料列和它們所傳回的資料行數目。 有三種類型：
* **資料表**：傳回多個資料列以及多個資料行。
* **多重值**:傳回多個資料列和單一資料行。
* **純量**:傳回單一資料列和單一資料行。

Azure Cosmos DB 中的 SQL 查詢一律會傳回單一資料行 （簡單的值或複雜的文件）。 因此，只有多重值和純量的子查詢都適用於 Azure Cosmos DB 中的。 您可以使用多重值子查詢只能在 FROM 子句中，為關聯的運算式。 為純量運算式，在 選取或 WHERE 子句或關聯的運算式之 FROM 子句中，您可以使用純量子查詢。


## <a name="multi-value-subqueries"></a>多重值的子查詢

多重值的子查詢會傳回一組文件，而且一律會使用的 FROM 子句中。 其用途：

* 最佳化聯結運算式。 
* 一次評估昂貴的運算式，並參考多次。

### <a name="optimize-join-expressions"></a>最佳化聯結運算式

多重值的子查詢可以將述詞推送之後選取對多的每個運算式，而不在 WHERE 子句中的所有跨聯結後，以最佳化聯結運算式。

請考量下列查詢：

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

此查詢中，索引會比對任何文件的標記具有名稱"嬰兒公式。 」 它是 nutrient 的項目，以介於 0 到 10 之間的值和量大於 1 的服務項目。 聯結運算式在套用任何篩選條件之前，將會針對每個相符的文件執行交叉乘積的標籤、 使萬物和食譜陣列的所有項目。 

WHERE 子句會再套用篩選述詞，在每個 < c t、 n、 s > tuple。 比方說，如果相符的文件中的三個陣列的每個有 10 個項目，它會展開為 1 x 10 x 10 x 10 （也就是 1,000 個） 的 tuple。 這裡使用的子查詢可幫助聯結的下一個運算式之前篩選出已加入的陣列的項目。

這個查詢相當於前一個，但使用子查詢：

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

假設標記陣列中的只有一個項目符合篩選條件，並使萬物和食譜陣列的五個項目。 聯結運算式則會展開為 1 x 1 x 5 x 5 = 25 項目，而不是在第一個查詢 1,000 個項目。

### <a name="evaluate-once-and-reference-many-times"></a>評估一次，參考次數

子查詢可協助最佳化查詢與昂貴的運算式，例如使用者定義函數 (Udf)、 複雜的字串或算術運算式。 您可以使用子查詢及聯結運算式來評估運算式一次，但參考它的次數。

下列查詢會執行 UDF`GetMaxNutritionValue`兩次：

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

以下是 UDF 只執行一次的對等查詢：

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> 請記住聯結運算式的交叉乘積行為。 如果此 UDF 運算式可評估為未定義，您應該確定聯結運算式一律會產生單一資料列，藉由直接從子查詢，而不是值傳回的物件。
>

以下是會傳回物件，而不是值的類似範例：

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

無法限制為 Udf 方法。 它適用於任何可能相當耗費資源的運算式。 例如，您可以採取相同的方法，以數學函式`avg`:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

### <a name="mimic-join-with-external-reference-data"></a>模擬結合外部參考資料

通常，您可能需要參考靜態的資料不常變更，例如單位的度量單位或國家/地區代碼。 它是不重複的每個文件的這類資料。 避免重複項目會儲存在儲存體，並保持較小的文件大小，以改善寫入效能。 您可以使用子查詢，來模擬 inner 聯結語意與參考資料的集合。

例如，假設這組參考資料：

| **單位** | **名稱**            | **乘數** | **基礎單位** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | Nanogram            | 1.00E-09       | 字母組          |
| µg       | Microgram           | 1.00E-06       | 字母組          |
| mg       | Milligram           | 1.00E-03       | 字母組          |
| g        | 字母組                | 1.00E+00       | 字母組          |
| kg       | Kilogram            | 1.00E+03       | 字母組          |
| mg       | Megagram            | 1.00E+06       | 字母組          |
| Gg       | Gigagram            | 1.00E+09       | 字母組          |
| nJ       | Nanojoule           | 1.00E-09       | Joule         |
| µJ       | Microjoule          | 1.00E-06       | Joule         |
| mJ       | Millijoule          | 1.00E-03       | Joule         |
| J        | Joule               | 1.00E+00       | Joule         |
| kJ       | Kilojoule           | 1.00E+03       | Joule         |
| MJ       | Megajoule           | 1.00E+06       | Joule         |
| GJ       | Gigajoule           | 1.00E+09       | Joule         |
| cal      | 卡路里             | 1.00E+00       | 卡路里       |
| kcal     | 卡路里             | 1.00E+03       | 卡路里       |
| IU       | 國際部隊 |                |               |


下列查詢會模擬聯結此資料，讓您新增的單位名稱的輸出：

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>純量子查詢

純量子查詢運算式是評估為單一值的子查詢。 子查詢投影 （SELECT 子句） 的值為純量子查詢運算式的值。  您可以在其中的純量運算式是有效的許多地方使用純量子查詢運算式。 比方說，您可以使用任何運算式，在這兩個 SELECT 和 WHERE 子句中的純量子查詢。

使用純量子查詢，不一定協助最佳化，不過。 例如，將純量子查詢做為引數傳遞給 「 系統 」 或 「 使用者定義函式所提供的資源單位 (RU) 耗用量或延遲沒有任何好處。

純量子查詢可以進一步分類為：
* 簡單運算式的純量子查詢
* 彙總的純量子查詢

### <a name="simple-expression-scalar-subqueries"></a>簡單運算式的純量子查詢

簡單運算式的純量子查詢是相互關聯的子查詢的 SELECT 子句中不含任何彙總的運算式。 這些子查詢會提供任何最佳化優點，因為編譯器將它們轉換成一個較大的簡單運算式。 內部與外部查詢之間沒有相互關聯的內容。

以下提供一些範例：

**範例 1**

```sql
SELECT 1 AS a, 2 AS b
```

您可以使用簡單運算式的純量子查詢，來重新撰寫此查詢中:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

這兩個查詢會產生以下輸出：

```json
[
  { "a": 1, "b": 2 }
]
```

**範例 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

您可以使用簡單運算式的純量子查詢，來重新撰寫此查詢中:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

查詢的輸出：

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**範例 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

您可以使用簡單運算式的純量子查詢，來重新撰寫此查詢中:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

查詢的輸出：

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>彙總的純量子查詢

彙總的純量子查詢是已在其投影或評估為單一值的篩選器中的彙總函式的子查詢。

**範例 1：**

以下是具有其投影中的單一彙總函式運算式的子查詢：

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

查詢的輸出：

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**範例 2**

以下是具有多個彙總函式運算式的子查詢：

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

查詢的輸出：

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**範例 3**

以下是具有彙總的子查詢投影和篩選條件中的查詢：

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

查詢的輸出：

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

一種撰寫此查詢的更佳方式是加入子查詢，並參考別名中這兩種 SELECT 子查詢和 WHERE 子句。 此查詢會更有效率，因為您需要執行子查詢只能在聯結陳述式，而不是在投射和篩選。

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

#### <a name="exists-expression"></a>EXISTS 運算式

Azure Cosmos DB 支援 EXISTS 運算式。 這是內建於 Azure Cosmos DB SQL API 的彙總純量子查詢。 EXISTS 是採用的子查詢運算式並傳回 true，如果子查詢傳回任何資料列的布林運算式。 否則，它會傳回 false。

由於 Azure Cosmos DB SQL API 不會區分布林運算式和任何其他純量運算式，您可以使用在兩個選取的 EXISTS 和 WHERE 子句。 這是不同於 T-SQL，布林運算式 (例如，EXISTS、 BETWEEN 和 IN) 限於篩選條件。

如果 EXISTS 子查詢傳回單一值，有未定義的持續存在，會評估為 false。 比方說，請考慮下列查詢會評估為 false:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


如果值先前的子查詢中省略關鍵字，查詢會評估為 true:
```sql
SELECT EXISTS (SELECT undefined) 
```

子查詢會選取的清單物件中括住值的清單。 選取的清單中不有任何值，如果子查詢會傳回單一值 '{}'。 這個值會定義，所以 EXISTS 評估為 true。

#### <a name="example-rewriting-arraycontains-and-join-as-exists"></a>範例：重寫 ARRAY_CONTAINS 和聯結與 EXISTS

常見的使用案例的 ARRAY_CONTAINS 是陣列中的項目存在來篩選文件。 在此情況下，我們會檢查看看 tags 陣列是否包含項目，名為 「 橙色 」。

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

您可以重新撰寫相同的查詢使用 EXISTS:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

此外，ARRAY_CONTAINS 可以只檢查值是否為等於在陣列中的任何項目中。 如果您需要更複雜的篩選陣列屬性，可使用聯結。

請考慮下列查詢，它會篩選根據單位和`nutritionValue`陣列中的屬性： 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

針對每個集合中的文件中，交叉乘積會執行其陣列項目。 這項聯結作業就可以在陣列中的屬性上的篩選條件。 不過，此查詢的整體 RU 耗用量將會明顯。 比方說，如果 1,000 份文件中每個陣列具有 100 個項目，它會擴充到 1000 x 100 （也就是 100,000 次） 的 tuple。

使用 EXISTS 可以協助您避免此昂貴的交叉乘積：

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

在此情況下，您篩選 EXISTS 子查詢中的陣列項目。 陣列項目符合篩選條件，如果專案然後和 EXISTS 評估為 true。

您也可以別名 EXISTS 在投射中參考它：

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

查詢的輸出：

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

#### <a name="array-expression"></a>陣列運算式

您可以使用陣列運算式，以做為陣列的查詢結果投影。 您可以使用此運算式只能在 SELECT 子句內的查詢。

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

查詢的輸出：

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

如同其他的子查詢中，陣列運算式的篩選條件可能會有。

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

查詢的輸出：

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

陣列運算式也可以有子查詢的 FROM 子句之後。

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

查詢的輸出：

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>後續步驟

- [SQL 查詢範例](how-to-sql-query.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [模型文件資料](modeling-data.md)
