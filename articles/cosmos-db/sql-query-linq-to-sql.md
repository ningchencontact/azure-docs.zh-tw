---
title: Azure Cosmos DB 中的 SQL 轉譯為 LINQ
description: 對應至 Azure Cosmos DB SQL 查詢的 LINQ 查詢。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 057614da8fd29e1208c2788049c5d6d1a985eed5
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342448"
---
# <a name="linq-to-sql-translation"></a>LINQ 至 SQL 轉譯

Azure Cosmos DB 查詢提供者會執行從 LINQ 查詢的最佳對應，在 Cosmos DB SQL 查詢。 下列說明假設 LINQ 有基本的熟悉。

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

## <a id="SupportedLinqOperators"></a>支援的 LINQ 運算子

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

## <a name="examples"></a>範例

下列範例會說明一些標準 LINQ 查詢運算子轉譯成 Cosmos DB 查詢的方式。

### <a name="select-operator"></a>選取運算子

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

### <a name="selectmany-operator"></a>SelectMany 運算子

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

### <a name="where-operator"></a>Where 運算子

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

## <a name="composite-sql-queries"></a>複合 SQL 查詢

您可以撰寫上述的運算子，以形成功能更強大的查詢。 由於 Cosmos DB 支援巢狀的容器，您可以串連或巢狀組合。

### <a name="concatenation"></a>串連

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

### <a name="nesting"></a>巢狀

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


## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [模型文件資料](modeling-data.md)
