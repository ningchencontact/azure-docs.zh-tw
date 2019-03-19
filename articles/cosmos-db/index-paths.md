---
title: 在 Azure Cosmos DB 中使用索引路徑
description: Azure Cosmos DB 中的索引路徑概觀
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/13/2019
ms.author: mjbrown
ms.openlocfilehash: d0fce763822ded374eab2f70c3f319aba0c89267
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57992826"
---
# <a name="index-paths-in-azure-cosmos-db"></a>Azure Cosmos DB 中的索引路徑

藉由在 Azure Cosmos DB 中使用索引路徑，您將可選擇包含或排除特定路徑的編製索引。 如果您已知道查詢模式，選擇特定路徑將可改善寫入效能並減少索引佔用的儲存空間。 索引路徑的開頭為根 (`/`) 萬用字元運算子，且通常結尾為 `?` 萬用字元運算子。 此模式代表有多個可能的首碼值。 例如，若要處理查詢 `SELECT * FROM Families F WHERE F.familyName = "Andersen"`，您必須在容器的索引原則中包含 `/familyName/?` 的索引路徑。

索引路徑也可以使用 `*`萬用字元運算子來指定路徑首碼底下的遞迴行為。 例如，使用 `/payload/*` 可將 payload 屬性下的所有項目自編製索引作業中排除。

## <a name="common-patterns-for-index-paths"></a>索引路徑的常見模式

以下是指定索引路徑的常見模式：

| **路徑** | **描述/使用案例** |
| ---------- | ------- |
| /          | 集合的預設路徑。 遞迴並套用至整個文件樹狀結構。|
| /prop/?    | 提供如下所示的查詢所需的索引路徑 (具有範圍類型，分別): <br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5 <br><br>SELECT FROM collection c ORDER BY c.prop  |
| /prop/*    | 指定之標籤底下所有路徑的索引路徑。 用于以下查询 <br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop |
| /props/[]/?| 為反覆項目及針對 ["a"、"b"、"c"] 等純量陣列進行之聯結查詢提供服務所需的索引路徑：<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5 |
| /props/[]/subprop/? | 為反覆項目及針對 [{subprop: "a"}, {subprop: "b"}] 等物件陣列進行之聯結查詢提供服務所需的索引路徑：<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value" |
| /prop/subprop/? | 提供的查詢所需的索引路徑 (具有範圍類型，分別):<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5  |

設定自訂的索引路徑時，您必須為由特殊路徑 `/*` 表示的完整項目指定預設的索引編制規則。

## <a name="next-steps"></a>後續步驟

在下列文章中深入了解 Azure Cosmos DB 中的索引編製：

- [索引編製概觀](index-overview.md)
- [Azure Cosmos DB 中的索引編製原則](indexing-policies.md)
- [Azure Cosmos DB 中的索引類型](index-types.md)
