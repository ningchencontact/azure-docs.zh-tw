---
title: Azure Cosmos DB 查詢語言中的 EXP
description: 瞭解 Azure Cosmos DB 中的 SQL 系統函數 EXP。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 21c7ae63f46f2acd961245c59805220174c106f1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351036"
---
# <a name="exp-azure-cosmos-db"></a>EXP （Azure Cosmos DB）
 傳回指定之數值運算式的指數值。  
  
## <a name="syntax"></a>語法
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="remarks"></a>備註
  
  常數 **e** (2.718281…)，為自然對數的基礎。  
  
  數字的指數是常數**e**的數字乘冪。 例如 EXP(1.0) = e^1.0 = 2.71828182845905 和 EXP(10) = e^10 = 22026.4657948067。  
  
  數字的自然對數之指數為該數字本身：EXP (LOG (n)) = n。 且數字的指數之自然對數為該數字本身：LOG (EXP (n)) = n。  
  
## <a name="examples"></a>範例
  
  下列範例會宣告一個變數，並傳回指定變數 (10) 的指數值。  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 以下為結果集。  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 下列範例會傳回自然對數的指數值 20，以及指數的自然對數 20。 因為這些函式是彼此的反向函數，傳回值以浮點數學計算，四捨五入之後在這兩種情況均為 20。  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 以下為結果集。  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
