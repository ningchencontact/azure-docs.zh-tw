---
title: Azure Cosmos DB 中的參數化的查詢
description: 深入了解參數化 SQL 查詢
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 2bfc22346c1dd43d7d3c2937ffc286e48ae774d0
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342640"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Azure Cosmos DB 中的參數化的查詢

Cosmos DB 支援查詢使用類似 @ 標記法來表示的參數。 參數化的 SQL 提供穩固的處理和使用者輸入的逸出，並避免意外洩露資料透過 SQL 插入式攻擊。

## <a name="examples"></a>範例

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


## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [模型文件資料](modeling-data.md)
