---
title: Azure Cosmos DB 中的編製索引
description: 了解 Azure Cosmos DB 中編製索引的運作方式。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: thweiss
ms.openlocfilehash: c8e21ea89f3e23709d636ab8af4716bff76d7217
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479277"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Azure Cosmos DB 中編制索引-總覽

Azure Cosmos DB 是不限架構的資料庫, 可讓您反復執行應用程式, 而不需要處理架構或索引管理。 根據預設, Azure Cosmos DB 會自動為您[容器](databases-containers-items.md#azure-cosmos-containers)中的所有專案編制每個屬性的索引, 而不需要定義任何架構或設定次要索引。

本文的目標是要說明 Azure Cosmos DB 如何為數據編制索引, 以及如何使用索引來改善查詢效能。 建議您先完成本節, 再探索如何自訂[索引編制原則](index-policy.md)。

## <a name="from-items-to-trees"></a>從專案到樹狀結構

每次專案儲存在容器時, 其內容都會投射為 JSON 檔, 然後轉換成樹狀結構標記法。 這表示該專案的每個屬性都會以樹狀結構中的節點表示。 虛擬根節點會建立為專案中所有第一層屬性的父系。 分葉節點包含專案所攜帶的實際純量值。

例如, 請考慮下列專案:

    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }

它會以下列樹狀結構來表示:

![以樹狀結構表示的上一個專案](./media/index-overview/item-as-tree.png)

請注意陣列在樹狀結構中的編碼方式: 陣列中的每個專案都會取得一個中繼節點, 並以陣列內該專案的索引 (0、1等等) 標示。

## <a name="from-trees-to-property-paths"></a>從樹狀結構到屬性路徑

Azure Cosmos DB 將專案轉換成樹狀結構的原因, 是因為它允許這些樹狀結構內的路徑參考屬性。 若要取得屬性的路徑, 我們可以將樹狀結構從根節點移至該屬性, 並串連每個已流覽節點的標籤。

以下是上述範例專案中每個屬性的路徑:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

寫入專案時, Azure Cosmos DB 會有效地為每個屬性的路徑和其對應的值編制索引。

## <a name="index-kinds"></a>索引種類

Azure Cosmos DB 目前支援三種類型的索引:

**範圍**索引種類用於:

- 相等查詢:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
    ```

- 範圍查詢:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ``` 
  (適用于`>`、 `<`、 `>=`、 `<=`、 )`!=`

- `ORDER BY`查詢

   ```sql 
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN`查詢

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

範圍索引可以用於純量值 (字串或數位)。

**空間**索引種類用於:

- 地理空間距離查詢: 

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- 查詢中的地理空間: 

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

空間索引可用於格式正確的[GeoJSON](geospatial.md)物件。 目前支援點、Linestring 和多邊形。

**複合**索引種類用於:

- `ORDER BY`多個屬性的查詢: 

   ```sql
   SELECT * FROM container c ORDER BY c.firstName, c.lastName
   ```

## <a name="querying-with-indexes"></a>使用索引進行查詢

編制資料索引時所解壓縮的路徑, 可讓您在處理查詢時輕鬆地查閱索引。 藉由將`WHERE`查詢的子句與已編制索引的路徑清單進行比對, 可以非常快速地識別符合查詢述詞的專案。

例如, 請考慮下列查詢: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`。 查詢述詞 (篩選項目, 其中任何位置都有 "法國" 作為其國家/地區) 會符合下列紅色反白顯示的路徑:

![符合樹狀結構內的特定路徑](./media/index-overview/matching-path.png)

> [!NOTE]
> 依單一屬性排序的 子句一律需要範圍索引,如果它所參考的路徑沒有一個,將會`ORDER BY`失敗。 同樣地, 多`ORDER BY`個查詢*一律*需要複合索引。

## <a name="next-steps"></a>後續步驟

在下列文章中深入了解編製索引：

- [編製索引原則](index-policy.md)
- [如何管理編製索引原則](how-to-manage-indexing-policy.md)
