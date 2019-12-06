---
title: Azure Cosmos DB 查詢語言中的 ASIN
description: 瞭解 Azure Cosmos DB 中的反正弦（ASIN） SQL 系統函數如何傳回以弧度為單位的角度，其正弦值為指定的數值運算式
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3e790eb5ae5eb780637b199a1a65dec2dd02d1bc
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871733"
---
# <a name="asin-azure-cosmos-db"></a>ASIN （Azure Cosmos DB）
 傳回角度，以弧度為單位，其正弦函數是指定的數值運算式。 這也稱為反正弦值。  
  
## <a name="syntax"></a>語法
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回-1 的 `ASIN`。  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 以下為結果集。  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
