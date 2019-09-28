---
title: Azure Cosmos DB 查詢語言中舍入
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數 ROUND。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4bad140ea1c6b1c59c8f752bc5336bbd49952e16
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349511"
---
# <a name="round-azure-cosmos-db"></a>ROUND （Azure Cosmos DB）
 傳回數值，四捨五入到最接近的整數值。  
  
## <a name="syntax"></a>語法
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="remarks"></a>備註
  
  執行的舍入運算會遵循從零開始舍入的點距離。 如果輸入是介於兩個整數之間的數值運算式，則結果會是離零最接近的整數值。  
  
  |< numeric_expr >|圓角|
  |-|-|
  |-6.5000|-7|
  |-0.5|-1|
  |0.5|1|
  |6.5000|7||
  
## <a name="examples"></a>範例
  
  下列範例會將以下正數和負數數字四捨五入至最接近的整數。  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  以下為結果集。  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
