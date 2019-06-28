---
title: 使用陣列和 Azure Cosmos DB 中的物件
description: 深入了解陣列和物件建立 SQL 語法適用於 Azure Cosmos DB。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 338f3b51edf38d20a963992e121b7e2dbd0c6873
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342571"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>使用陣列和 Azure Cosmos DB 中的物件

Azure Cosmos DB SQL API 的主要功能是陣列和物件的建立。

## <a name="arrays"></a>陣列

下列範例所示，您可以建構陣列：

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

您也可以使用[陣列運算式](sql-query-subquery.md#array-expression)建構從陣列[子查詢之](sql-query-subquery.md)結果。 這所有不同的查詢取得指定陣列中的子系的名稱。

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>反覆運算

SQL API 還支援用於逐一查看 JSON 陣列，包含新的結構，透過新增[IN 關鍵字](sql-query-keywords.md#in)FROM 來源中。 在下列範例中：

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

## <a name="next-steps"></a>後續步驟

- [開始使用](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [聯結](sql-query-join.md)