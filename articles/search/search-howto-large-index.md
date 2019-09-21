---
title: 使用內建索引子為大型資料集編製索引 - Azure 搜尋服務
description: 了解大型資料的索引編製策略或透過批次模式進行密集計算的索引編製策略、資源運用，以及排程式、平行和分散式索引編製的技術。
services: search
author: HeidiSteen
manager: nitinme
ms.service: search
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: heidist
ms.openlocfilehash: 44a8136c4e02d4eceb5b11231bbbfed010159e75
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172868"
---
# <a name="how-to-index-large-data-sets-in-azure-search"></a>如何在 Azure 搜尋服務中為大型資料集編製索引

隨著資料量的成長或處理需求的變更，您可能會發現簡單或預設的索引編制策略已不再具有生產力。 Azure 搜尋服務有好幾種可因應較大型資料集的方法，範圍從如何建構資料上傳要求，到使用來源專屬的索引子來處理排程式和分散式的工作負載。

對大型資料適用的技術，同樣適用於長時間執行的程序。 具體來說，[平行索引編製](#parallel-indexing)中概述的步驟就適用於需要密集計算的索引編製作業，例如[認知搜尋管線](cognitive-search-concept-intro.md)中的影像分析或自然語言處理。

下列各節將探索用來編制大量資料索引的三項技術。

## <a name="option-1-pass-multiple-documents"></a>選項 1：傳遞多個檔

若要為較大型的資料集編製索引，其中一個最簡單的機制是在單一要求中提交多個文件或記錄。 只要整個承載的大小在 16 MB 以內，要求便可以在大量上傳作業中處理多達 1000 個文件。 無論您使用的是 .NET SDK 中的[REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)或[IndexBatch](https://docs.microsoft.com/otnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) ，都適用這些限制。 針對任一 API，您會在每個要求的主體中封裝1000檔。

在為個別要求實作批次索引編製時，會使用 REST、.NET 或透過索引子。 少數索引子會有不同的運作限制。 具體而言，Azure Blob 索引編製作業會在發現平均文件大小較大時，將批次大小設定為 10 個文件。 針對以[建立索引子 REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer ) 為基礎的索引子，您可以設定 `BatchSize` 引數以自訂此設定，以便更加符合資料的特性。 

> [!NOTE]
> 若要讓檔案大小保持關閉，請避免將不可查詢的資料加入至索引。 影像和其他二進位資料無法直接搜尋，而且不應該儲存於索引中。 若要將不可查詢的資料整合到搜尋結果，請定義不可搜尋的欄位，以儲存資源的 URL 參考。

## <a name="option-2-add-resources"></a>選項 2：新增資源

佈建在其中一個[標準定價層](search-sku-tier.md)的服務，其儲存體和工作負載 (查詢或索引編製) 容量通常不會充分利用，因此若要因應較大型的資料集，明顯可以使用[增加分割區和複本計數](search-capacity-planning.md)作為解決方案。 若要獲得最佳結果，您需要同時擁有這兩個資源：分割區 (用於儲存) 和複本 (用於資料擷取工作)。

增加複本和分割區是可增加成本的計費事件，但除非您在最大負載的情況下持續編制索引，否則您可以在編制索引程式期間增加規模，然後在索引編制之後調整資源層級演練.

## <a name="option-3-use-indexers"></a>選項 3：使用索引子

[索引子](search-indexer-overview.md)是用來將支援的 Azure 資料平臺上的外部資料源編目，以取得可搜尋的內容。 有幾個索引子功能雖然不是專門用於大規模的索引編製作業，但特別適用於因應較大型的資料集：

+ 排程器可讓您以固定間隔分拆索引編製作業，以將作業分散到不同時間進行。
+ 排程的索引編製作業可以在最後一個已知的停止點繼續進行。 如果資料來源未能在 24 小時內完整搜耙一遍，索引子會在第二天從上次停下的地方繼續開始索引編製作業。
+ 將資料分割成較小的個別資料來源可實現平行處理作業。 您可以將大型資料集分割成較小的資料集，然後建立可平行編制索引的多個索引子資料來源定義。

> [!NOTE]
> 索引子有專門適用的資料來源，因此使用索引子的方法只適用於 Azure 上的選定資料來源：[SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)、[Blob 儲存體](search-howto-indexing-azure-blob-storage.md)、[資料表儲存體](search-howto-indexing-azure-tables.md)、[Cosmos DB](search-howto-index-cosmosdb.md)。

### <a name="scheduled-indexing"></a>排程的索引編製

索引子排程是一種重要機制，可用來處理大型資料集以及緩慢執行的程序 (例如，認知搜尋管線中的影像分析)。 索引子處理會在 24 小時的期間內運作。 如果處理無法在 24 小時內完成，索引子排程的行為可能對您會有幫助。 

根據設計，排程的索引編製會在特定間隔啟動，且作業通常會在繼續執行下一個排程的間隔之前完成。 不過，如果處理未在間隔內完成，索引子就會停止 (因為其時間已用盡)。 在下一個間隔中，將會從上次中斷之處繼續進行處理，而系統會追蹤該發生點。 

實際上，對於跨數天的索引負載，您可以將索引子安排在 24 小時的排程上。 繼續在下一個 24 小時的週期內進行索引編製時，作業會從最後一個已知的正常文件重新開始。 如此，索引子將可順利在數天內處理待處理項目，直到所有未處理的文件都完成處理為止。 如需此方法的詳細資訊，請參閱[在 Azure Blob 儲存體中為大型資料集編製索引](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)。 如需有關設定一般排程的詳細資訊，請參閱[建立索引子 REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer#request-syntax)或參閱[如何排程 Azure 搜尋服務的索引子](search-howto-schedule-indexers.md)。

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>平行索引

平行索引編製策略所根據的是以一致的方式為多個資料來源編製索引，在此策略中，每個資料來源定義都會指定資料的子集。 

若需要進行非例行性且需要密集計算的索引編製作業 (例如，對認知搜尋管線中的掃描文件進行 OCR、影像分析或自然語言處理)，平行索引編製策略往往是能在最短時間完成長時間執行程序的理想方法。 如果您可以消除或減少查詢要求，對不會同時處理查詢的服務使用平行索引編製策略，便會成為因應龐大且處理速度緩慢的內容的最佳策略選項。 

平行處理具有下列元素：

+ 將來源資料分置於多個容器中，或是相同容器內的多個虛擬資料夾中。 
+ 將每個迷你資料集對應至它自己的[資料來源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)，並與它自己的[索引子](https://docs.microsoft.com/rest/api/searchservice/create-indexer)配對。
+ 對於認知搜尋，請參考每個索引子定義中的相同[技能集](https://docs.microsoft.com/rest/api/searchservice/create-skillset)。
+ 寫入相同的目標搜尋索引中。 
+ 將所有索引子排程在相同時間執行。

> [!NOTE]
> Azure 搜尋服務不支援將複本或分割區分配給特定工作負載。 同時編製大量索引的風險，是系統負擔過重而影響到查詢效能。 如果您有測試環境，請先在該處實作平行索引以了解利弊得失。

### <a name="how-to-configure-parallel-indexing"></a>如何設定平行索引編製

就索引子而言，處理容量大致上會以搜尋服務所使用的每個服務單位 (SU) 的一個索引子子系統為基礎。 在至少有兩個複本的基本或標準層上佈建的 Azure 搜尋服務，可能會有多個並行的索引子。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，在搜尋服務儀表板的 [概觀] 頁面上查看 [定價層]，以確認足供平行索引之用。 基本層和標準層都提供多個複本。

2. 在 [設定] > [調整] 中，為平行處理[增加複本](search-capacity-planning.md)：每個索引子工作負載多加一個其他複本。 現有的查詢磁碟區保留足夠的數目。 為了編製索引而犧牲查詢工作負載，不是理想的取捨。

3. 在 Azure 搜尋索引子可達到的層級上，將資料散發到多個容器中。 這可以是 Azure SQL Database 中的多個資料表、Azure Blob 儲存體中的多個容器，或是多個集合。 為每個資料表或容器定義一個資料來源物件。

4. 建立和排程多個索引子，以平行方式執行：

   + 假設一項服務有六個複本。 設定六個索引子，分別對應至包含六分之一個資料集的資料來源，讓整個資料集分割到六個不同的去處。 

   + 將每個索引子指向相同的索引。 對於認知搜尋工作負載，將每個索引子指向相同的技能集。

   + 在每個索引子定義內，排程相同的執行階段執行模式。 例如，`"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` 會對所有索引子建立 2018-05-15 的排程，以八小時的間隔執行。

在排定的時間，所有索引子都會開始執行、載入資料、套用擴充 (如果您設定了認知搜尋管線)，並寫入至索引。 Azure 搜尋服務不會鎖定索引的更新。 並行寫入會受到管理，如果特定寫入在第一次嘗試時不成功，將會重試。

> [!Note]
> 增加複本時，如果預計索引大小會大幅增加，請考慮增加分割區計數。 分割區會儲存配量的索引內容；您的分割區愈多，每個分割區所須儲存的配量就愈少。

## <a name="see-also"></a>另請參閱

+ [索引子概觀](search-indexer-overview.md)
+ [在入口網站中編製索引](search-import-data-portal.md)
+ [Azure SQL Database 索引子](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB 索引子](search-howto-index-cosmosdb.md)
+ [Azure Blob 儲存體索引子](search-howto-indexing-azure-blob-storage.md)
+ [Azure 資料表儲存體索引子](search-howto-indexing-azure-tables.md)
+ [Azure 搜尋服務中的安全性](search-security-overview.md)
