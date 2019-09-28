---
title: POWER in Azure Cosmos DB 查詢語言
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數電源。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683c53c369f136ad4b917b93e9a92a71072d05e0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349645"
---
# <a name="power-azure-cosmos-db"></a>電源（Azure Cosmos DB）
 將指定之運算式的值傳回給指定的乘冪。  
  
## <a name="syntax"></a>語法
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr1*  
   為數值運算式。  
  
*numeric_expr2*  
   這是引發*numeric_expr1*的乘冪。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例示範如何將乘冪數字提高到 3 (數字立方體)。  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 以下為結果集。  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
