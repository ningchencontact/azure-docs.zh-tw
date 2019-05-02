---
title: Azure SQL 資料倉儲版本資訊 | Microsoft Docs
description: Azure SQL 資料倉儲的版本資訊。
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/10/2019
author: anumjs
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.openlocfilehash: 4c5279d1ddf3153493ebc01dc010114ff7e6b5e7
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917233"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Azure SQL 資料倉儲版本資訊

本文將摘要說明 [Azure SQL 資料倉儲](sql-data-warehouse-overview-what-is.md)最新版本中的新功能與改進功能。 本文也會列出值得注意不直接相關的發行版本但發行相同的時間範圍內的內容更新。 其他 Azure 服務的增強功能，請參閱 <<c0> [ 服務更新](https://azure.microsoft.com/updates)。

## <a name="march-2019"></a>2019 年 3 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**現在可供 Gen2 預覽工作負載重要性**|工作負載的重要性會讓資料工程師能夠使用重要性來分類要求。 以高重要性的要求保證快速地存取資源，進而符合 Sla。  工作負載重要性可讓高商業值工作，以符合 Sla，在共用環境中使用較少的資源。<br/><br/>工作負載管理分類和重要性的 preview 是組建與發行日期 2019 年 4 月 9 日的或更新版本。 使用者應該避免使用早於此日期的組建進行工作負載管理測試。 若要判斷您的組建是否工作負載管理功能，請執行`select @@version`當連接到您的 SQL 資料倉儲執行個體。</br></br>如需有關工作負載重要性的詳細資訊，請參閱[分類](sql-data-warehouse-workload-classification.md)並[重要性](sql-data-warehouse-workload-importance.md)文件中的概觀文章。 請參閱[建立工作負載分類](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)以及文件。<br/><br/>請參閱中的作用中的工作負載重要性以下影片：<br/>[工作負載管理概念](  https://www.youtube.com/embed/QcCRBAhoXpM)<br/>[工作負載管理案例](https://www.youtube.com/embed/_2rLMljOjw8)|
|**資料探索與分類**|Azure SQL 資料倉儲的資料探索與分類現在提供公開預覽。 請務必保護敏感資料與客戶的隱私權。 隨著您的業務和客戶的資料資產的擴增，變得難以管理，來探索、 分類及保護您的資料。 我們使用 Azure SQL 資料倉儲原生導入的資料探索與分類功能，有助於保護您的資料更容易管理。 這個功能的整體優點包括：<br/>&bull; &nbsp; 會議資料隱私權標準和法規合規性需求。<br/>&bull; &nbsp; 限制存取權，並強化安全性的資料倉儲包含高度敏感性資料。<br/>&bull; &nbsp; 監視和警示的異常存取敏感性資料。<br/>&bull; &nbsp; 在 Azure 入口網站上的中央儀表板中的敏感性資料的視覺效果。 </br></br>資料探索與分類是適用於 Azure SQL 資料倉儲中所有的 Azure 區域，它的進階資料安全性，包括弱點評定和威脅偵測的一部分。 如需有關資料探索與分類的詳細資訊，請參閱 <<c0> [ 部落格文章](https://azure.microsoft.com/blog/announcing-public-preview-of-data-discovery-classification-for-microsoft-azure-sql-data-warehouse/)與我們的線上[文件](/azure/sql-database/sql-database-data-discovery-and-classification)。|
|**GROUP BY 彙總套件**|彙總套件現在是支援的 GROUP BY 選項在 Azure 資料倉儲中。   GROUP BY ROLLUP 會建立每個資料行運算式組合的群組。 GROUP BY 也 「 積存 」 結果到小計和總計。 GROUP BY 函式會處理由右至左，減少對其建立群組和從右向的資料行運算式數目。  資料行順序會影響 ROLLUP 的輸出，而且可能會影響結果集中的資料列數目。<br/><br/>如需有關群組的彙總的詳細資訊，請參閱[GROUP BY & Amp;#40;transact-SQL&AMP;#41;](/sql/t-sql/queries/select-group-by-transact-sql?view=azure-sqldw-latest)
|**改善的精確度，已使用的 DWU 和 CPU 入口網站計量**|SQL 資料倉儲大幅增強 Azure 入口網站計量的精確度。  此版本包含的修正程式的 CPU 和 Dwu 計量定義，以正確反映所有計算節點的 您的工作負載。 此修正之前, 已正在 undereported 度量值。 預期會看見已使用的 DWU 增加和 Azure 入口網站中的 CPU 計量。 |
|**資料列層級安全性**|我們在 2017 年 11 月引進的資料列層級安全性功能。 我們現在已擴充這項支援，以及外部資料表。 此外，我們已新增支援呼叫內嵌資料表值函數 （內嵌 Tvf） 定義的安全性篩選器述詞所需的不具決定性的函式。 此新增可讓您指定 IS_ROLEMEMBER()，user_name （） 等等。 在 安全性篩選器述詞。 如需詳細資訊，請參閱中的範例[資料列層級安全性文件](/sql/relational-databases/security/row-level-security)。|
|**其他的 T-SQL 支援**|SQL 資料倉儲的 T-SQL 語言介面區已經擴充成包含對[STRING_SPLIT & Amp;#40;transact-SQL&AMP;#41;](/sql/t-sql/functions/string-split-transact-sql)。
|**查詢最佳化工具增強功能** |查詢最佳化是任何資料庫的重要元件。 如何最適合執行查詢的最佳選擇可能會產生明顯的改進。  當分散式環境中執行複雜的分析查詢，作業數目就會執行大小事。 已產生更好的品質計畫增強查詢效能。 這些計劃降低昂貴的資料傳輸作業，例如備援計算重複的子查詢。 如需詳細資訊，請參閱此 Azure SQL 資料倉儲[部落格文章](https://azure.microsoft.com/blog/smarter-faster-safer-azure-sql-data-warehouse-is-simply-unmatched/)。|
| | |

### <a name="documentation-improvements"></a>文件改進

| 文件改進 | 詳細資料 |
| --- | --- |
| | |

## <a name="january-2019"></a>2019 年 1 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**傳回 Order By 最佳化**|SELECT…ORDER BY 查詢會在此版本中獲得效能提升。   現在，所有計算節點會將其結果傳送至單一計算節點。 此節點將合併和排序結果，然後加以傳回給使用者。  當查詢結果集包含大量資料列時，透過單一計算節點進行合併會讓效能顯著提升。 先前，查詢執行引擎會排列在每個計算節點上的結果。 結果將它們串流處理到控制節點。 控制節點會然後合併結果。|
|**適用於 PartitionMove 和 BroadcastMove 的資料移動增強功能**|在 Azure SQL 資料倉儲 Gen2 的型別 ShuffleMove，資料移動步驟會使用即時的資料移動技術。  如需詳細資訊，請參閱 <<c0> [ 效能增強功能的部落格](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)。 此版本中，PartitionMove 和 BroadcastMove 是現在會由相同的即時的資料移動技術提供。 使用這些類型的資料移動步驟的使用者查詢會執行更佳的效能。 您無須變更任何程式碼，即可享有這些改進的效能。|
|**值得注意的錯誤 (bug)**|Azure SQL 資料倉儲版本不正確-`SELECT @@VERSION`可能傳回不正確的版本，10.0.9999.0。 目前所發行的正確版本是 10.0.10106.0。 此錯誤 (bug) 已經被回報並正在進行檢閱。
| | |

### <a name="documentation-improvements"></a>文件改進

| 文件改進 | 詳細資料 |
| --- | --- |
|None | |
| | |

## <a name="december-2018"></a>2018 年 12 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**正式運作的虛擬網路服務端點**|此版本具有所有 Azure 區域內 Azure SQL 資料倉儲虛擬網路 (VNet) 服務端點的一般可用性。 VNet 服務端點讓您可隔離連線，從虛擬網路內的指定子網路或一組子網路連線到邏輯伺服器。 從 VNet 傳送至 Azure SQL 資料倉儲的流量一律會保留在 Azure 骨幹網路中。 這會優先使用此直接路由，而不是透過虛擬設備或內部部署傳送網際網路流量的任何特定路由。 透過服務端點存取虛擬網路並不會額外收費。 [Azure SQL 資料倉儲](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)的目前定價模型照常適用。<br/><br/>透過此版本，我們也讓 PolyBase 能夠透過 [Azure Blob File System](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver) (ABFS) 驅動程式連線到 [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (ADLS)。 Azure Data Lake Storage Gen2 所具備的品質，足以滿足 Azure 儲存體分析資料完整生命週期的一切所需。 兩個現有 Azure 儲存體服務 (Azure Blob 儲存體與 Azure Data Lake Storage Gen1) 的功能均涵蓋在內。 [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index) 的功能 (例如檔案系統語意、檔案層級安全性和級別) 結合了 [Azure Blob 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)的低成本、分層式儲存體、高可用性/災害復原功能。<br/><br/>透過使用 Polybase，您也可將資料從固定於 VNet 的 Azure 儲存體匯入到 Azure SQL 資料倉儲。 同樣的，也支援經由 PolyBase 將資料從 Azure SQL 資料倉儲匯出至固定於 VNet 的 Azure 儲存體。<br/><br/>如需 Azure SQL 資料倉儲中 VNet 服務端點的詳細資訊，請參閱完整的[部落格文章](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/) (英文) 或[文件](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json)。|
|**自動的效能監視 （預覽）**|[查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017)目前已可在 Azure SQL 資料倉儲預覽版中使用。 查詢存放區的設計目的是透過追蹤查詢、查詢計劃、執行階段統計資料以及查詢記錄等方式，協助您監視資料倉儲的活動與效能，藉此排解查詢效能的問題。 查詢存放區是一組內容存放區與「動態管理檢視」(DMV)，具備以下功能：<br/><br/>&bull; &nbsp; 找出並調整熱門資源取用查詢<br/>&bull; &nbsp; 找出並改善未規劃的工作負載<br/>&bull; &nbsp; 統計資料、 索引或系統的大小 （DWU 設定） 中的變更來評估查詢效能和計劃的影響<br/>&bull; &nbsp; 請參閱完整的查詢文字，以執行的所有查詢<br/><br/>查詢存放區包含三個實際存放區：<br/>&bull; &nbsp; 計劃存放區來保存執行計劃資訊<br/>&bull; &nbsp; 執行階段統計資料存放區來保存執行統計資料資訊<br/>&bull; &nbsp; 以保存等候統計資料存放區等候統計資料資訊。<br/><br/>SQL 資料倉儲會自動管理這些存放區，並提供無限的數量的查詢 storied 過去七天，不需要額外收費。 啟用查詢存放區很簡單，只要執行 ALTER DATABASE T-SQL 陳述式即可： <br/>sql---ALTER DATABASE [DatabaseName] 設定 QUERY_STORE = ON;---如需有關 Azure SQL 資料倉儲中的查詢存放區的詳細資訊，請參閱本文中，[使用查詢存放區來監視效能](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)，和查詢存放區 Dmv，例如[sys.query_store_query](/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql)。 以下是版本發佈的[部落格文章](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/) (英文)。|
|**Azure SQL 資料倉儲 Gen2 的較低的計算層級**|Azure SQL 資料倉儲 Gen2 現可支援較低的支援層級。 客戶可從 100 cDWU ([資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)) 開始使用，在數分鐘內調整為 30,000 cDWU，體驗 Azure SQL 資料倉儲卓越的效能、彈性及安全性功能。 自 2018 年 12 月中起，這些[地區](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)的客戶即可在較低的運算層級享有 Gen2 的效能和彈性，其餘地區則會在 2019 年跟進。<br/><br/>注重價值的客戶希望能全面評估安全的高效能資料倉儲的所有益處，且不想要猜測決定最適合的試用環境，而 Microsoft 降低了次世代資料倉儲服務的入門門檻，對這些客戶敞開了大門。 客戶最低可從 100 cDWU 開始，比目前 500 cDWU 的入門門檻更低。 SQL 資料倉儲 Gen2 會進一步支援暫停和繼續的作業，不單只將彈性侷限在運算方面。 Gen2 也支援無限制資料行存放區的儲存體容量，每個查詢的記憶體增加 2.5 倍，最多可達 128 個並行查詢，還具備[調適性快取](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/)功能。 與同價格的 Gen1 一樣的資料倉儲單位相比，上述功能的效能平均高出五倍。 在保證內建資料保護功能的 Gen2 中，異地備援備份是標準功能。 Azure SQL Data Warehouse Gen2 時刻做好充足準備，只待您開始調整級別。|
|**資料行存放區背景合併**|根據預設，Azure SQL 資料倉儲 (Azure SQL DW) 會使用稱為[資料列群組](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)的微型分割，以資料行格式儲存資料。 有時候，因為在建置索引或載入資料時記憶體受到限制，資料列群組可能會經過壓縮，且使用的壓縮大小低於最佳的 1 百萬個資料列。 資料列群組也可能會因為刪除操作而變得零碎。 小型或零碎的資料列群組會耗用更高的記憶體，執行查詢時的效率也不好。 使用這一版的 Azure SQL DW 時，資料行存放區的背景維護工作會合併經過壓縮的小型資料列群組，以建立較大型的資料列群組，從而更加充分地利用記憶體並加快查詢的執行速度。
| | |

## <a name="october-2018"></a>2018 年 10 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**資料倉儲的 DevOps**|SQL 資料倉儲 (SQL DW) 高度要求的功能目前為預覽狀態，Visual Studio 中支援資料工具 (SSDT)！ 開發人員小組現在可以透過單一的版本控制程式碼基底執行協同作業，並快速地將變更部署到全球任何執行個體。 有興趣加入嗎？ 此功能目前可供預覽！ 您可以透過造訪 [SQL 資料倉儲 Visual Studio SQL Server Data Tools (SSDT) - 預覽註冊表單](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u)進行註冊。 鑑於需求量很大，我們正在對預覽進行驗收，以確保為客戶提供最佳體驗。 註冊後，我們的目標是在七個工作日內確認您的狀態。|
|**資料列層級安全性全面上市**|Azure SQL 資料倉儲 (SQL DW) 現在支援資料列層級安全性 (RLS)，增加了保護機密資料的強大功能。 藉由引進 RLS，您可以實作安全性原則來控制對資料表中資料列的存取，例如誰可以存取哪些資料列。 RLS 支援這種更細緻的存取控制，而不必重新設計您的資料倉儲。 RLS 簡化了整體安全性模型，因為存取限制邏輯位於資料庫層本身，而不是遠離另一個應用程式中的資料。 RLS 還消除了導入檢視以篩選掉用於存取控制管理之資料列的需要。 我們為所有客戶提供此企業級安全性功能，無需任何額外的費用。|
|**進階的建議程式**|Azure SQL 資料倉儲 (SQL DW) 的進階微調透過額外的資料倉儲相關建議和計量變得更加簡單。 Azure Advisor 還提供其他進階的效能建議，包括：<br/><br/>1.自適性快取 – 取得何時調整規模以發揮最佳快取使用率的建議。<br/>2.資料表散發 – 判斷何時複寫資料表以減少資料移動並提升工作負載效能。<br/>3.Tempdb – 了解何時調整和設定資源類別，以減少 tempdb 競爭。<br/><br/>資料倉儲計量與 [Azure 監視器](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/)的整合更加深入，包括功能更強的可自訂監視圖表，能在概觀刀鋒視窗中提供近乎即時的計量。 當您監視使用量，或是驗證並套用資料倉儲建議時，不再需要離開資料倉儲概觀刀鋒視窗，即可存取 Azure 監視器計量。 此外，也提供新的計量 (例如 tempdb 及自適性快取使用率)，作為您效能建議的補充。|
|**進階微調使用整合式的建議程式**|Azure SQL 資料倉儲 (SQL DW) 的進階微調透過額外的資料倉儲相關建議和計量以及入口網站概觀刀鋒視窗的重新設計變得更加簡單，該刀鋒視窗提供了 Azure Advisor 和 Azure 監視器的整合式體驗。|
|**加速的資料庫復原 (ADR)**|Azure SQL 資料倉儲加速資料庫復原 (ADR) 現在處於公開預覽狀態。 ADR 是新的 SQL 資料庫引擎功能，藉由完整重新設計目前的復原程序，來大幅改善資料庫可用性 (特別是在有長時間執行的交易時)。 ADR 的主要優點是快速且一致的資料庫復原，以及即時的交易回復。|
|**Azure 監視器診斷記錄檔**|藉由直接與 Azure 監視器診斷記錄整合，SQL 資料倉儲 (SQL DW) 現在提供更強的分析工作負載見解。 這項新功能可讓開發人員分析更長一段時間內的工作負載行為，並在充分了解資訊的情況下，做出查詢最佳化或容量管理決策。 我們現在透過 [Azure 監視器診斷記錄](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs)引進了外部的記錄處理程序，這些記錄提供了有關資料倉儲工作負載的額外見解。 只要按一下按鈕，您就可以使用 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries) 為歷程查詢效能疑難排解功能設定診斷記錄。 Azure 監視器診斷記錄透過將記錄儲存至儲存體帳戶以供稽核，來支援可自訂的保留期間；也支援將記錄串流至事件中樞，以獲得近乎即時的遙測深入解析功能；以及使用具有記錄查詢之 Log Analytics 來分析記錄的功能。 診斷記錄是由您資料倉儲的遙測檢視所組成，其等同於 SQL 資料倉儲最常使用的效能疑難排解 DMV。 我們已為此初始版本啟用下列系統動態管理檢視的檢視：<br/><br/>&bull; &nbsp; [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)|
|**資料行存放區的記憶體管理**|隨著壓縮的資料行存放區資料列群組數目增加，管理這些資料列群組的內部資料行區段中繼資料所需的記憶體會隨之增加。  因此，查詢效能和針對某些資料行存放區動態管理檢視 (Dmv) 所執行的查詢可能會降級。  此版本已進行改善，讓這些情況的內部中繼資料大小達到最佳化，進而改善這類查詢的體驗和效能。|
|**Azure Data Lake 儲存體 Gen2 整合版 (GA**|Azure SQL 資料倉儲 (SQL DW) 現在已與 Azure Data Lake Storage Gen2 原生整合。 客戶現在可以使用 ABFS 中的外部資料表將資料載入至 SQL DW。 這項功能可讓客戶與其 Data Lake Storage Gen2 中的 Data Lake 整合。|
|**值得注意的錯誤 (bug)**|Parquet 資料倉儲上的小型資源類別，DW2000 等等-失敗的 CETAS 這個修正程式正確地識別 Parquet 程式碼路徑中建立外部資料表為 null 參考。<br/><br/>識別資料行值可能會遺失一些 CTAS 作業中的識別資料行的值可能不會保留當 CTASed 到另一個資料表。 在報告[部落格](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/)。<br/><br/>在某些情況下，當查詢仍在執行中-此修正觸發 InvalidOperationException，如果查詢仍在執行時，工作階段就會終止而終止工作階段的內部失敗。<br/><br/>（部署在 2018 年 11 月）嘗試載入多個小型檔案從 ADLS (Gen1) 使用 Polybase 時，客戶所遇到次佳的效能。 系統效能的瓶頸在 AAD 安全性權杖驗證期間。 效能問題已藉由啟用安全性權杖的快取來降低影響。 |
| | |

## <a name="next-steps"></a>後續步驟

- [建立 SQL 資料倉儲](./create-data-warehouse-portal.md)

## <a name="more-information"></a>詳細資訊

- [部落格 - Azure SQL 資料倉儲](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/) \(英文\)
- [客戶諮詢小組部落格](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [客戶成功案例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [影片](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 詞彙](../azure-glossary-cloud-terminology.md)
