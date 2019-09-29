---
title: Azure Cosmos DB 查詢語言的 RAND
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數 RAND。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4a0672000e630c9e06df84d9c2da5cb8b988c05a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349610"
---
# <a name="rand-azure-cosmos-db"></a>RAND （Azure Cosmos DB）
 從 [0，1）傳回隨機產生的數位值。
 
## <a name="syntax"></a>語法
  
```sql
RAND ()  
```  

## <a name="return-types"></a>傳回類型

  傳回數值運算式。

## <a name="remarks"></a>備註

  `RAND` 是不具決定性的函數。 @No__t-0 的重複呼叫不會傳回相同的結果。

## <a name="examples"></a>範例
  
  下列範例會傳回隨機產生的數位值。
  
```sql
SELECT RAND() AS rand 
```  
  
 以下為結果集。  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
