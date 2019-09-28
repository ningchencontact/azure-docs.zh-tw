---
title: Azure Cosmos DB 查詢語言中的 COS
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數 COS。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7e64127b7af749af87b90331c3f175957cfa7f09
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351078"
---
# <a name="cos-azure-cosmos-db"></a>COS （Azure Cosmos DB）
 在指定運算式中傳回指定角度的三角餘弦函數，以弧度為單位。  
  
## <a name="syntax"></a>語法
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會計算指定角度的 `COS`。  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 以下為結果集。  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
