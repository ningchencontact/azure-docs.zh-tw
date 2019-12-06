---
title: Azure Cosmos DB 查詢語言中較低
description: 瞭解 Azure Cosmos DB 中的較低 SQL 系統函數，以在將大寫字元資料轉換成小寫之後傳回字串運算式
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f7cd864d90b8ab17a180a00a7a6d4bb683da9be
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873296"
---
# <a name="lower-azure-cosmos-db"></a>LOWER （Azure Cosmos DB）
 傳回將大寫字元資料轉換成小寫之後的字串運算式。  
  
## <a name="syntax"></a>語法
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*str_expr*  
   這是字串運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回字串運算式。  
  
## <a name="examples"></a>範例
  
  下列範例顯示如何在查詢中使用 `LOWER`。  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 以下為結果集。  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="next-steps"></a>後續步驟

- [字串函數 Azure Cosmos DB](sql-query-string-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
