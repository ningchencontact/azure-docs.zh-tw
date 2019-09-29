---
title: 以 Azure Cosmos DB 的查詢語言取代
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數取代。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2cb7d82efd010fd7c3395a4f6a9217370d9e5779
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349593"
---
# <a name="replace-azure-cosmos-db"></a>取代（Azure Cosmos DB）
 使用其他字串值取代指定的字串值的所有項目。  
  
## <a name="syntax"></a>語法
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr1*  
   這是要搜尋的字串運算式。  
  
*str_expr2*  
   這是要尋找的字串運算式。  
  
*str_expr3*  
   這是用來取代*str_expr1*中*str_expr2*出現次數的字串運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。  
  
## <a name="examples"></a>範例
  
  下列範例顯示如何在查詢中使用 `REPLACE`。  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 以下為結果集。  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
