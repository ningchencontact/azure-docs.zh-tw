---
title: Azure Cosmos DB 的 SQL 查詢運算子
description: 瞭解 Azure Cosmos DB 的 SQL 運算子。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: 899355ad7331a3df8cd5d647a573dc15e3a0bb14
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003388"
---
# <a name="operators-in-azure-cosmos-db"></a>Azure Cosmos DB 中的運算子

本文詳細說明 Azure Cosmos DB 支援的各種運算子。

## <a name="equality-and-comparison-operators"></a>等號比較運算子

下表顯示 SQL API 中任何兩個 JSON 類型之間的相等比較結果。

| **Op** | **未定義** | **Null** | **布林值** | **Number** | **String** | **物件** | **Array** |
|---|---|---|---|---|---|---|---|
| **未定義** | 未定義的 | 未定義的 | 未定義的 | 未定義的 | 未定義的 | 未定義的 | 未定義的 |
| **Null** | 未定義的 | **Ok** | 未定義的 | 未定義的 | 未定義的 | 未定義的 | 未定義的 |
| **布林值** | 未定義的 | 未定義的 | **Ok** | 未定義的 | 未定義的 | 未定義的 | 未定義的 |
| **Number** | 未定義的 | 未定義的 | 未定義的 | **Ok** | 未定義的 | 未定義的 | 未定義的 |
| **String** | 未定義的 | 未定義的 | 未定義的 | 未定義的 | **Ok** | 未定義的 | 未定義的 |
| **物件** | 未定義的 | 未定義的 | 未定義的 | 未定義的 | 未定義的 | **Ok** | 未定義的 |
| **Array** | 未定義的 | 未定義的 | 未定義的 | 未定義的 | 未定義的 | 未定義的 | **Ok** |

`>`對於`Undefined`、 `>=`、 、`!=`和等`<=`比較運算子，會產生不同類型之間或兩個物件或陣列之間的比較。 `<`  

如果純量運算式的結果為， `Undefined`則專案不會包含在結果中，因為`Undefined`不等於`true`。

## <a name="logical-and-or-and-not-operators"></a>邏輯 (AND、OR 和 NOT) 運算子

邏輯運算子的運算對象是布林值。 下表顯示這些運算子的邏輯事實資料表：

**OR 運算子**

| OR | 真 | 偽 | 未定義的 |
| --- | --- | --- | --- |
| 真 |真 |真 |真 |
| 偽 |真 |偽 |未定義的 |
| 未定義的 |真 |未定義的 |未定義的 |

**AND 運算子**

| AND | 真 | 偽 | 未定義的 |
| --- | --- | --- | --- |
| 真 |真 |偽 |未定義的 |
| 偽 |偽 |偽 |偽 |
| 未定義的 |未定義的 |偽 |未定義的 |

**NOT 運算子**

| NOT |  |
| --- | --- |
| 真 |偽 |
| 偽 |真 |
| 未定義的 |未定義的 |


## <a name="-operator"></a>* 運算子

特殊運算子 * 會將整個專案視為。 使用時，它必須是唯一投射的欄位。 之類`SELECT * FROM Families f`的查詢有效，但`SELECT VALUE * FROM Families f`和`SELECT *, f.id FROM Families f`無效。

## <a name="-and--operators"></a>? 還有？ 人員

您可以使用三元（？）和聯合（？）運算子來建立條件運算式，如同和 JavaScript 之類C#的程式設計語言。 

您可以使用？ 用來即時構造新 JSON 屬性的運算子。 例如，下列查詢會將等級層級分類`elementary`為`other`或：

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

您也可以將呼叫嵌套到？ 運算子，如下列查詢所示： 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

就像其他查詢運算子一樣，？ 如果遺漏參考的屬性，或者要比較的類型不同，則運算子會排除專案。

使用？ 運算子，可在查詢半結構化或混合類型資料時，有效率地檢查項目中的屬性。 例如，下列查詢`lastName`會傳回`surname` （如果有的話）， `lastName`如果不存在，則傳回。

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [關鍵字](sql-query-keywords.md)
- [SELECT 子句](sql-query-select.md)
