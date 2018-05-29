---
title: 重建 Azure 搜尋服務索引或重新整理可搜尋內容 | Microsoft Docs
description: 藉由完整重建或部分累加索引編製來新增元素、更新現有的元素或文件或刪除過時的文件，以重新整理 Azure 搜尋服務索引。
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: f38054eaf2829149a496f840366b6f2f9e03e12b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942098"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>如何重建 Azure 搜尋服務索引

重建索引可變更其結構，改變索引在 Azure 搜尋服務中的實際表示法。 相反地，重新整理索引則是僅限於內容的更新，會從參與的外部資料來源收取最新的變更。 本文將指示如何在結構和實質上同時更新索引。

進行索引更新時需要具備服務層級的讀寫權限。 您可以用程式設計方式，使用指定更新選項的參數呼叫 REST 或 .NET API，為內容進行完整重建或累加的索引編製。 

一般而言，索引的更新是隨需的。 不過，對於使用來源特定[索引子](search-indexer-overview.md)進行填入的索引，您可以使用內建的排程器。 排程器可支援每 15 分鐘一次的文件重新整理，而且您可以依需求設定任意間隔和模式。 較快的重新整理速率需要以手動方式推送索引更新，這有可能透過交易的重複寫入來執行，而同時更新外部資料來源和 Azure 搜尋服務索引。

## <a name="full-rebuilds"></a>完整重建

對於許多類型的更新，都必須執行完整重建。 完整重建是指刪除索引 (包括資料和中繼資料)，接著再從外部資料來源重新填入索引。 請以程式設計方式[刪除](https://docs.microsoft.com/rest/api/searchservice/delete-index)、[建立](https://docs.microsoft.com/rest/api/searchservice/create-index)並[重新載入](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)索引，而加以重建。 

重建之後請留意，如果您已測試過查詢模式和評分設定檔，且基礎內容已變更，則可以預期查詢結果將會有變化。

## <a name="when-to-rebuild"></a>重建時機

在作用中的開發期間，如果索引結構描述處於變動狀態，則應規劃頻繁的完整重建。

| 修改 | 重建狀態|
|--------------|---------------|
| 變更欄位名稱、資料類型或其[索引屬性](https://docs.microsoft.com/rest/api/searchservice/create-index) | 變更欄位定義通常會造成重建的負面影響，但下列[索引屬性](https://docs.microsoft.com/rest/api/searchservice/create-index)除外：Retrievable、SearchAnalyzer、SynonymMaps。 您可以將 Retrievable、SearchAnalyzer 和 SynonymMaps 屬性新增至現有的欄位，而無須重建其索引。|
| 新增欄位 | 重建並沒有嚴格的需求。 對於已編製索引的現有文件，會為其新欄位提供 Null 值。 在未來重新編製索引時，來源資料中的值會取代 Azure 搜尋服務所加入的 Null。 |
| 刪除欄位 | 您無法直接從 Azure 搜尋服務索引中刪除欄位。 您應讓應用程式忽略「已刪除的」欄位，以避免使用該欄位。 實際上，在您下次使用省略問題欄位的結構描述重建索引之前，欄位定義和內容都會保留在索引中。|

> [!Note]
> 如果您切換層，也需要重建。 如果您在某個時間點決定提高容量，將無法使用就地升級。 您必須在新的容量點建立新的服務，且必須為新服務從頭建置索引。 

## <a name="partial-or-incremental-indexing"></a>部分或累加式索引編製

在索引進入生產環境後，焦點將會移轉至累加式索引編製，這通常不會沒有明顯的服務中斷。 部分或累加式索引編製是僅限內容的工作負載，會同步處理搜尋索引的內容，以反映參與的資料來源內容所處的狀態。 在來源中新增或刪除的文件，將會在索引中新增或刪除。 在程式碼中，呼叫[新增、更新或刪除文件](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)作業或 .NET 的等同作業。

> [!Note]
> 使用會搜耙外部資料來源的索引子時，會使用來源系統中的變更追蹤機制進行累加式索引編製。 對於 [Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection)，會使用 `lastModified` 欄位。 在 [Azure 資料表儲存體](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection)中，`timestamp` 具有相同的用途。 同樣地，[Azure SQL Database 索引子](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows)和 [Azure Cosmos DB 索引子](search-howto-index-cosmosdb.md#indexing-changed-documents)都具有標示資料列更新的欄位。 如需索引子的詳細資訊，請參閱[索引子概觀](search-indexer-overview.md)。

## <a name="scale-out-indexing"></a>相應放大索引

隨著資料磁碟區的成長或處理需求的變更，您可能會發現簡單的重建和重新編製索引作業已不敷需求。 要因應增加的需求，首先我們建議您現有服務的限制範圍內提高[規模和容量](search-capacity-planning.md)。 

如果您可以使用[索引子](search-indexer-overview.md)，則其他相應放大機制也將可供使用。 索引子會隨附內建的排程器，可讓您定期分批編製索引，或將處理延伸到 24 小時的時間範圍外。 此外，在與資料來源定義配對時，索引子可藉由分割資料並使用排程以平行方式執行，而協助您達成平行處理原則。

### <a name="scheduled-indexing-for-large-data-sets"></a>為大型資料集排定的索引編製

排程是處理大型資料集和執行緩慢的分析 (例如，認知搜尋管線中的影像分析) 的重要機制。 索引子處理會在 24 小時的期間內運作。 如果處理無法在 24 小時內完成，索引子排程的行為可能對您會有幫助。 

根據設計，排程的索引編製會在特定間隔啟動，且作業通常會在繼續執行下一個排程的間隔之前完成。 不過，如果處理未在間隔內完成，索引子就會停止 (因為其時間已用盡)。 在下一個間隔中，將會從上次中斷之處繼續進行處理，而系統會追蹤該發生點。 

實際上，對於跨數天的索引負載，您可以將索引子安排在 24 小時的排程上。 繼續在下一個 24 小時的期間內進行索引編製時，作業將會從最後一個已知的正常文件重新開始。 如此，索引子將可順利在數天內處理待處理項目，直到所有未處理的文件都完成處理為止。 如需此方法的詳細資訊，請參閱[為大型資料集編製索引](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>平行索引

第二個選項是設定以平行方式處理的索引編製策略。 對於非常態且須耗用較高計算資源的索引編製需求 (例如認知搜尋管線中對掃描文件的 OCR)，平行索引策略可能是達成該目標的理想方式。 在認知搜尋擴充管線中，影像分析和自然語言處理都需要長時間執行。 對不會同時處理查詢要求的服務使用平行索引策略，或許可因應龐大且處理速度緩慢的內容所產生的問題。 

平行處理的策略具有下列元素：

+ 將來源資料分置於多個容器中，或是相同容器內的多個虛擬資料夾中。 
+ 將每個小型資料集對應至一個[資料來源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)，並與其本身的[索引子](https://docs.microsoft.com/rest/api/searchservice/create-indexer)配對。
+ 對於認知搜尋，請參考每個索引子定義中的相同[技能集](ref-create-skillset.md)。
+ 寫入相同的目標搜尋索引中。 
+ 將所有索引子排程在相同時間執行。

> [!Note]
> Azure 搜尋服務不支援將複本或分割區分配給特定工作負載。 同時編製大量索引的風險，是系統負擔過重而影響到查詢效能。 如果您有測試環境，請先在該處實作平行索引以了解利弊得失。

### <a name="configure-parallel-indexing"></a>設定平行索引

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