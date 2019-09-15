---
title: Azure Cosmos DB 中的參數化查詢
description: 瞭解 SQL 參數化查詢
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 45c1344c32e35f60f35ba8ed105e912d92574cce
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003611"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Azure Cosmos DB 中的參數化查詢

Cosmos DB 支援具有以熟悉 @ notation 表示之參數的查詢。 參數化 SQL 提供強大的使用者輸入處理和植入，並可防止透過 SQL 插入的資料意外洩漏。

## <a name="examples"></a>範例

例如，您可以撰寫`lastName`接受和`address.state`做為參數的查詢，並`address.state`根據使用者輸入，對的`lastName`各種值執行。

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

然後，您可以將此要求以參數化 JSON 查詢的形式傳送給 Cosmos DB，如下所示：

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

下列範例會使用參數化查詢來設定 TOP 引數： 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

參數值可以是任何有效的 JSON：字串、數位、布林值、null，甚至是陣列或嵌套 JSON。 因為 Cosmos DB 是無架構，所以不會針對任何類型驗證參數。


## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [模型檔資料](modeling-data.md)
