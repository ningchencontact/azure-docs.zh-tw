---
title: Azure Cosmos DB 查詢語言中的 COS
description: 瞭解 Azure Cosmos DB 中的余弦（COS） SQL 系統函數如何在指定的運算式中傳回指定角度的三角余弦值（以弧度為單位）
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 05546ef531059e05a1426a288a48446d63d8e5df
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873398"
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
