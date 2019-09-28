---
title: Azure Cosmos DB 查詢語言中的 ABS
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數 ABS。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 480c5ffcfaccfac5061e53612a2ee57235cfa626
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348750"
---
# <a name="abs-azure-cosmos-db"></a>ABS （Azure Cosmos DB）
 傳回指定之數值運算式的絕對 (正) 值。  
  
## <a name="syntax"></a>語法
  
```sql
ABS (<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會顯示在三個不同的數位上使用 `ABS` 函數的結果。  
  
```sql
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 以下為結果集。  
  
```json
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
