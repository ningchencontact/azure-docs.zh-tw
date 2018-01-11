---
title: "Azure Cosmos DB 編製索引原則 | Microsoft Docs"
description: "了解 Azure Cosmos DB 中編製索引的運作方式。 了解如何設定編製索引原則，以自動編製索引並追求更高的效能。"
keywords: "編製索引運作方式, 自動編製索引, 為資料庫編製索引"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: d5e8f338-605d-4dff-8a61-7505d5fc46d7
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/17/2017
ms.author: arramac
ms.openlocfilehash: b09f5323f0378721412baade9be9926ebd0c171e
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2018
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Azure Cosmos DB 如何為資料編製索引？

根據預設，會為所有 Azure Cosmos DB 資料編製索引。 雖然許多客戶很高興，讓 Azure Cosmos DB 編製索引的所有層面，自動都處理，您可以指定自訂*編製索引原則*Azure Cosmos DB 中的建立期間的集合。 在 Azure Cosmos DB 的檢索原則是更有彈性且功能強大比所提供之其他資料庫平台的次要索引。 在 Azure Cosmos DB 中，您可以設計和自訂索引的圖形，而不必犧牲結構描述彈性。 

深入了解 Azure Cosmos DB 中的索引如何運作，請務必了解當您管理編製索引原則時，您可以進行細部之間的利弊得失索引儲存體的額外負荷、 寫入及查詢的輸送量，以及查詢一致性。  

在本文中，我們需要仔細查看 Azure Cosmos DB 編製索引原則，查看如何以自訂編製索引的原則和相關聯的取捨。 

閱讀本文後，您將能夠回答下列問題：

* 如何覆寫要在索引編製中包含或排除的屬性？
* 我要如何設定最終更新的索引？
* 如何設定編製索引來執行 ORDER BY 或範圍的查詢？
* 如何變更集合的索引編製原則？
* 如何比較不同索引編製原則的儲存空間和效能？

## 自訂集合的編製索引原則<a id="CustomizingIndexingPolicy"></a>  
您可以藉由覆寫的預設檢索原則 Azure Cosmos DB 集合上的自訂儲存體、 寫入及查詢效能和查詢一致性之間的取捨。 您可以設定的下列層面：

* **包含或排除文件和索引的路徑**。 您可以排除，或者插入或取代集合中的文件時，在索引中包含特定的文件。 您也可以包含或排除特定的 JSON 屬性，也稱為*路徑*、 跨文件所包含的索引編製索引。 路徑包含萬用字元模式。
* **設定各種索引類型**。 針對每個包含的路徑，您可以指定此路徑需要集合索引的類型。 您可以指定類型的索引為依據的路徑資料、 預期的查詢工作負載，以及數值/字串"precision"。
* **設定索引的更新模式**。 Azure Cosmos DB 支援三個索引的模式： 一致、 延遲，和 None。 您可以在 Azure Cosmos DB 集合上設定編製索引原則透過索引的模式。 

下列的 Microsoft.NET 程式碼片段示範如何設定自訂的編製索引原則，當您建立的集合。 在此範例中，我們會在最大有效位數設定範圍的索引，如字串與數字的原則。 您可以使用此原則來執行 ORDER BY 查詢字串。

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> 編製索引原則變更與發行的 REST API 版本 2015年-06-03 的 JSON 結構描述。 使用該版本中，檢索原則的 JSON 結構描述支援範圍索引字串。 .NET SDK 1.2.0 及 Java、Python 和 Node.js SDKs 1.1.0 支援新的原則結構描述。 較早版本的 SDK 使用 REST API 2015-04-08 版。 它們支援的舊版結構描述編製索引原則。
> 
> 根據預設，Azure Cosmos DB 編製索引的文件內的所有字串屬性一致雜湊索引。 它以一致的方式具有範圍索引索引文件內的所有數值屬性。  
> 
> 

### <a name="customize-the-indexing-policy-in-the-portal"></a>自訂入口網站中的編製索引原則

您可以變更在 Azure 入口網站集合的檢索原則： 

1. 在入口網站中，移至您的 Azure Cosmos DB 帳戶，然後選取您的集合。 
2. 在左側瀏覽功能表中，選取**設定**，然後選取**編製索引原則**。 
3. 在下**編製索引原則**，變更您的編製索引原則，然後選取**確定**。 

### 資料庫索引模式<a id="indexing-modes"></a>  
Azure Cosmos DB 支援三個索引的模式，您可以透過 Azure Cosmos DB 集合上編製索引的原則設定： 一致、 延遲，和 None。

**一致**： 如果 Azure Cosmos DB 集合的原則一致，特定 Azure Cosmos DB 集合上的查詢，請遵循相同的一致性層級，與指定的點讀取相同 (繫結失效的工作階段或最終)。 文件更新 （插入、 取代、 更新和刪除的 Azure Cosmos DB 集合中的文件） 的過程中同步更新索引。

一致的索引支援一致的查詢，但要付出可能降低寫入輸送量。 這個簡化是需要編製索引的唯一路徑的函式和 「 一致性層級 」。 一致的索引編製模式是針對「快速寫入、立即查詢」工作負載而設計。

**延遲**: Azure Cosmos DB 集合時停止，也就是集合的輸送量容量不完全利用時處理使用者要求以非同步方式更新索引。 延遲的檢索模式可能是適用於需要文件擷取的"內嵌現在，稍後再查詢 」 工作負載。 請注意，因此資料會內嵌緩時變編製索引可能得不一致的結果。 這表示，計數查詢或特定的查詢結果可能不一致或重複任何給定的時間。 

索引通常是在趕上模式下使用內嵌的資料。 Lazy 編製索引，以在即將卸除並重新建立索引的結果變更存留時間 (TTL)。 這可讓計數和查詢結果不一致的一段時間。 因為這個緣故，大部分 Azure Cosmos DB 帳戶應該使用一致的檢索模式。

**無**： 具有無索引模式沒有任何與其相關聯的索引的集合。 這通常會使用如果 Azure Cosmos DB 做為索引鍵-值 」 儲存，且文件存取只能由其 ID 屬性。 

> [!NOTE]
> 編製索引的原則與設定為 無具有副作用的卸除任何現有的索引。 如果存取模式，需要只 ID 或自我連結，請使用此選項。
> 
> 

下表顯示根據針對集合設定的索引編製模式 (「一致」和「延遲」)，以及針對查詢要求指定的一致性層級，顯示查詢的一致性。 這適用於使用任何介面所做的查詢： REST API，Sdk，或從預存程序和觸發程序。 

|一致性|檢索模式： 一致|檢索模式： 延遲|
|---|---|---|
|強式|強式|最終|
|界限-陳舊|界限-陳舊|最終|
|工作階段|工作階段|最終|
|最終|最終|最終|

Azure Cosmos DB 傳回錯誤，而沒有任何索引模式的集合上所做的查詢。 查詢仍然會執行為透過明確的掃描**x ms-documentdb-啟用-掃描**REST API 中的標頭或**EnableScanInQuery**使用.NET SDK 要求選項。 掃描不支援某些查詢功能，例如，ORDER BY **EnableScanInQuery**。

下表顯示的一致性檢索模式 （一致、 Lazy，和無） 為基礎的查詢時**EnableScanInQuery**指定。

|一致性|索引模式︰一致|索引模式：緩慢|索引模式：無|
|---|---|---|---|
|強式|強式|最終|強式|
|界限-陳舊|界限-陳舊|最終|界限-陳舊|
|工作階段|工作階段|最終|工作階段|
|最終|最終|最終|最終|

下列程式碼範例顯示如何建立 Azure Cosmos DB 集合，使用.NET SDK 上所有的文件插入一致編製索引。

     // Default collection creates a Hash index for all string fields and a Range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>索引路徑
Azure Cosmos DB 模型當做樹狀結構的 JSON 文件和索引。 您可以微調原則，在樹狀目錄中的路徑。 在文件，您可以選擇要包含或排除的編製索引的路徑。 這可以提供改進的寫入效能和案例事先知道查詢模式會降低索引儲存體。

索引路徑的開頭為根 (/)，且通常結尾為 ? 萬用字元運算子。 這表示有多個可能的值為前置詞。 例如，若要為 SELECT * FROM Families F WHERE F.familyName = "Andersen" 提供服務，您必須在集合的索引原則中包含 /familyName/? 的索引路徑。

索引路徑也可以使用 \* 萬用字元運算子來指定路徑首碼底下的遞迴行為。 例如，使用 /payload/* 可將 payload 屬性下的所有項目自編製索引作業中排除。

以下是指定索引路徑的常見模式：

| Path                | 描述/使用案例                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | 集合的預設路徑。 遞迴，並套用至整份文件樹狀目錄。                                                                                                                                                                                                                                   |
| /prop/?             | 索引路徑所需做的查詢如下所示 (具有雜湊或範圍類型分別):<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                       |
| /"prop"/*             | 指定之標籤底下所有路徑的索引路徑。 使用下列查詢<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop         |
| /props/[]/?         | 為反覆項目及針對 ["a"、"b"、"c"] 等純量陣列進行之聯結查詢提供服務所需的索引路徑：<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5                                                                         |
| /props/[]/subprop/? | 為反覆項目及針對 [{subprop: "a"}, {subprop: "b"}] 等物件陣列進行之聯結查詢提供服務所需的索引路徑：<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"                                  |
| /prop/subprop/?     | 索引路徑所需做的查詢 (具有雜湊或範圍類型分別):<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> 當您設定自訂的索引路徑時，您都必須指定的預設索引整份文件樹狀目錄中，表示特殊路徑規則"/ *"。 
> 
> 

下列範例會設定特定的路徑具有範圍索引和自訂的有效位數的值 20 個位元組：

    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    

    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    // Default for everything else
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" ,
            Indexes = new Collection<Index> {
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 } 
            }
        });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), pathRange);


### <a name="index-data-types-kinds-and-precisions"></a>索引資料類型、 類型，以及精確度
當您設定路徑的檢索原則時，您會有多個選項。 您可以為每個路徑指定一或多個編製索引的定義：

* **資料型別**： 字串、 數字、 點、 多邊形或 LineString （可以包含每個每個路徑的資料類型只有一個項目）。
* **索引類型**： 雜湊 （等號查詢）、 範圍 （等號比較、 範圍或 ORDER BY 的查詢） 或空間 （空間查詢）。
* **有效位數**： 的雜湊索引，這不同於 1 到 8 的字串與數字。 預設值為 3。 範圍索引，這個值可以是-1 （最大有效位數）。 它可以介於 1 和 100 （最大有效位數） 的字串或數字的值而有所不同。

#### <a name="index-kind"></a>索引類型
Azure Cosmos DB 支援雜湊索引和範圍索引類型為字串或數字資料類型，可以設定每個路徑，或兩者。

* **雜湊** 支援有效率的相等查詢和 JOIN 查詢。 對於大部分的使用情況下，雜湊索引不需要更高的有效位數超過 3 個位元組的預設值。 資料類型可以是字串或數字。
* **範圍**支援有效率的等號查詢，範圍查詢 (使用 >，<>、 =、 < =、 ！ =)，以及 ORDER BY 的查詢。 預設的 ORDER By 查詢也會需要最大索引有效位數 (-1)。 資料類型可以是字串或數字。

Azure Cosmos DB 也支援空間索引類型，每個路徑可供指定的點、 多邊形或 LineString 資料型別。 位於指定路徑的值必須是有效的 GeoJSON 片段，例如 `{"type": "Point", "coordinates": [0.0, 10.0]}`。

* **空間** 支援有效率的空間 (內部和距離) 查詢。 資料類型可以是點、 多邊形或 LineString。

> [!NOTE]
> Azure Cosmos DB 支援點、 多邊形和 LineString 的資料類型的自動編製索引。
> 
> 

以下是支援的索引種類和可用來處理的查詢的範例：

| 索引類型 | 描述/使用案例                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 雜湊       | Hash over /prop/? (或 /) 可用來有效率地處理下列查詢︰<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>Hash over /props/[]/? (或 / 或 /props/) 可用來有效率地處理下列查詢︰<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5                                                                                                                       |
| 範圍      | Range over /prop/? (或 /) 可用來有效率地處理下列查詢︰<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                                                                                                                                                              |
| 空間     | Range over /prop/? (或 /) 可用來有效率地處理下列查詢︰<br><br>SELECT FROM collection c<br><br>WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Polygon", ... }) --with indexing on points enabled<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type": "Point", ... }, c.prop) --with indexing on polygons enabled              |

根據預設，則會傳回錯誤的包含查詢這類範圍運算子 > = 如果發出信號的掃描可能需要做的查詢沒有範圍索引 （的任何有效位數）。 範圍查詢可以透過執行不含範圍索引**x ms-documentdb-啟用-掃描**REST API 中的標頭或**EnableScanInQuery**使用.NET SDK 要求選項。 如果 Azure Cosmos DB 可以使用索引來篩選查詢中有任何其他篩選條件，則會不傳回任何錯誤。

相同的規則適用於空間查詢。 根據預設，如果沒有空間索引，而且沒有可從索引提供服務的其他篩選，則會針對空間查詢傳回錯誤。 它們可以透過執行掃描為**x ms-documentdb-啟用-掃描**或**EnableScanInQuery**。

#### <a name="index-precision"></a>索引精確度
進行索引的儲存負擔和查詢效能之間的利弊得失，您可以使用索引的有效位數。 數字，我們建議使用有效位數的預設設定為-1 （最大值）。 因為數字是在 JSON 中的 8 個位元組，這就相當於 8 個位元組的組態。 選擇較低的值的有效位數，例如 1 到 7，在某些範圍內的值對應到相同的方式建立索引項目。 因此，降低索引儲存空間，但是可能需要處理多個文件查詢執行。 因此，它會消耗更多的輸送量，以要求單位。

索引精準度組態對於字串範圍有更實際的應用。 由於字串可以是任何任意長度，索引的有效位數的選擇可能會影響字串範圍查詢的效能。 它也可能會影響索引的儲存空間所需的數量。 字串範圍索引可以設定 1 到 100 則為-1 （最大值）。 如果您想要執行 ORDER BY 查詢字串屬性，您必須指定有效位數為-1 的對應路徑。

空間索引一律會使用預設的索引有效位數 （點、 LineString、 和多邊形） 的所有類型。 無法覆寫的預設索引的有效位數為空間索引。 

下列範例會示範如何使用.NET SDK 增加範圍索引集合中的有效位數。 

**使用自訂索引精確度建立集合**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for strings to Range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> 當查詢使用 ORDER BY，但不一定範圍的索引，對查詢路徑的最大有效位數時，azure Cosmos DB 將傳回錯誤。 
> 
> 

同樣地，您可以完全排除路徑編製索引。 下一個範例示範如何排除文件的整個區段 (*樹狀子目錄*) 使用索引從\*萬用字元運算子。

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opt-in-and-opt-out-of-indexing"></a>參加，並退出編製索引
您可以選擇是否要讓集合自動編製所有文件的索引。 根據預設，自動檢索所有文件，但您可以關閉自動索引。 索引已關閉時，文件可以存取只能透過其自我連結，或由查詢所使用的文件識別碼。

在關閉自動索引編製功能的情況下，您仍然可以選擇性地只將特定的文件新增到索引中。 相反地，您可以離開自動索引，並選擇性地選擇要排除特定的文件。 您有必須進行查詢的文件的子集時，開啟/關閉組態索引非常有用的。

下列範例示範如何透過使用明確包含文件[SQL API 的.NET SDK](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet)和[RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx)屬性。

    // If you want to override the default collection behavior to either
    // exclude (or include) a document in indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modify-the-indexing-policy-of-a-collection"></a>修改集合的編製索引原則
在 Azure Cosmos DB 中，您可以變更集合，以即時編製索引原則。 檢索原則的 Azure Cosmos DB 集合中的變更可能會導致索引的圖形中的變更。 變更會影響可以編製索引的路徑，其有效位數和索引本身的一致性模型。 有效的檢索原則的變更需要舊索引轉換成新的索引。 

**線上索引轉換**

![編製索引的運作方式 – Azure Cosmos DB 線上索引轉換](./media/indexing-policies/index-transformations.png)

索引轉換為線上進行。 這表示每個舊原則編製索引的文件會有效率地轉換每個新的原則*而不會影響寫入可用性或佈建的輸送量*的集合。 一致性的讀取和寫入作業使用 REST API，Sdk，或從預存程序和觸發程序不會影響索引轉換期間。 沒有任何效能降低或停機時間以您的應用程式時做變更的編製索引原則。

不過，在索引轉換進行期間，不論索引編製模式設定 (「一致」或「延遲」) 為何，查詢最終會是一致的。 這也適用於查詢從所有介面： REST API，Sdk，並從預存程序和觸發程序。 就像 Lazy 編製索引與索引轉換是以非同步方式在背景中執行複本上使用備用資源可供特定的複本。 

在位置中也進行索引轉換。 Azure Cosmos DB 不會維護兩份索引及交換 out 舊索引以新的。 這表示沒有額外的磁碟空間是必要或索引轉換發生時，在集合中取用。

當您變更編製索引原則時，變更會套用至從舊的索引移至新的主要索引模式組態為基礎。 檢索模式設定播放高於其他值，例如包含/排除的路徑、 索引類型和有效位數而較大的角色。 

如果您舊的和新的原則同時使用一致的索引，則 Azure Cosmos DB 執行線上索引轉換。 您無法套用另一個索引的原則變更正在進行轉換時，具有一致的檢索模式。 不過，您可以移動 Lazy 或無檢索模式轉換時正在進行中： 

* 當您移動至 Lazy 時，索引的原則變更正有效。 Azure Cosmos DB 開始重新建立索引以非同步的方式。 
* 當您移動為 None 時，會立即卸除的索引。 為 None 移動時，您想要取消進行中 」 轉換，並啟動全新與不同的編製索引原則。 

下列程式碼片段示範如何修改集合的檢索原則從一致的檢索模式以延遲的檢索模式。 如果您使用.NET SDK，您可以開始建立索引的原則變更使用新**ReplaceDocumentCollectionAsync**方法。

**修改從一致至 Lazy 的編製索引原則**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);

**追蹤進度索引轉換**

您也可以使用一致的索引來追蹤 「 索引 」 轉換的百分比進度**IndexTransformationProgress** response 屬性從**ReadDocumentCollectionAsync**呼叫。 其他 Sdk 和 REST API，支援對等的屬性和方法來進行索引的原則變更。 您可以藉由呼叫檢查索引所要的轉換一致的索引進行**ReadDocumentCollectionAsync**: 

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await client.ReadDocumentCollectionAsync(
            UriFactory.CreateDocumentCollectionUri("db", "coll"));

        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

> [!NOTE]
> * **IndexTransformationProgress**屬性適用於只有時轉換成一致的索引。 使用**ResourceResponse.LazyIndexingProgress**追蹤轉換至延遲索引的屬性。
> * **IndexTransformationProgress**和**LazyIndexingProgress**屬性只會填入一個非資料分割的集合，也就是建立不含資料分割索引鍵的集合。
>

您可以移動到「無」索引編製模式，以卸除集合的索引。 如果您想要取消進行中 」 轉換，並立即開始一個新，可能很有用的操作工具。

**卸除集合的索引**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

您什麼時候會對 Azure Cosmos DB 集合進行編製索引原則變更？ 以下是最常見的使用案例：

* 一般作業期間，提供一致的結果，但改為使用延遲的檢索模式期間大量資料匯入。
* 開始使用新的索引功能上目前的 Azure Cosmos DB 集合。 例如，您可以使用地理空間查詢，這項工作需要的空間索引類型，或 ORDER BY/範圍查詢，需要字串範圍索引類型的字串。
* 手動選取的屬性要編製索引，並隨著時間變更它們。
* 微調索引的有效位數，來改善查詢效能，或減少耗用儲存體。

> [!NOTE]
> 若要使用修改編製索引原則**ReplaceDocumentCollectionAsync**，您必須使用版本 1.3.0 或更新版本的.NET sdk。
> 
> 若要順利完成索引轉換，請確認有足夠的可用儲存空間可用集合上。 如果集合已達到其儲存體配額，就會暫停索引轉換。 索引轉換就會自動繼續儲存空間可用時，例如，如果您刪除某些文件。
> 
> 

## <a name="performance-tuning"></a>效能微調
SQL Api 提供的效能度量，例如使用的索引儲存體以及輸送量成本 （要求單位），以針對每個作業的相關資訊。 您可以使用這項資訊來比較不同的檢索原則，以及用來微調效能。

若要檢查儲存體配額和使用方式的集合，請執行**HEAD**或**取得**要求針對集合資源。 接著，檢查**x ms-要求配額**和**x ms-要求使用量**標頭。 在 .NET SDK 中，[ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx) 中的 [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) 和 [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) 屬性包含這些對應的值。

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


若要測量索引的每一個寫入作業的額外負荷 （建立、 更新或刪除），檢查**x ms-要求免費**標頭 (或同等權限[RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx)屬性[ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) .NET SDK 中) 來測量的這些作業所耗用的要求單位數。

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);

     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"), queryString).AsDocumentQuery();

     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }

     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## <a name="changes-to-the-indexing-policy-specification"></a>索引編製原則規格的變更
2015 年 7 月 7 日到使用 REST API 2015-06-03 版本引進了檢索原則的結構描述中的變更。 SDK 版本中的對應類別具有符合結構描述的新實作。 

在 JSON 規格中實作下列變更：

* 檢索原則的字串支援範圍索引。
* 每個路徑可以有多個索引定義。 它可以有一個用於每種資料類型。
* 編製索引的有效位數支援 1 到 8 的數字、 1 到 100 的字串，請和-1 （最大有效位數）。
* 路徑區段不需要雙引號來逸出每個路徑。 例如，您可以新增的路徑  **/標題 /？** 而不是**"title"/？**。
* 代表 「 所有路徑"的根路徑可以表示成 **/ \***  (除了 **/** )。

如果您有程式碼，會佈建集合與使用.NET SDK 1.1.0 版或較早版本撰寫的自訂編製索引原則，將移到 SDK 版本 1.2.0，您必須變更您的應用程式程式碼，來處理這些變更。 如果您沒有程式碼，會設定編製索引原則，或如果您打算繼續使用舊版 SDK 時，不需要任何變更。

如需實用的比較，這裡是使用 REST API 版本 2015年-06-03，後面接著使用較早的 REST API 2015-04-08 版所撰寫相同的編製索引原則所撰寫自訂的編製索引原則的範例。

**目前的檢索原則 JSON (REST API 版本 2015年-06-03)**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Hash",
                   "dataType":"Number",
                   "precision":7
                }
             ]
          }
       ],
       "ExcludedPaths":[
          {
             "path":"/nonIndexedContent/*"
          }
       ]
    }


**先前編製索引原則 JSON (REST API 2015-04-08 版)**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "IncludedPaths":[
          {
             "IndexType":"Hash",
             "Path":"/",
             "NumericPrecision":7,
             "StringPrecision":3
          }
       ],
       "ExcludedPaths":[
          "/\"nonIndexedContent\"/*"
       ]
    }


## <a name="next-steps"></a>後續步驟
如需索引的原則管理範例及深入了解 Azure Cosmos DB 查詢語言，請參閱下列連結：

* [SQL API.NET 索引管理程式碼範例](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
* [集合的 SQL 應用程式開發介面 REST 作業](https://msdn.microsoft.com/library/azure/dn782195.aspx)
* [使用 SQL 查詢](sql-api-sql-query.md)

