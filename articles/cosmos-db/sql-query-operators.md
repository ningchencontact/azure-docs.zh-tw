---
title: 適用於 Azure Cosmos DB SQL 查詢運算子
description: 深入了解 Azure Cosmos DB 的 SQL 運算子。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: eecc1522f8c260286c7dd7fc4c2e58d5d8caa8fb
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342776"
---
# <a name="operators-in-azure-cosmos-db"></a>Azure Cosmos DB 中的運算子

本文詳細說明 Azure Cosmos DB 所支援的各種運算子。

## <a name="equality-and-comparison-operators"></a>相等和比較運算子

下表顯示 SQL API 中任何兩個 JSON 類型之間的相等比較結果。

| **Op** | **未定義** | **Null** | **布林值** | **Number** | **String** | **物件** | **Array** |
|---|---|---|---|---|---|---|---|
| **未定義** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **布林值** | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined |
| **Number** | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined |
| **String** | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined |
| **物件** | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined |
| **Array** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** |

比較運算子，例如`>`， `>=`， `!=`， `<`，並`<=`，比較類型，或兩個物件或陣列產生`Undefined`。  

純量運算式的結果是否`Undefined`，在結果中，不包含的項目，因為`Undefined`不等於`true`。

## <a name="logical-and-or-and-not-operators"></a>邏輯 (AND、OR 和 NOT) 運算子

邏輯運算子的運算對象是布林值。 下表顯示這些運算子的邏輯真值表：

**OR 運算子**

| 或 | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Undefined |
| Undefined |True |Undefined |Undefined |

**AND 運算子**

| AND | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |False |Undefined |
| False |False |False |False |
| Undefined |Undefined |False |Undefined |

**NOT 運算子**

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Undefined |Undefined |


## <a name="-operator"></a>* 運算子

特殊運算子 * 專案整個項目，因為是。 使用時，它必須是唯一投射的欄位。 查詢喜歡`SELECT * FROM Families f`有效，但`SELECT VALUE * FROM Families f`和`SELECT *, f.id FROM Families f`無效。

## <a name="-and--operators"></a>? 和?? 運算子

您可以使用三元 （？） 和聯合 （？） 運算子來建立條件運算式，如所示的程式語言，例如C#和 JavaScript。 

您可以使用？ 建構新的 JSON 屬性，即時的運算子。 比方說，下列查詢會分類成的成績等級`elementary`或`other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

您也可以巢狀呼叫嗎？ 「 運算子 」，如下列查詢所示： 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

如同其他查詢運算子，嗎？ 如果參考的屬性遺失或是要比較的類型不同，運算子會排除項目。

使用?? 針對半結構化或混合類型的資料查詢時，有效率地檢查屬性的項目中的運算子。 例如，下列查詢會傳回`lastName`如果有的話，或是`surname`如果`lastName`不存在。

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [關鍵字](sql-query-keywords.md)
- [SELECT 子句](sql-query-select.md)
