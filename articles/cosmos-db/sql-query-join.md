---
title: 適用於 Azure Cosmos DB 的 SQL JOIN 查詢
description: 適用於 Azure Cosmos DB，以了解加入 SQL 語法。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 408ee11b318143b3128833a741e04dd68f3816ed
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342708"
---
# <a name="joins-in-azure-cosmos-db"></a>Azure Cosmos DB 中的聯結

在關聯式資料庫中，在資料表之間的聯結是邏輯的必然結果，設計正規化結構描述。 相較之下，SQL API 使用反正規化的資料模型的無結構描述的項目，也就是邏輯的對等*自我聯結*。

內部聯結是參與聯結之集的完整交叉乘積。 N 方聯結的結果為一組 N 元素 Tuple，其中 Tuple 中的每個值都與參與聯結的別名集相關聯，而且可以透過參考其他子句中的別名加以存取。

## <a name="syntax"></a>語法

語言支援的語法`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`。 此查詢會傳回一組 tuple`N`值。 每個 Tuple 所擁有的值，都是將所有容器別名在其個別集合上反覆運算所產生的。 

我們來看看下面的 FROM 子句：`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 讓每個來源定義 `input_alias1, input_alias2, …, input_aliasN`。 這個 FROM 子句會傳回一組 N-Tuple (具有 N 個值的 Tuple)。 每個 Tuple 所擁有的值，都是將所有容器別名在其個別集合上反覆運算所產生的。  
  
**範例 1** - 2 個來源  
  
- 讓 `<from_source1>` 為容器範圍並代表集 {A, B, C}。  
  
- 讓 `<from_source2>` 為參照 input_alias1 的文件範圍，並代表以下集：  
  
    {1, 2} 為 `input_alias1 = A,`  
  
    {3} 為 `input_alias1 = B,`  
  
    {4, 5} 為 `input_alias1 = C,`  
  
- FROM 子句 `<from_source1> JOIN <from_source2>` 會產生下列 Tuple：  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**範例 2** - 3 個來源  
  
- 讓 `<from_source1>` 為容器範圍並代表集 {A, B, C}。  
  
- 讓 `<from_source2>` 為參照 `input_alias1` 的文件範圍，並代表以下集：  
  
    {1, 2} 為 `input_alias1 = A,`  
  
    {3} 為 `input_alias1 = B,`  
  
    {4, 5} 為 `input_alias1 = C,`  
  
- 讓 `<from_source3>` 為參照 `input_alias2` 的文件範圍，並代表以下集：  
  
    {100, 200} 為 `input_alias2 = 1,`  
  
    {300} 為 `input_alias2 = 3,`  
  
- FROM 子句 `<from_source1> JOIN <from_source2> JOIN <from_source3>` 會產生下列 Tuple：  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > 缺少 `input_alias1`、`input_alias2` 的其他值 Tuple，其中 `<from_source3>` 並未傳回任何值。  
  
**範例 3** - 3 個來源  
  
- 讓 <from_source1> 為容器範圍並代表集 {A, B, C}。  
  
- 讓 `<from_source1>` 為容器範圍並代表集 {A, B, C}。  
  
- 讓 <from_source2> 為參照 input_alias1 的文件範圍，並代表以下集：  
  
    {1, 2} 為 `input_alias1 = A,`  
  
    {3} 為 `input_alias1 = B,`  
  
    {4, 5} 為 `input_alias1 = C,`  
  
- 讓 `<from_source3>` 將範圍設定為 `input_alias1` 並代表集：  
  
    {100, 200} 為 `input_alias2 = A,`  
  
    {300} 為 `input_alias2 = C,`  
  
- FROM 子句 `<from_source1> JOIN <from_source2> JOIN <from_source3>` 會產生下列 Tuple：  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200),  (C, 4, 300) ,  (C, 5, 300)  
  
  > [!NOTE]
  > 這會在 `<from_source2>` 和 `<from_source3>` 之間產生交叉乘積，因為兩者都只限於相同的 `<from_source1>` 範圍。  這會讓 4 (2x2) Tuple 具備值 A，0 Tuple 具備值 B (1x0) 而 2 (2x1) Tuple 具備值 C。  
  
## <a name="examples"></a>範例

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

## <a name="next-steps"></a>後續步驟

- [開始使用](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [子查詢](sql-query-subquery.md)