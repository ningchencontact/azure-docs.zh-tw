---
title: Azure Cosmos DB 索引編製原則
description: 瞭解如何在 Azure Cosmos DB 中設定和變更自動編制索引和更佳效能的預設索引編制原則。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: thweiss
ms.openlocfilehash: 01e3e1f1c9bffee0604de1260e8e466f5b1d229d
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467867"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB 中的編制索引原則

在 Azure Cosmos DB 中, 每個容器都有索引編制原則, 以指示容器的專案應該如何編制索引。 新建立之容器的預設索引編制原則會編制每個專案的每個屬性的索引、強制執行任何字串或數位的範圍索引, 以及類型為 Point 之任何 GeoJSON 物件的空間索引。 這可讓您取得高查詢效能, 而不需要事先考慮索引編制和索引管理。

在某些情況下, 您可能會想要覆寫此自動行為, 使其更符合您的需求。 您可以藉由設定其*索引模式*來自訂容器的索引編制原則, 並包含或排除*屬性路徑*。

> [!NOTE]
> 本文所述的更新索引編制原則的方法僅適用于 Azure Cosmos DB 的 SQL (核心) API。

## <a name="indexing-mode"></a>索引模式

Azure Cosmos DB 支援兩種編制索引模式:

- **一致**：如果容器的編制索引原則設定為 [一致], 當您建立、更新或刪除專案時, 會同步更新索引。 這表示讀取查詢的一致性會是[為帳戶設定的一致性](consistency-levels.md)。

- **無**：如果容器的索引編制原則設定為 [無], 則會在該容器上有效停用索引編制。 當容器當做單純的索引鍵-值存放區使用, 而不需要次要索引時, 通常會使用此參數。 它也可以協助加速大量插入作業。

## <a name="including-and-excluding-property-paths"></a>包含和排除屬性路徑

自訂索引編制原則可以指定從索引中明確包含或排除的屬性路徑。 藉由優化已編制索引的路徑數目, 您可以降低容器所使用的儲存體數量, 並改善寫入作業的延遲。 這些路徑會依照[[編制索引總覽] 區段中所述的方法](index-overview.md#from-trees-to-property-paths)定義, 並具有下列新增專案:

- 開頭為純量值 (字串或數位) 的路徑結尾為`/?`
- 陣列中的元素會透過`/[]`標記法 ( `/0`而不`/1`是等等) 來定址。
- `/*`萬用字元可用來比對節點底下的任何元素

再次取得相同的範例:

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

- `headquarters`的`employees`路徑為`/headquarters/employees/?`
- `locations` '`country`路徑為`/locations/[]/country/?`
- 下`headquarters`任何專案的路徑為`/headquarters/*`

當路徑明確包含在索引編制原則中時, 它也必須定義哪些索引類型應套用至該路徑, 以及針對每個索引類型, 此索引適用的資料類型:

| 索引類型 | 允許的目標資料類型 |
| --- | --- |
| 範圍 | 字串或數位 |
| 空間 | 點、LineString 或多邊形 |

例如, 我們可以`/headquarters/employees/?`包含路徑, 並指定`Range`應該在該路徑上同時`String`套用和`Number`值的索引。

### <a name="includeexclude-strategy"></a>包含/排除策略

任何索引編制原則都必須包含根路徑`/*`作為包含或排除的路徑。

- 包含根路徑, 以選擇性地排除不需要編制索引的路徑。 這是建議的方法, 因為它可讓 Azure Cosmos DB 主動為任何可能新增至模型的新屬性編制索引。
- 排除根路徑, 以選擇性地包含需要編制索引的路徑。

- 對於包含: 英數位元和 _ (底線) 之一般字元的路徑, 您不需要以雙引號括住路徑字串 (例如, "/path/？")。 針對具有其他特殊字元的路徑, 您需要以雙引號括住路徑字串 (例如, "/\"path-abc\"/？")。 如果您的路徑中預期有特殊字元, 您可以將每個路徑都換成安全。 在功能上, 如果您要將每個路徑與包含特殊字元的路徑進行轉義, 則不會有任何差異。

- 除非將 etag 新增至所包含的索引路徑, 否則系統屬性 "etag" 預設會從索引中排除。

如需索引編制原則的範例, 請參閱[這一節](how-to-manage-indexing-policy.md#indexing-policy-examples)。

## <a name="composite-indexes"></a>複合索引

`ORDER BY`查詢兩個或多個屬性需要複合索引。 目前, 複合索引僅供多`ORDER BY`個查詢使用。 根據預設, 不會定義任何複合索引, 因此您應該視需要[加入複合索引](how-to-manage-indexing-policy.md#composite-indexing-policy-examples)。

定義複合索引時, 您可以指定:

- 兩個或多個屬性路徑。 定義屬性路徑的順序很重要。
- 順序 (遞增或遞減)。

使用複合索引時, 會使用下列考慮:

- 如果複合索引路徑不符合 ORDER BY 子句中的屬性順序, 則複合索引無法支援查詢

- 複合索引路徑的順序 (遞增或遞減) 也應該符合 ORDER BY 子句中的順序。

- 複合索引也支援 ORDER BY 子句, 而且在所有路徑上的順序相反。

請考慮下列範例, 其中的複合索引定義于屬性 a、b 和 c 上:

| **複合索引**     | **範例`ORDER BY`查詢**      | **受索引支援嗎？** |
| ----------------------- | -------------------------------- | -------------- |
| ```(a asc, b asc)```         | ```ORDER BY  a asc, b asc```        | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  b asc, a asc```        | ```No```             |
| ```(a asc, b asc)```          | ```ORDER BY  a desc, b desc```      | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  a asc, b desc```       | ```No```             |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc, c asc``` | ```Yes```            |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc```        | ```No```            |

您應該自訂索引編制原則, 讓您可以服務`ORDER BY`所有必要的查詢。

## <a name="modifying-the-indexing-policy"></a>修改編製索引原則

您可以[使用 Azure 入口網站或其中一個支援的 sdk](how-to-manage-indexing-policy.md), 隨時更新容器的索引編制原則。 索引編制原則的更新會觸發從舊索引轉換到新的作業, 這會在線上和就地執行 (因此作業期間不會耗用額外的儲存空間)。 舊原則的索引會有效率地轉換成新的原則, 而不會影響容器上布建的寫入可用性或輸送量。 索引轉換是非同步作業, 完成所需的時間取決於布建的輸送量、專案數目和其大小。 

> [!NOTE]
> 正在重新編制索引時, 查詢可能不會傳回所有相符的結果, 而且不會傳回任何錯誤。 這表示在索引轉換完成之前, 查詢結果可能不一致。 您可以[使用其中一個 sdk](how-to-manage-indexing-policy.md)來追蹤索引轉換的進度。

如果新的索引編制原則模式設定為 [一致], 則在進行索引轉換時, 不會套用其他索引編制原則變更。 將索引編制原則的模式設定為 [無] (這會立即卸載索引), 即可取消執行中的索引轉換。

## <a name="indexing-policies-and-ttl"></a>編制索引原則和 TTL

生存[時間 (TTL) 功能](time-to-live.md)需要在其開啟的容器上進行索引編制。 這表示：

- 在編制索引模式設定為 None 的容器上, 無法啟用 TTL。
- 在啟用 TTL 的容器上, 無法將索引編制模式設定為 [無]。

針對不需要編制屬性路徑的情況, 但需要 TTL, 您可以搭配使用索引編制原則:

- 編制索引模式設定為 [一致],
- 沒有包含的路徑, 以及
- `/*`做為唯一排除的路徑。

## <a name="obsolete-attributes"></a>過時的屬性

使用索引編制原則時, 您可能會遇到下列已過時的屬性:

- `automatic`這是在索引編制原則的根目錄定義的布林值。 當您使用的工具需要它時, `true`它現在會被忽略, 而且可以設定為。
- `precision`是在包含路徑的索引層級上定義的數位。 當您使用的工具需要它時, `-1`它現在會被忽略, 而且可以設定為。
- `hash`是一種索引類型, 現在已由範圍種類取代。

## <a name="next-steps"></a>後續步驟

在下列文章中深入了解編製索引：

- [索引編製概觀](index-overview.md)
- [如何管理編製索引原則](how-to-manage-indexing-policy.md)
