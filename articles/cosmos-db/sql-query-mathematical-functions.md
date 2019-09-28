---
title: Azure Cosmos DB 查詢語言中的數學函數
description: 瞭解 Azure Cosmos DB 中的數學 SQL 系統函數。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fe4a84db3f2cbcfc2d9841caf520404afec2a297
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349666"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>數學函式（Azure Cosmos DB）  

每個數學函式都會執行計算，通常以提供來作為引數的輸入值為基礎，並傳回數值。

您可以執行如下列範例所示的查詢：

```sql
    SELECT VALUE ABS(-4)
```

結果如下：

```json
    [4]
```

## <a name="functions"></a>Functions

下列支援的內建數學函數會執行計算（通常是根據輸入引數），並傳回數值運算式。
  
||||  
|-|-|-|  
|[ABS](sql-query-abs.md)|[ACOS](sql-query-acos.md)|[ASIN](sql-query-asin.md)|  
|[ATAN](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[CEILING](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[DEGREES](sql-query-degrees.md)|  
|[EXP](sql-query-exp.md)|[FLOOR](sql-query-floor.md)|[LOG](sql-query-log.md)|  
|[LOG10](sql-query-log10.md)|[PI](sql-query-pi.md)|[POWER](sql-query-power.md)|  
|[RADIANS](sql-query-radians.md)|[RAND](sql-query-rand.md)|[ROUND](sql-query-round.md)|
|[SIGN](sql-query-sign.md)|[SIN](sql-query-sin.md)|[SQRT](sql-query-sqrt.md)|
|[SQUARE](sql-query-square.md)|[TAN](sql-query-tan.md)|[TRUNC](sql-query-trunc.md)||  
  
除了 RAND 以外，所有數學函數都是具決定性的函數。 這表示每次使用一組特定的輸入值來呼叫它們時，它們都會傳回相同的結果。

## <a name="next-steps"></a>後續步驟

- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
- [使用者定義函數](sql-query-udfs.md)
- [總計](sql-query-aggregates.md)
