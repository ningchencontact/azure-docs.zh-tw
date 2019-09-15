---
title: Azure Cosmos DB 中的別名
description: 瞭解 Azure Cosmos DB SQL 查詢中的別名值
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: 3b17cbc7710647b1e1875025a1db1849034ec1dc
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002077"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Azure Cosmos DB 中的別名

您可以在查詢中明確地別名值。 如果查詢有兩個具有相同名稱的屬性，請使用別名來重新命名其中一個或兩個屬性，以便在預測的結果中加以區分。

## <a name="examples"></a>範例

用於別名的 AS 關鍵字是選擇性的，如下列範例所示，將第二個值投射`NameInfo`為：

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

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT 子句](sql-query-select.md)
- [FROM 子句](sql-query-from.md)
