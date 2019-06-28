---
title: Azure Cosmos DB 中的位移 LIMIT 子句
description: 適用於 Azure Cosmos DB，以了解位移 LIMIT 子句。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 60ac28c80e9f7cc72f4d6005c12cb5f68671341e
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342436"
---
# <a name="offset-limit-clause"></a>位移 LIMIT 子句

位移限制子句是選擇性的子句來略過，則採用多個查詢的值。 位移的計數和限制計數所需位移限制子句中。

ORDER BY 子句搭配使用位移的限制時，結果集藉由略過產生，並對已排序的值。 如果沒有 ORDER BY 子句使用時，它會導致的值決定的順序。

## <a name="syntax"></a>語法
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>引數

- `<offset_amount>`

   指定的查詢結果應該略過的項目數的整數。

- `<limit_amount>`
  
   指定的查詢結果應包含的項目數的整數

## <a name="remarks"></a>備註
  
  限制計數和位移計數所需位移限制子句中。 如果選擇性`ORDER BY`子句使用時，結果集藉由略過已排序的值產生。 否則，查詢會傳回固定的順序的值。 目前只能在單一分割區內查詢支援這個子句，跨資料分割查詢不支援它。

## <a name="examples"></a>範例

例如，以下是會略過第一個值，並傳回第二個值 （以依據居住城市名稱的順序） 的查詢：

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

以下是查詢，會略過第一個值，並傳回第二個值 （而不會排序）：

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
