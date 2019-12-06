---
title: Azure Cosmos DB 的 SQL 查詢運算子
description: 深入瞭解 SQL 運算子，例如 Azure Cosmos DB 支援的等號、比較和邏輯運算子。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: f3efe4bee749f0d3132206ca68a33a60f0e16b81
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870933"
---
# <a name="operators-in-azure-cosmos-db"></a>Azure Cosmos DB 中的運算子

本文詳細說明 Azure Cosmos DB 支援的各種運算子。

## <a name="equality-and-comparison-operators"></a>等號比較運算子

下表顯示 SQL API 中任何兩個 JSON 類型之間的相等比較結果。

| **Op** | **未定義** | **Null** | **布林值** | **Number** | **String** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **未定義** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **布林值** | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined |
| **Number** | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined |
| **String** | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined |
| **Object** | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined |
| **Array** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** |

針對 `>`、`>=`、`!=`、`<`和 `<=`等比較運算子，在不同類型之間或兩個物件或陣列之間的比較會產生 `Undefined`。  

如果純量運算式的結果是 `Undefined`，此專案就不會包含在結果中，因為 `Undefined` 不等於 `true`。

## <a name="logical-and-or-and-not-operators"></a>邏輯 (AND、OR 和 NOT) 運算子

邏輯運算子的運算對象是布林值。 下表顯示這些運算子的邏輯事實資料表：

**OR 運算子**

| 或 | 是 | 否 | Undefined |
| --- | --- | --- | --- |
| 是 |是 |是 |是 |
| 否 |是 |否 |Undefined |
| Undefined |是 |Undefined |Undefined |

**AND 運算子**

| AND | 是 | 否 | Undefined |
| --- | --- | --- | --- |
| 是 |是 |否 |Undefined |
| 否 |否 |否 |否 |
| Undefined |Undefined |否 |Undefined |

**NOT 運算子**

| NOT |  |
| --- | --- |
| 是 |否 |
| 否 |是 |
| Undefined |Undefined |


## <a name="-operator"></a>* 運算子

特殊運算子 * 會將整個專案視為。 使用時，它必須是唯一投射的欄位。 `SELECT * FROM Families f` 之類的查詢有效，但 `SELECT VALUE * FROM Families f` 和 `SELECT *, f.id FROM Families f` 無效。

## <a name="-and--operators"></a>? 還有？ 運算子

您可以使用三元（？）和聯合（？）運算子來建立條件運算式，如同和 JavaScript 之類C#的程式設計語言。 

您可以使用？ 用來即時構造新 JSON 屬性的運算子。 例如，下列查詢會將等級層級分類為 `elementary` 或 `other`：

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

使用？ 運算子，可在查詢半結構化或混合類型資料時，有效率地檢查項目中的屬性。 例如，下列查詢會傳回 `lastName` （如果有的話）或 `surname` （如果 `lastName` 不存在）。

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [關鍵字](sql-query-keywords.md)
- [SELECT 子句](sql-query-select.md)
