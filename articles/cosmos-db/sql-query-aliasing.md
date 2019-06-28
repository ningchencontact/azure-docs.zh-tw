---
title: Azure Cosmos DB 中的別名
description: 深入了解 Azure Cosmos DB SQL 查詢中的別名值
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: e532fb7180af8a21de6ae9a2e4d798abd9e93e7b
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342505"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Azure Cosmos DB 中的別名

您可以明確別名在查詢中的值。 如果查詢有兩個具有相同名稱的屬性，使用別名重新命名一或兩個屬性，因此它們來區分投射的結果。

## <a name="examples"></a>範例

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

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [SELECT 子句](sql-query-select.md)
- [FROM 子句](sql-query-from.md)
