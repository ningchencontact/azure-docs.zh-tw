---
title: Azure Cosmos DB 索引編製原則
description: 了解如何設定及變更編製索引原則，自動編製索引和更高的效能，Azure Cosmos DB 中的預設值。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: thweiss
ms.openlocfilehash: 3f19668cc4fb4f4f4a900c157aa79de83ad1b79b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67163733"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB 中編製索引原則

在 Azure Cosmos DB 中，每個容器會有規定如何容器的項目應進行索引編製索引原則。 編製索引原則的新預設值建立的容器索引的每個項目，並強制執行的任何字串或數字，範圍索引的每個屬性，並針對任何 GeoJSON 物件的空間索引類型的點。 這可讓您不需考慮編製索引和索引管理預先取得高效能查詢。

在某些情況下，您可能想要覆寫這個自動行為，使其更符合您的需求。 您可以藉由設定自訂容器的索引編製原則及其*編製索引模式*，和包含或排除*屬性路徑*。

> [!NOTE]
> 更新這篇文章中所述的索引編製原則的方法僅適用於 Azure Cosmos DB 的 SQL （核心） API。

## <a name="indexing-mode"></a>編製索引模式

Azure Cosmos DB 支援兩個編製索引模式：

- **一致**：如果容器的索引編製原則設定為 一致，索引會在當您建立、 更新或刪除項目同步更新。 這表示將會讀取查詢的一致性[為帳戶設定的一致性](consistency-levels.md)。

- **無**：如果容器的索引編製原則設為 None 時，編製索引會有效地停用該容器。 這通常用於容器做為純虛擬的索引鍵-值存放區，而不需要次要索引。 它也可協助加速大量插入作業。

## <a name="including-and-excluding-property-paths"></a>包含和排除的屬性路徑

自訂的編製索引原則可以指定屬性路徑明確包含或排除編製索引。 藉由最佳化編製索引的路徑數目，您可以降低您的容器所使用的儲存體數量，並改善的寫入作業的延遲。 這些路徑會定義[編製索引的 [概觀] 區段中所述的方法](index-overview.md#from-trees-to-property-paths)加上下列各項：

- 開頭為純量值 （字串或數字） 路徑的結尾 `/?`
- 從陣列的項目會一起透過定址`/[]`標記法 (而非`/0`，`/1`等。)
- `/*`萬用字元可以用來比對節點下的任何項目

一次採用相同的範例：

    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }

- `headquarters`的`employees`路徑 `/headquarters/employees/?`
- `locations`'`country`路徑 `/locations/[]/country/?`
- 底下的任何項目路徑`headquarters`是 `/headquarters/*`

當路徑明確包含在索引編製原則時，它也有定義應該套用哪一個索引類型，該路徑，以及每個索引類型，適用於這個索引的資料類型：

| 索引類型 | 允許的目標資料類型 |
| --- | --- |
| 範圍 | 字串或數字 |
| 空間 | 點、 LineString 或多邊形 |

比方說，我們可以在其中包含`/headquarters/employees/?`路徑，並指定所`Range`索引應該套用該路徑上兩個`String`和`Number`值。

### <a name="includeexclude-strategy"></a>包含/排除策略

任何索引編製原則必須包含根路徑`/*`為包含或排除的路徑。

- 包含要選擇性地排除不需要編製索引的路徑的根路徑。 這是建議的方法，因為這可讓 Azure Cosmos DB 主動編製索引的任何新屬性，可能會加入至您的模型。
- 排除根路徑，以選擇性地包含需要編製索引的路徑。

- 路徑規則包含的字元： 英數字元及 _ （底線），您不需要逸出路徑字串周圍雙引號括住 （例如，"/ 路徑 /？"）。 針對與其他特殊字元的路徑，您需要逸出周圍雙引號括住路徑字串 (例如，"/\"路徑 abc\"/？")。 如果您希望特殊字元，在您的路徑，您可以逸出每個路徑，基於安全性考量。 在功能上它不進行任何差異，如果逸出每個路徑與只是具有特殊字元。

請參閱[本節](how-to-manage-indexing-policy.md#indexing-policy-examples)編製索引原則範例。

## <a name="composite-indexes"></a>複合索引

查詢`ORDER BY`兩個或多個屬性需要一個複合的索引。 目前，複合的索引只利用多重`ORDER BY`查詢。 根據預設，因此您應先，未定義任何複合的索引[新增複合的索引](how-to-manage-indexing-policy.md#composite-indexing-policy-examples)視。

定義複合的索引，當您指定：

- 兩個或多個屬性的路徑。 路徑是在哪一個屬性中的序列定義大小事。
- 順序 （遞增或遞減）。

使用複合索引時，會使用下列考量：

- 如果複合的索引路徑不相符的 ORDER BY 子句中的屬性順序，然後複合的索引無法支援的查詢

- 複合的索引路徑 （遞增或遞減） 順序也應符合 ORDER BY 子句中的順序。

- 複合的索引也支援 ORDER BY 子句，以相反順序，在所有路徑上。

請考慮下列的範例，其中屬性定義複合的索引 a、 b 和 c:

| **複合索引**     | **範例`ORDER BY`查詢**      | **索引的支援？** |
| ----------------------- | -------------------------------- | -------------- |
| ```(a asc, b asc)```         | ```ORDER BY  a asc, b asc```        | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  b asc, a asc```        | ```No```             |
| ```(a asc, b asc)```          | ```ORDER BY  a desc, b desc```      | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  a asc, b desc```       | ```No```             |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc, c asc``` | ```Yes```            |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc```        | ```No```            |

您應該自訂您的編製索引原則，因此您可以提供所有必要`ORDER BY`查詢。

## <a name="modifying-the-indexing-policy"></a>修改編製索引原則

可以在任何時候更新容器的索引編製原則[藉由使用 Azure 入口網站或其中一個支援的 Sdk](how-to-manage-indexing-policy.md)。 編製索引原則的更新會觸發從舊索引轉換至新的程式，因此額外儲存空間會不佔用任何空間作業期間） 在線上或就地執行。 舊原則的索引有效率地轉換至新的原則不會影響寫入可用性或容器上佈建的輸送量。 索引轉換是非同步作業，並完成所花費的時間取決於佈建的輸送量、 項目數目和其大小。 

> [!NOTE]
> 重新編製索引正在進行時，查詢可能不會傳回所有符合的結果，而且根本不傳回任何錯誤會。 這表示，查詢結果可能不一致索引轉換完成之前。 可追蹤索引轉換進度[藉由使用其中一個 Sdk](how-to-manage-indexing-policy.md)。

如果新的索引編製原則的模式設定為 一致，沒有其他索引編製原則變更時，可以套用索引轉換正在進行中。 執行索引轉換可以取消編製索引原則的模式設定為 無 （這會立即卸除索引）。

## <a name="indexing-policies-and-ttl"></a>索引編製原則與 TTL

[-存留時間 (TTL) 功能](time-to-live.md)需要編製索引設為 [開啟] 容器中的 作用。 這表示：

- 不可能啟用編製索引模式設為 無的其中一個容器上的 TTL
- 您不可能的索引編製模式設定為 無在容器上啟用 TTL 的位置。

其中沒有屬性的路徑必須要編製索引，但 TTL 是必要的情況下，您可以使用索引編製原則：

- 設定為 一致，編製索引模式和
- 任何包含的路徑，以及
- `/*` 做為唯一的排除路徑。

## <a name="obsolete-attributes"></a>過時的屬性

當使用索引編製原則，您可能會遇到現在已過時的下列屬性：

- `automatic` 布林值會定義編製索引原則的根目錄。 它現在會被忽略，並可以設定為`true`，當您使用此工具需要它。
- `precision` 在包含路徑的索引層級定義的數字。 它現在會被忽略，並可以設定為`-1`，當您使用此工具需要它。
- `hash` 為範圍類型現在已取代的索引類型。

## <a name="next-steps"></a>後續步驟

在下列文章中深入了解編製索引：

- [索引編製概觀](index-overview.md)
- [如何管理編製索引原則](how-to-manage-indexing-policy.md)
