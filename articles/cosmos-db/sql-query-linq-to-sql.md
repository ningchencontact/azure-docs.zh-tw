---
title: Azure Cosmos DB 中的 LINQ to SQL 轉譯
description: 將 LINQ 查詢對應至 Azure Cosmos DB SQL 查詢。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: ad6dcf7307955300a781a7a649b6ac76b3c69589
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003550"
---
# <a name="linq-to-sql-translation"></a>LINQ 至 SQL 轉譯

Azure Cosmos DB 查詢提供者會執行從 LINQ 查詢到 Cosmos DB SQL 查詢的最佳工作對應。 下列描述假設對 LINQ 有基本的熟悉度。

查詢提供者類型系統僅支援 JSON 基本類型：數值、布林值、字串和 null。

查詢提供者支援下列純量運算式：

- 常數值，包括在查詢評估時間的基本資料類型常數值。
  
- 屬性/陣列索引運算式，參考物件或陣列元素的屬性。 例如:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- 算術運算式，包括數值和布林值的一般算術運算式。 如需完整清單，請參閱[AZURE COSMOS DB SQL 規格](https://go.microsoft.com/fwlink/p/?LinkID=510612)。
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- 字串比較運算式，其中包括比較字串值與某個常數位串值。  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- 物件/陣列建立運算式，其會傳回復合實數值型別或匿名型別的物件，或這類物件的陣列。 您可以將這些值加以嵌套。
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a id="SupportedLinqOperators"></a>支援的 LINQ 運算子

包含在 SQL .NET SDK 中的 LINQ 提供者支援下列運算子：

- **Select**：投影會轉譯為 SQL SELECT，包括物件結構。
- **Where**：篩選會轉譯為 sql，其中、和支援`&&`在`||`、 `!`和之間轉譯為 sql 運算子
- **SelectMany**：可讓陣列回溯到 SQL JOIN 子句。 使用來連鎖或嵌套運算式，以篩選陣列元素。
- **OrderBy**和**OrderByDescending**：使用 ASC 或 DESC 轉譯成 ORDER BY。
- 彙總的 **Count**、**Sum**、**Min**、**Max** 與 **Average** 運算子，以及其非同步對應項 **CountAsync**、**SumAsync**、**MinAsync**、**MaxAsync** 與 **AverageAsync**。
- **CompareTo**：轉譯為範圍比較。 通常用於字串，因為它們在 .NET 中無法比較。
- **Take**：轉譯為 SQL TOP，以限制查詢的結果。
- **數學函數**：支援來自`Abs`.net、 `Asin` `Atan` 、、、`Ceiling`、 `Cos` 、、`Floor`、、、、、、、、、、、的轉譯`Exp` `Acos` `Log` `Log10` `Pow` `Round`、 `Sign`、 、`Sin`、和`Truncate`對等的 SQL 內建函數。 `Tan` `Sqrt`
- **字串函數**：支援來自`Concat`.net、 `Count` `EndsWith` 、、、`IndexOf`、 `Replace` 、、`StartsWith`、、、、、、、、、、、的轉譯`Reverse` `Contains` `SubString` `ToLower` `ToUpper` `TrimEnd`和`TrimStart`相等的 SQL 內建函數。
- **陣列函數**：支援從 .net `Concat`、 `Contains`和`Count`轉換為對等的 SQL 內建函數。
- **地理空間擴充功能**：`Distance`支援從 stub 方法`IsValid` `IsValidDetailed`、、和`Within`轉換為對等的 SQL 內建函數。
- **使用者定義函數延伸函數**：支援從 stub 方法`UserDefinedFunctionProvider.Invoke`到對應的使用者定義函數的轉譯。
- **其他**：支援和條件`Coalesce`運算子的轉譯。 視內容`Contains`而定，可以轉譯成中的字串 CONTAINS、ARRAY_CONTAINS 或 SQL。

## <a name="examples"></a>範例

下列範例說明一些標準 LINQ 查詢運算子如何轉譯為 Cosmos DB 查詢。

### <a name="select-operator"></a>選取運算子

語法為 `input.Select(x => f(x))`，其中 `f` 是純量運算式。

**選取運算子，範例1：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**選取運算子，範例2：** 

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**選取運算子，範例3：**

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

**Where 運算子，範例1：**

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
  
**Where 運算子，範例2：**

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

您可以撰寫前面的運算子來形成更強大的查詢。 由於 Cosmos DB 支援嵌套的容器，因此您可以串連或嵌套組合。

### <a name="concatenation"></a>串連

語法為 `input(.|.SelectMany())(.Select()|.Where())*`。 串連查詢的開頭可以是選擇性`SelectMany`查詢，後面接著多個`Select` or `Where`運算子。

**串連，範例1：**

- **LINQ Lambda 運算式**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**串連，範例2：**

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

**串連，範例3：**

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

**串連，範例4：**

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

`input.SelectMany(x=>x.Q())`語法為`Select`，其中`Q`是、 `SelectMany`或運算子。`Where`

嵌套的查詢會將內部查詢套用至外部容器的每個元素。 其中一個重要的功能是，內部查詢可以參考外部容器中元素的欄位，例如自我聯結。

**嵌套，範例1：**

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

**嵌套，範例2：**

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

**嵌套，範例3：**

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

- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [模型檔資料](modeling-data.md)
