---
title: Azure Cosmos DB 中的編製索引
description: 了解 Azure Cosmos DB 中編製索引的運作方式。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: ecf53251020ce1b639a5bf8da65f5d31ff699db9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59265690"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>在 Azure Cosmos DB 中編製索引 - 概觀

Azure Cosmos DB 是一個無從驗證結構描述的資料庫，可讓您快速逐一查看應用程式，而不需處理結構描述或索引管理。 根據預設，Azure Cosmos DB 會為容器中的所有項目自動編製索引，而不需要開發人員設定結構描述或編製次要索引。

## <a name="items-as-trees"></a>顯示為樹狀結構的項目

投影為 JSON 文件容器中的項目，並以樹狀結構代表它們，Azure Cosmos DB 會標準化的結構，以及執行個體值項目到統一的概念**動態編碼路徑結構**. 在此表示法，在 JSON 文件，其中包含屬性名稱和其值，每個標籤會變成樹狀結構的節點。 樹狀結構的分葉包含實際的值和中繼節點包含的結構描述資訊。 下圖代表兩個建立的樹狀結構中的項目 （1 和 2） 的 Azure Cosmos 容器：

![針對 Azure Cosmos 容器中兩個不同項目的樹狀結構表示法](./media/index-overview/indexing-as-tree.png)

虛擬根節點會建立為對應至標籤下方的 JSON 文件中的實際節點的父系。 巢狀資料結構會衍生樹狀目錄中的階層。 以數值 (例如，0、1、...) 標示的中繼人造節點可用來表示列舉和陣列索引。

## <a name="index-paths"></a>索引路徑

Azure Cosmos DB 專案中的項目 Azure Cosmos 容器做為 JSON 文件和索引為樹狀結構。 然後，您可以微調的索引原則，此樹狀結構中的路徑。 您可以選擇要在編製索引中包含或排除路徑。 針對事前知道查詢模式的案例，這可改善寫入效能並降低索引儲存體。 若要進一步了解，請參閱[的索引路徑](index-paths.md)。

## <a name="indexing-under-the-hood"></a>編製索引：幕後

適用於 azure Cosmos 資料庫*自動編製索引*資料，其中每個樹狀結構中的路徑會編製索引，除非您設定要排除特定路徑。

Azure Cosmos 資料庫採用反向索引資料結構來儲存每個項目的資訊，並促成適用於查詢的高效率表示法。 在索引樹狀結構是建構所有表示在容器中的個別項目樹狀結構的聯集的文件。 一段時間的索引樹狀結構的成長，因為會加入新項目，或在容器中更新現有的項目。 不同於關聯式資料庫編製索引，Azure Cosmos DB 不會重新啟動從從頭開始編製索引引入新的欄位。 新的項目會新增至現有的索引結構。 

在索引樹狀結構的每個節點會包含標籤和位置的值，稱為索引項目*詞彙*，和項目，稱為識別碼*公佈*。 發佈在大括號中的文章 (例如{1,2}) 在反向的索引圖會對應至項目這類*文件 1*並*Document2*包含指定的標籤值。 一致的方式將結構描述標籤和執行個體值的一個重要含意是，所有項目封裝在大型的索引。 仍在分葉中的執行個體值不會重複出現，它可能會在項目間的不同角色中並具有不同的結構描述標籤，但它是同一個值。 下圖顯示兩個不同項目的反向索引：

![編製索引的背後原理，反向索引](./media/index-overview/inverted-index.png)

> [!NOTE]
> 反向索引的顯示方式類似於在資訊擷取網域的搜尋引擎中使用的編製索引結構。 使用此方法，Azure Cosmos DB 可讓您針對任何項目搜尋資料庫，而不論其結構描述結構為何。

針對標準化的路徑，索引會針對一路從根索引到值的轉送路徑以及值的類型資訊進行編碼。 路徑和值會編碼為提供各種類型的範圍，空間至等編製索引。值編碼是設計來提供唯一值或一組路徑的組合。

## <a name="querying-with-indexes"></a>使用索引進行查詢

反向索引可讓查詢快速識別符合查詢述詞的文件。 藉由將結構描述和路徑而言，統一的執行個體值，反向的索引也是樹狀結構。 因此，索引和結果均可序列化為有效的 JSON 文件，並且在樹狀結構表示法中傳回文件時，以文件本身的形式傳回。 這個方法能夠對結果進行遞迴以執行其他查詢。 下圖說明在點查詢中編製索引的範例：  

![點查詢範例](./media/index-overview/index-point-query.png)

範圍查詢，如*GermanTax*是[使用者定義函式](stored-procedures-triggers-udfs.md#udfs)當中執行的查詢處理。 使用者定義函式是可提供豐富的程式設計邏輯整合到查詢任何已註冊，JavaScript 函式。 下圖說明在範圍查詢中編製索引的範例：

![範圍查詢範例](./media/index-overview/index-range-query.png)

## <a name="next-steps"></a>後續步驟

在下列文章中深入了解編製索引：

- [編製索引原則](index-policy.md)
- [索引類型](index-types.md)
- [索引路徑](index-paths.md)
- [如何管理編製索引原則](how-to-manage-indexing-policy.md)
