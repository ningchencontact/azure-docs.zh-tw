---
title: Azure Cosmos DB 查詢語言的上限
description: 深入瞭解 SQL 系統函數上限 Azure Cosmos DB。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6b0f025948803a23c5b3c8bb6415c0e111b946b2
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349036"
---
# <a name="upper-azure-cosmos-db"></a>上限（Azure Cosmos DB）
 傳回將小寫字元資料轉換成大寫之後的字串運算式。  
  
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
