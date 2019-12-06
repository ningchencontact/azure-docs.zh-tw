---
title: Azure Cosmos DB 查詢語言的 LTRIM
description: 瞭解 Azure Cosmos DB 中的 LTRIM SQL 系統函數，以在移除開頭空白之後傳回字串運算式
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 652204416c201ccca024aff0239fc10dcc3eb105
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870984"
---
# <a name="ltrim-azure-cosmos-db"></a>LTRIM （Azure Cosmos DB）
 傳回移除開頭空白之後的字串運算式。  
  
## <a name="syntax"></a>語法
  
```sql
LTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   這是字串運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。  
  
## <a name="examples"></a>範例
  
  下列範例顯示如何在查詢內使用 `LTRIM`。  
  
```sql
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 以下為結果集。  
  
```json
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
