---
title: Azure Cosmos DB 查詢語言中的 ACOS
description: 瞭解 Azure Cosmos DB 中的 ACOS （arccosice） SQL 系統函數如何傳回以弧度為單位的角度，其餘弦值為指定的數值運算式
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fd5baa3ffefb7039c40706253e633661a51150f2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873517"
---
# <a name="acos-azure-cosmos-db"></a>ACOS （Azure Cosmos DB）
 傳回角度，以弧度為單位，它的餘弦是指定的數值運算式；也稱為反餘弦值。  
  
## <a name="syntax"></a>語法
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引數
  
*numeric_expr*  
   為數值運算式。  
  
## <a name="return-types"></a>傳回類型
  
  傳回數值運算式。  
  
## <a name="examples"></a>範例
  
  下列範例會傳回-1 的 `ACOS`。  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 以下為結果集。  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>後續步驟

- [數學函數 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
