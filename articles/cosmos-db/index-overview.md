---
title: Azure Cosmos DB 中的編製索引
description: 了解 Azure Cosmos DB 中編製索引的運作方式。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: thweiss
ms.openlocfilehash: 44706e5ebe2442dcb45dfc45e2c322938cf7dca9
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068650"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Azure Cosmos DB-概觀中編製索引

Azure Cosmos DB 是無從驗證結構描述資料庫，可讓您逐一查看在您的應用程式，而不需要處理結構描述或索引管理。 根據預設，Azure Cosmos DB 自動編製索引的所有項目中的每個屬性您[容器](databases-containers-items.md#azure-cosmos-containers)而不需要定義任何結構描述，或設定次要索引。

本文的目的在於說明 Azure Cosmos DB 資料的索引，以及如何使用索引來改善查詢效能。 建議您瀏覽這一節中，之後再探索如何自訂[編製索引原則](index-policy.md)。

## <a name="from-items-to-trees"></a>從樹狀結構的項目

每次項目儲存在容器中，其內容將投射為 JSON 文件中，則轉換成樹狀結構表示法。 這意思是該項目的每個屬性，取得表示為節點樹狀結構中。 虛擬根節點會建立為父項目的所有第一層級屬性。 分葉節點包含實際的純量值，來擴充項目。

例如，請考慮此項目：

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

它會表示下列樹狀結構為：

![以樹狀結構表示上一個項目](./media/index-overview/item-as-tree.png)

請注意在樹狀目錄中陣列的編碼方式： 在陣列中的每個項目取得加上該陣列內的項目索引的中繼節點 (0、 1 等等。)。

## <a name="from-trees-to-property-paths"></a>從樹狀結構以屬性路徑

為什麼 Azure Cosmos DB 會將項目轉換成樹狀結構的原因是因為它可讓這些樹狀結構內的路徑所參考的屬性。 若要取得屬性的路徑，我們可以周遊樹狀結構從根節點，該屬性，並串連每個周遊節點的標籤。

以下是來自上述範例中項目的每一個屬性的路徑：

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

當寫入項目時，Azure Cosmos DB 有效率地編製索引每個屬性的路徑和其對應的值。

## <a name="index-kinds"></a>索引類型

Azure Cosmos DB 目前支援兩種類型的索引：

**範圍**用於索引類型：

- 等號比較查詢： 

   ```sql SELECT * FROM container c WHERE c.property = 'value'```

- 範圍查詢： 

   ```sql SELECT * FROM container c WHERE c.property > 'value'``` (適用於`>`， `<`， `>=`， `<=`， `!=`)

- `ORDER BY` 查詢：

   ```sql SELECT * FROM container c ORDER BY c.property```

- `JOIN` 查詢： 

   ```sql SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'```

範圍索引可以用於純量值 （字串或數字）。

**空間**用於索引類型：

- 地理空間距離的查詢： 

   ```sql SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40```

- 在查詢中的地理空間： 

   ```sql SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })```

空間索引可正確地格式化[GeoJSON](geospatial.md)物件。 目前支援點、 Linestring 和多邊形。

**複合**用於索引類型：

- `ORDER BY` 在多個屬性上的查詢： 

   ```sql SELECT * FROM container c ORDER BY c.firstName, c.lastName```

## <a name="querying-with-indexes"></a>使用索引進行查詢

擷取時資料編製索引的路徑可以輕鬆處理查詢時，查閱索引。 藉由比對`WHERE`子句的查詢中的索引路徑清單，就可以識別非常快速地符合查詢述詞的項目。

例如，請考慮下列查詢： `SELECT location FROM location IN company.locations WHERE location.country = 'France'`。 查詢述詞 （篩選項目，其中的任何位置都有 「 法國 」 作為其國家/地區） 會比對的路徑下方的紅色反白顯示：

![比對樹狀結構內的特定路徑](./media/index-overview/matching-path.png)

> [!NOTE]
> `ORDER BY`子句，根據單一屬性排序*一律*需要範圍索引，而且如果它所參考的路徑不可以有一個將會失敗。 同樣地，多重`ORDER BY`查詢*一律*需要複合的索引。

## <a name="next-steps"></a>後續步驟

在下列文章中深入了解編製索引：

- [編製索引原則](index-policy.md)
- [如何管理編製索引原則](how-to-manage-indexing-policy.md)
