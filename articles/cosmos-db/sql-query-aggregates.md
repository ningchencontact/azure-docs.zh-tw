---
title: Azure Cosmos DB 中的彙總函式
description: 適用於 Azure Cosmos DB，以了解 SQL 彙總函式語法。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: a6937e9e811ea8e44eda6f2bcb5d2c7d78db4934
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342710"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Azure Cosmos DB 中的彙總函式

彙總函式的一組 SELECT 子句中的值執行計算，並傳回單一值。 例如，下列查詢會傳回內的項目計數`Families`容器：

## <a name="examples"></a>範例

```sql
    SELECT COUNT(1)
    FROM Families f
```

結果為：

```json
    [{
        "$1": 2
    }]
```

您也可以使用 VALUE 關鍵字來傳回彙總的純量值。 例如，下列查詢會以單一數字傳回值的計數：

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

結果為：

```json
    [ 2 ]
```

您也可以使用篩選器結合彙總。 例如，下列查詢會傳回的位址狀態的項目計數`WA`。

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

結果為：

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>類型的彙總函式

SQL API 支援下列彙總函式。 SUM 和 AVG 處理數字的值，並計數、 最小和最大上數字、 字串、 布林值，以及 null 值的工作。

| 函式 | 描述 |
|-------|-------------|
| COUNT | 以運算式傳回項目的數目。 |
| SUM   | 以運算式傳回所有值的總和。 |
| 最小值   | 以運算式傳回最小值。 |
| 最大值   | 以運算式傳回最大值。 |
| 平均   | 以運算式傳回值的平均。 |

您也可以對陣列反覆運算的結果彙總。

> [!NOTE]
> 在 Azure 入口網站的 資料總管 中，彙總查詢可能會彙總只能有一個查詢 頁面上的部分結果。 SDK 會產生所有頁面的單一累計值。 若要執行彙總查詢，使用程式碼，您需要.NET SDK 1.12.0、.net、.NET Core SDK 1.1.0 或 Java SDK 1.9.5 或更高版本。

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 簡介](introduction.md)
- [系統函式](sql-query-system-functions.md)
- [使用者定義函式](sql-query-udfs.md)