---
title: Azure Cosmos DB 查詢語言中的 CONCAT
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數 CONCAT。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1e61d61a3d64ca7d7808619159e4dfc8e8b33d68
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351255"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT （Azure Cosmos DB）
 傳回字串，該字串是串連兩個或多個字串值的結果。  
  
## <a name="syntax"></a>語法
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   這是要串連至其他值的字串運算式。 @No__t-0 函數需要至少兩個*str_expr*引數。  
  
## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回指定值的串連字串。  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 以下為結果集。  
  
```json
[{"concat": "abcdef"}]  
```  
  

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
