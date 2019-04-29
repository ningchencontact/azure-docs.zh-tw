---
title: 重建 Azure 搜尋服務索引或重新整理可搜尋內容 - Azure 搜尋服務
description: 藉由完整重建或部分累加索引編製來新增元素、更新現有的元素或文件或刪除過時的文件，以重新整理 Azure 搜尋服務索引。
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2595912732389c8a415d1854a84a7b9c182e4dc7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871143"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>如何重建 Azure 搜尋服務索引

本文說明如何重建 Azure 搜尋服務索引、需要重建索引的情況為何，以及降低重建索引對進行中查詢要求之影響的建議。

「重建」是指卸除並重新建立與索引 (包括所有欄位型反向索引) 相關聯的實體資料結構。 在 Azure 搜尋服務中，您無法卸除並重新建立個別的欄位。 若要重建索引，必須刪除所有的欄位儲存體，根據現有的或修訂過的索引結構描述來重新建立，然後填入推送至索引的資料，或從外部來源提取的資料。 在開發期間通常會重建索引，但您可能也需要重建生產層級的索引，以配合結構變更 (如新增複雜類型)，或新增欄位至建議工具。

相較於使索引離線時的重建，「資料重新整理」執行為背景工作。 您可以新增、移除及取代文件查詢工作負載，且對查詢工作負載的中斷最短，不過查詢通常需要較長的時間才能完成。 如需有關更新索引內容的詳細資訊，請參閱[新增、更新或刪除文件](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) \(英文\)。

## <a name="rebuild-conditions"></a>重建條件

| 條件 | 描述 |
|-----------|-------------|
| 變更欄位定義 | 修改欄位名稱、資料類型或特定[索引屬性](https://docs.microsoft.com/rest/api/searchservice/create-index) \(英文\) (可搜尋、可篩選、可排序、可面向化) 需要完整重建。 |
| 將分析器指派給欄位 | [分析器](search-analyzers.md)定義在索引中，之後指派給欄位。 您可以隨時將新分析器定義新增至索引，但當欄位建立後，您只能*指派*分析器。 這適用於 **analyzer** 和 **indexAnalyzer** 屬性。 **searchAnalyzer** 屬性是例外狀況 (您可以將此屬性指派給現有欄位)。 |
| 更新或刪除索引中的分析器定義 | 您無法刪除或變更索引中的現有分析器組態 (分析器、權杖化工具、權杖篩選器或 char 篩選器)，除非您重建整個索引。 |
| 將欄位新增至建議工具 | 如果已有欄位存在，且您想要將它新增至[建議工具](index-add-suggesters.md)建構中，您必須重建索引。 |
| 刪除欄位 | 若要實體上移除欄位的所有追蹤，您必須重建索引。 如果沒有立即進行重建，您可以修改應用程式碼，以停用對「已刪除」欄位的存取。 實體上，在您下次套用省略問題欄位的結構描述重建之前，欄位定義和內容都會保留在索引中。 |
| 切換階層 | 如果您需要更多容量，則沒有就地升級的做法。 您必須在新的容量點建立新的服務，且必須為新服務從頭建置索引。 |

可以進行任何其他修改，而不會影響現有的實體結構。 具體來說，下列變更*不*需要重建索引：

+ 新增欄位
+ 在現有欄位上設定 [retrievable] 屬性
+ 在現有欄位上設定 [searchAnalyzer]
+ 在索引中新增新的分析器定義
+ 新增、更新或刪除評分設定檔
+ 新增、更新或刪除 CORS 設定
+ 新增、更新或刪除 synonymMaps

當您新增新的欄位時，現有已編製索引的文件會為新欄位提供 Null 值。 未來重新整理資料時，來自外部來源資料的值會取代 Azure 搜尋服務所加入的 Null。 如需有關更新索引內容的詳細資訊，請參閱[新增、更新或刪除文件](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) \(英文\)。

## <a name="partial-or-incremental-indexing"></a>部分或累加式索引編製

在 Azure 搜尋服務中，您無法依每個欄位來控制編製索引，即無法選擇刪除或重新建立特定欄位。 同樣地，沒有內建機制能[根據準則來編製索引](https://stackoverflow.com/questions/40539019/azure-search-what-is-the-best-way-to-update-a-batch-of-documents) \(英文\)。 任何您所需的準則驅動索引編製，都必須透過自訂程式碼來達成。

不過，您輕鬆就能「重新整理」索引中的文件。 對於許多搜尋解決方案，外部來源資料是暫時性的，且來源資料和搜尋索引之間的同步處理是常見的作法。 在程式碼中，呼叫[新增、更新或刪除文件](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) \(英文\) 作業或 [.NET 的對等項目](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexesoperationsextensions.createorupdate?view=azure-dotnet) \(英文\)，以更新索引內容或為新欄位加入值。

## <a name="partial-indexing-with-indexers"></a>使用索引子部分編製索引

[索引子](search-indexer-overview.md)簡化資料重新整理工作。 索引子只能對外部資料來源中的一個資料表或檢視編製索引。 若要對多個資料表編製索引，最簡單的方法是建立加入多個資料表的檢視，並投影您要編製索引的資料行。 

使用會搜耙外部資料來源的索引子時，請檢查來源資料中的「高水位標記」資料行。 如果此資料行存在，您可以只挑選包含新的或修訂過的內容之資料列，以將它用於累加變更偵測。 對於 [Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection)，會使用 `lastModified` 欄位。 在 [Azure 資料表儲存體](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection)中，`timestamp` 具有相同的用途。 同樣地，[Azure SQL Database 索引子](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows)和 [Azure Cosmos DB 索引子](search-howto-index-cosmosdb.md#indexing-changed-documents)都具有標示資料列更新的欄位。 

如需有關索引子的詳細資訊，請參閱[索引子概觀](search-indexer-overview.md)和[重設索引子 REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) \(英文\)。

## <a name="how-to-rebuild-an-index"></a>如何重建索引

在作用中的開發期間，如果索引結構描述處於變動狀態，則應規劃頻繁的完整重建。 對於已經在生產環境中的應用程式，我們建議您建立與現有索引並存執行的新索引，以避免查詢停機時間。

進行索引更新時需要具備服務層級的讀寫權限。 

您無法在入口網站中重建索引。 以程式設計的方式來說，您可以呼叫[更新索引 REST API](https://docs.microsoft.com/rest/api/searchservice/update-index) /(英文/) 或[等同的 .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexesoperations.createorupdatewithhttpmessagesasync?view=azure-dotnet) /(英文/)，以用於完整重建。 更新索引要求與[建立索引 REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) \(英文\) 相同，但其內容不同。

下列工作流程雖然偏向 REST API，但同樣適用於 .NET SDK。

1. 當重複使用索引名稱時，請[卸除現有索引](https://docs.microsoft.com/rest/api/searchservice/delete-index) \(英文\)。 

   目標是該索引的任何查詢都會立即被卸除。 刪除索引是無法復原的動作，這樣會終結欄位集合和其他建構的實體儲存體。 卸除索引之前，請務必清楚了解刪除它的意涵。 

2. 使用服務端點、API 金鑰及[管理金鑰](https://docs.microsoft.com/azure/search/search-security-api-keys)編寫[更新索引](https://docs.microsoft.com/rest/api/searchservice/update-index) \(英文\) 要求。 寫入作業需要管理金鑰。

3. 在要求的內文中，提供包含已變更或已修訂欄位定義的索引結構描述。 要求本文包含索引結構描述，以及評分設定檔的建構、分析器、建議工具和 CORS 選項。 結構描述需求記錄在[建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index) \(英文\) 中。

4. 傳送[更新索引](https://docs.microsoft.com/rest/api/searchservice/update-index) \(英文\) 要求以重建 Azure 搜尋服務上索引的實體運算式。 

5. 使用來自外部來源的[文件載入索引](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) \(英文\)。

當您建立索引時，系統會針對索引結構描述中的每個欄位配置實體儲存體，並為每個可搜尋欄位建立反向索引。 不可搜尋的欄位可用於篩選條件或運算式中，但不會有反向索引且不是可全文或模糊搜尋。 在重建的索引上，這些反向索引會被刪除並根據您提供的索引結構描述來重新建立。

當您載入索引時，每個欄位的反向索引都會填入來自每個文件的唯一 Token 化文字，且包含相對應文件識別碼的對應。 例如，編製旅館資料集的索引時，為 City 欄位建立的反向索引可能會包含 Seattle、Portland 等字詞。 City 欄位中包含 Seattle、Portland 之文件的文件識別碼會列在字詞旁邊。 在進行任何[新增、更新或刪除](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) \(英文\) 作業時，字詞和文件識別碼也會隨之更新。

> [!NOTE]
> 如果您有嚴格的 SLA 需求，您可以考慮專為這項工作佈建新服務，其中開發和索引編製完全獨立於生產環境索引。 個別的服務會在自己的硬體上執行，排除資源競爭的可能性。 當開發完成時，您可以保留新索引，將查詢重新導向至新的端點和索引，或者您也可以執行完成的程式碼，以在您原始 Azure 搜尋服務上發佈修訂過的索引。 目前沒有機制能將準備好的索引移動到其他服務。

## <a name="view-updates"></a>檢視更新

第一個文件載入之後，您就可以開始查詢索引。 如果您知道文件的別碼，[查閱文件 REST API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) \(英文\) 可傳回特定文件。 若要進行更廣泛的測試，您應該等到索引完全載入，然後使用查詢來確認您預期會看到的內容。

## <a name="see-also"></a>請參閱

+ [索引子概觀](search-indexer-overview.md)
+ [大規模索引大型資料集](search-howto-large-index.md)
+ [在入口網站中編製索引](search-import-data-portal.md)
+ [Azure SQL Database 索引子](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB 索引子](search-howto-index-cosmosdb.md)
+ [Azure Blob 儲存體索引子](search-howto-indexing-azure-blob-storage.md)
+ [Azure 資料表儲存體索引子](search-howto-indexing-azure-tables.md)
+ [Azure 搜尋服務中的安全性](search-security-overview.md)