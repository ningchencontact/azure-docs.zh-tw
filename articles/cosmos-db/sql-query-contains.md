---
title: 包含 Azure Cosmos DB 查詢語言
description: 瞭解 Azure Cosmos DB 中的 CONTAINS SQL 系統函數如何傳回布林值，指出第一個字串運算式是否包含第二個
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 59c0e2628bfde24200bd386adc858ba778790231
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871528"
---
# <a name="contains-azure-cosmos-db"></a>包含（Azure Cosmos DB）
 傳回布林值，表示第一個字串運算式是否包含第二個字串運算式。  
  
## <a name="syntax"></a>語法
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr1*  
   這是要搜尋的字串運算式。  
  
*str_expr2*  
   這是要尋找的字串運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回布林運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會檢查 "abc" 是否包含 "ab"，以及 "abc" 是否包含 "d"。  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 以下為結果集。  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
