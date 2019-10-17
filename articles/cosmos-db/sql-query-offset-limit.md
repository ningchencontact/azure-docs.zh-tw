---
title: Azure Cosmos DB 中的位移限制子句
description: 深入瞭解 Azure Cosmos DB 的位移限制子句。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 7aae56783f83f13b50321c88d69f07d910e589dd
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326881"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的位移限制子句

OFFSET LIMIT 子句是選擇性的子句，可略過查詢中的一些值。 Offset 限制子句中需要有位移計數和限制計數。

當位移限制與 ORDER BY 子句一起使用時，結果集會藉由執行 skip 並採取排序的值來產生。 如果未使用 ORDER BY 子句，則會產生具有決定性的值順序。

## <a name="syntax"></a>語法
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>引數

- `<offset_amount>`

   指定查詢結果應略過的專案整數數目。

- `<limit_amount>`
  
   指定查詢結果應包含的整數專案數

## <a name="remarks"></a>備註
  
  Offset 限制子句中需要有位移計數和限制計數。 如果使用選擇性的 `ORDER BY` 子句，則會藉由跳過已排序的值來產生結果集。 否則，查詢會傳回固定的值順序。 此子句目前僅支援單一分割區內的查詢，跨分割區查詢尚不支援。

## <a name="examples"></a>範例

例如，以下的查詢會略過第一個值，並傳回第二個值（依常駐城市名稱的順序）：

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

結果為：

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

以下是略過第一個值並傳回第二個值的查詢（沒有排序）：

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

結果為：

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>後續步驟

- [開始使用](sql-query-getting-started.md)
- [SELECT 子句](sql-query-select.md)
- [ORDER BY 子句](sql-query-order-by.md)
