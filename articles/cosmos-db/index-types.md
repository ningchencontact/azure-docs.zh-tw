---
title: Azure Cosmos DB 中的索引類型
description: Azure Cosmos DB 中的索引類型概觀
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: 5e7ee7c0bdfd0cff6be182e6d087cc264910e440
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271555"
---
# <a name="index-types-in-azure-cosmos-db"></a>Azure Cosmos DB 中的索引類型

當您設定路徑的編製索引原則時，有多個選項。 您可以為每個路徑指定一或多個編製索引的定義：

- **資料類型：** String、Number、Point、Polygon 或 LineString (每個路徑每個資料類型只能包含一個項目)。

- **索引類型：** 範圍 （相等、 範圍或 ORDER BY 查詢） 或者 （適用於空間查詢） 的空間。

- **精確度：** 針對範圍索引，最大有效位數的值為-1，這也是預設值。

## <a name="index-kind"></a>索引類型

Azure Cosmos DB 支援範圍索引可以設定為字串或數字資料類型，每個路徑，或兩者。

- **範圍索引**支援有效率的相等查詢、 聯結查詢、 範圍查詢 (使用 >，<>、 =、 < =、 ！ =)，和 ORDER BY 查詢。 ORDER BY 查詢，根據預設，也需要最大索引精確度 (-1)。 資料類型可以是 String 或 Number。

- **空間索引**支援有效率的空間 (內部和距離) 查詢。 資料類型可以是 Point、Polygon 或 LineString。 Azure Cosmos DB 針對每個路徑也支援空間索引類型 (可針對 Point、Polygon 或 LineString 資料類型加以指定)。 位於指定路徑的值必須是有效的 GeoJSON 片段，例如 {"type":"Point", "coordinates": [0.0, 10.0]}。 Azure Cosmos DB 支援自動編製 Point、Polygon 及 LineString 資料類型的索引。

以下是範例的範圍、 查詢和空間索引可以用來提供：

| **索引類型** | **描述/使用案例** |
| ---------- | ---------------- |
| 範圍      | 对 /prop/?（或 /）应用范围索引 可用于有效完成下列查询：<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop<br><br>範圍 over /props/ [] /？ (或 / 或 /props/) 可用來有效率地處理下列查詢︰<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5  |
| 空間    | Range over /prop/? (或 /) 可用來有效率地處理下列查詢︰<br><br>SELECT FROM collection c WHERE ST_DISTANCE(c.prop, {"type":"Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type":"Point", ... }) --with indexing on points enabled<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type":"Polygon", ... }, c.prop) --with indexing on polygons enabled. |

## <a name="default-behavior-of-index-kinds"></a>索引類型的預設行為

- 如果沒有 (任何精確度的) 範圍索引，以發出可能需要掃描才能進行查詢的訊號，根據預設，含有範圍運算子 (例如 >=) 的查詢都會傳回錯誤。

- 只要在 REST API 中使用 **x-ms-documentdb-enable-scan** 標頭，或使用 .NET SDK 利用 **EnableScanInQuery** 要求選項，仍然可以在沒有範圍索引的情況下執行範圍查詢。 如果在查詢中有 Azure Cosmos DB 可以使用索引據以篩選的其他任何篩選，就不會傳回任何錯誤。

- 根據預設，如果沒有空間索引或可從索引提供服務的其他篩選，則會針對空間查詢傳回錯誤。 這類查詢可以使用執行為掃描**x-ms-documentdb-啟用-掃描**或是**EnableScanInQuery**。

## <a name="index-precision"></a>索引精確度

> [!NOTE]
> Azure Cosmos 容器支援新的索引配置不再需要自訂索引精確度最大精確度值 (-1) 以外的值。 使用此方法，路徑一律以最大精確度編製索引路徑。 如果您指定精確度值編製索引原則時，在容器上的 CRUD 要求將會以無訊息方式忽略精確度值和容器的回應只包含最大精確度值 (-1)。  依預設，所有新的 Cosmos 容器使用新的索引配置。

- 您可以使用索引精準度進行索引的儲存體額外負荷與查詢效能之間取捨。 對於數字，我們建議使用預設精確度組態-1 （最多）。 因為數字在 JSON 中為 8 個位元組，相當於 8 個位元組的組態。 挑選較低值的精確度，例如 1-7，表示在某個範圍內的值可對應至相同的索引項目。 因此，您可以減少索引儲存空間，但查詢執行可能需要處理更多的項目。 最終將會耗用更多輸送量/RU。

- 索引精確度對於字串範圍有更實際的應用。 因為字串可以是任意長度，索引精確度的選擇可能會影響字串範圍查詢的效能。 它也可能會影響所需的索引儲存體空間數量。 字串範圍索引可設定 1 到 100 之間的索引精確度或 -1 (最大值)。 如果您想要針對字串屬性執行 ORDER BY 查詢，您必須為對應的路徑，將精確度指定為 -1。

- 空間索引針對所有類型 (Points、LineString 及 Polygon) 一律都使用預設的索引精確度。 空間索引的預設索引精確度無法覆寫。

當查詢使用 ORDER BY，但沒有針對所查詢路徑之最大精確度的範圍索引時，Azure Cosmos DB 就會傳回錯誤。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Cosmos DB 中的索引編製，請參閱下列文章：

- [編製索引的概觀](index-overview.md)
- [編製索引原則](indexing-policies.md)
- [索引路徑](index-paths.md)

