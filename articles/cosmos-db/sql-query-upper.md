---
title: Azure Cosmos DB 查詢語言的上限
description: 深入瞭解 SQL 系統函數上限 Azure Cosmos DB。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9809a138a5e28eb069d545d39cfda815c915bd78
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728883"
---
# <a name="upper-azure-cosmos-db"></a>上限（Azure Cosmos DB）
 傳回將小寫字元資料轉換成大寫之後的字串運算式。  

UPPER 系統函數不會使用索引。 如果您打算進行不區分大小寫的比較，則大寫系統函數可能會耗用大量的 RU。 如果是這種情況，您可以在插入時將大小寫正規化，而不是在每次進行比較時使用上層系統函數來將資料標準化。 然後查詢（例如 SELECT * FROM c，其中 UPPER （c. name） = ' BOB '）只會變成 SELECT * FROM c，其中 c.name = ' BOB '。

## <a name="syntax"></a>語法
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   這是字串運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。  
  
## <a name="examples"></a>範例
  
  下列範例顯示如何在查詢中使用 `UPPER`  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 以下為結果集。  
  
```json
[{"upper": "ABC"}]  
```

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
