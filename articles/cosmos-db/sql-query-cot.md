---
title: Azure Cosmos DB 查詢語言中的 COT
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數 COT。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d980319730e69fdcb529272ba1b8fb48d2b5b230
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351240"
---
# <a name="cot-azure-cosmos-db"></a>COT （Azure Cosmos DB）
 在指定的數值運算式中傳回指定角度的三角餘切函數，以弧度為單位。  
  
## <a name="syntax"></a>語法
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會計算指定角度的 `COT`。  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 以下為結果集。  
  
```json
[{"cot": -0.040311998371148884}]  
```  
  

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
