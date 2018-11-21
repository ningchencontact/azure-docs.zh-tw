---
title: Azure Cosmos DB 中的編製索引
description: 了解 Azure Cosmos DB 中編製索引的運作方式。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: c0525cfba16fb61f8388ae4d6a693be3bb71674c
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628377"
---
# <a name="indexing-in-azure-cosmos-db"></a>Azure Cosmos DB 中的編製索引

Azure Cosmos DB 是一個無從驗證結構描述的資料庫，可讓您快速逐一查看應用程式，而不需處理結構描述或索引管理。 根據預設，Azure Cosmos DB 會為容器中的所有項目自動編製索引，而不需要開發人員設定結構描述或編製次要索引。

## <a name="items-as-trees"></a>顯示為樹狀結構的項目

藉由將容器中的項目投影為 JSON 文件，並將它們顯示為樹狀結構，Azure Cosmos DB 會將項目上的結構和執行個體值標準化為 **動態編碼的路徑結構**的統一概念。 在此表示法中，JSON 文件中的每個標籤 (其中包含屬性名稱及其值) 都會變成樹狀結構的節點。 樹狀結構的分葉包含實際值，而中繼節點會包含結構描述資訊。 下圖顯示已針對容器中的兩個項目 (1 和 2) 建立的樹狀結構：

![針對 Azure Cosmos 容器中兩個不同項目的樹狀結構表示法](./media/index-overview/indexing-as-tree.png)

虛擬根節點會建立來作為對應至下方文件中標籤的實際節點父系。 巢狀資料結構會衍生樹狀目錄中的階層。 以數值 (例如，0、1、...) 標示的中繼人造節點可用來表示列舉和陣列索引。

## <a name="index-paths"></a>索引路徑

Azure Cosmos DB 會將項目投影為 JSON 文件，並將索引投影為樹狀結構。 您接著可為樹狀結構中的路徑調整原則。 您可以選擇要在編製索引中包含或排除路徑。 針對事前知道查詢模式的案例，這可改善寫入效能並降低索引儲存體。 如需深入了解，請參閱[索引路徑](index-paths.md)。

## <a name="indexing-under-the-hood"></a>編製索引：背後原理

Azure Cosmos 資料庫會為資料套用自動編製索引，除非您設定要排除特定路徑，否則就會為樹狀結構中的每個路徑編製索引。

Azure Cosmos 資料庫採用反向索引資料結構來儲存每個項目的資訊，並促成適用於查詢的高效率表示法。 索引樹狀結構是一份文件，會以表示容器中個別項目的所有樹狀結構集合聯集來建構。 索引樹狀結構會因為新增項目或在容器中更新現有項目而隨著時間成長。 不同於關聯式資料庫編製索引，Azure Cosmos DB 不會在導入新欄位時從頭重新開始編製索引，而是將新項目新增至現有的結構。 

索引樹狀結構的每個節點都是一個的索引項目 (稱為詞彙)，其中包含標籤和位置值以及項目的識別碼 (稱為告示)。 在反向索引圖中以大括號括起來的告示 (例如 {1,2})，會對應至包含指定標籤值的項目 (例如，文件 1 和文件 2)。 統一處理結構描述標籤和執行個體值的一個重要含意是要將所有項目封裝於大型索引內。 仍在分葉中的執行個體值不會重複出現，它可能會在項目間的不同角色中並具有不同的結構描述標籤，但它是同一個值。 下圖顯示不同項目的反向編製索引：

![編製索引的背後原理，反向索引](./media/index-overview/inverted-index.png)

> [!NOTE]
> 反向索引的顯示方式類似於在資訊擷取網域的搜尋引擎中使用的編製索引結構。 使用此方法，Azure Cosmos DB 可讓您針對任何項目搜尋資料庫，而不論其結構描述結構為何。

針對標準化的路徑，索引會針對一路從根索引到值的轉送路徑以及值的類型資訊進行編碼。 路徑和值都會進行編碼，以提供各種類型的編製索引，例如範圍、空間種類。 值編碼是設計來提供唯一值或一組路徑的組合。

## <a name="querying-with-indexes"></a>使用索引進行查詢

反向索引可讓查詢快速識別符合查詢述詞的文件。 藉由根據路徑統一處理結構描述和執行個體值，反向索引也會是一個樹狀結構。 因此，索引和結果均可序列化為有效的 JSON 文件，並且在樹狀結構表示法中傳回文件時，以文件本身的形式傳回。 這個方法能夠對結果進行遞迴以執行其他查詢。 下圖說明在點查詢中編製索引的範例：  

![點查詢範例](./media/index-overview/index-point-query.png)

針對範圍查詢，GermanTax 是在查詢處理過程中執行的使用者定義函式。 使用者定義函式是任何已註冊的 JavaScript 函式，可提供已整合到查詢的豐富程式設計邏輯。 下圖說明在範圍查詢中編製索引的範例：

![範圍查詢範例](./media/index-overview/index-range-query.png)

## <a name="next-steps"></a>後續步驟

在下列文章中深入了解編製索引：

- [編製索引原則](index-policy.md)
- [索引類型](index-types.md)
- [索引路徑](index-paths.md)
- [如何管理編製索引原則](how-to-manage-indexing-policy.md)