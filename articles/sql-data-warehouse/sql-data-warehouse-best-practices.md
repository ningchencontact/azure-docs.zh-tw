---
title: Azure Synapse Analytics （先前稱為 SQL DW）中 SQL 分析的最佳做法 |Microsoft Docs
description: 在 Azure Synapse Analytics （先前稱為 SQL DW）中開發 SQL 分析解決方案的建議和最佳作法。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: bafa4088ee413e28ec6adee3df20f253aeebb861
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499830"
---
# <a name="best-practices-for-sql-analytics-in-azure-synapse-analytics-formerly-sql-dw"></a>Azure Synapse Analytics （先前稱為 SQL DW）中 SQL 分析的最佳做法

本文是最佳作法的集合，可協助您從[SQL 分析](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse)部署達到最佳效能。  本文的目的是要提供您一些基本指引，並強調重點的重要區域。  每一節都會為您介紹一個概念，然後將您指向更深入探討此概念的詳細文章。 主題順序是依重要性排序。 

## <a name="reduce-cost-with-pause-and-scale"></a>利用暫停和調整來降低成本

如需透過暫停和調整來降低成本的詳細資訊，請參閱[管理計算](sql-data-warehouse-manage-compute-overview.md)。 

## <a name="maintain-statistics"></a>維護統計資料
Azure SQL 資料倉儲可以設定為自動偵測資料行並建立統計資料。  優化工具所建立的查詢計劃，只與可用的統計資料一樣好。  建議您為資料庫啟用 AUTO_CREATE_STATISTICS，並讓統計資料每天或每次載入之後更新，以確保查詢中所使用之資料行的統計資料一律是最新狀態。 

如果您發現更新所有統計資料所花費的時間太長，您可能會想要嘗試進一步選擇哪些資料行需要頻繁的統計資料更新。 例如，您可能想要更新您每天都要加入新值的日期資料行。 **對於涉及聯結的資料行、WHERE 子句中使用的資料行，以及在 GROUP BY 中找到的資料行，您將會獲得最大的好處。**

另請參閱[管理資料表的統計資料][Manage table statistics]、[CREATE STATISTICS][CREATE STATISTICS]、[UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>使用 DMV 對查詢進行監視和最佳化
SQL 分析有數個 Dmv 可用來監視查詢執行。  下列的監視相關文章會逐步解說如何查看執行中查詢的詳細資料。  若要在這些 DMV 中快速找到查詢，可在您的查詢中使用 LABEL 選項。

另請參閱[使用 DMV 監視工作負載][Monitor your workload using DMVs]、[LABEL][LABEL]、[OPTION][OPTION]、[sys.dm_exec_sessions][sys.dm_exec_sessions]、[sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests]、[sys.dm_pdw_request_steps][sys.dm_pdw_request_steps]、[sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests]、[sys.dm_pdw_dms_workers]、[DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN]、[sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="tune-query-performance-with-new-product-enhancements"></a>使用新的產品增強功能調整查詢效能
- [使用具體化視圖進行效能微調](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [使用已排序叢集資料行存放區索引的效能微調](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [使用結果集快取進行效能微調](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="group-insert-statements-into-batches"></a>將 INSERT 陳述式群組為批次
使用 INSERT 陳述式單次載入小型資料表、或甚至定期重新載入查閱，可能會和使用像 `INSERT INTO MyLookup VALUES (1, 'Type 1')`這樣的陳述式一樣正常執行。  不過，如果您一整天都得載入數千或數百萬個資料列，您可能會發現單一 INSERT 跟不上您的需求。  所以，請開發您自己的程序將它們寫入檔案，以及另一個程序定期執行並載入此檔案。

另請參閱 [INSERT][INSERT]

## <a name="use-polybase-to-load-and-export-data-quickly"></a>使用 PolyBase 將資料快速載入及匯出

 SQL 分析支援透過數個工具（包括 Azure Data Factory、PolyBase 和 BCP）載入和匯出資料。  若是小量的資料，效能不是那麼重要，任何工具都可以滿足您的需求。  不過，當您要載入或匯出大量資料，或者需要快速的效能時，PolyBase 是最佳選擇。  
 
 PolyBase 的設計目的是要利用 MPP （大量平行處理）架構，並將資料巨量的載入和匯出速度比任何其他工具更快。  您可使用 CTAS 或 INSERT INTO 來執行 PolyBase 載入。  **使用 CTAS 可以減少交易記錄，是載入資料最快的方法。** 
 
  Azure Data Factory 也支援 PolyBase 載入，並且可以達到與 CTAS 類似的效能。  PolyBase 支援各種不同的檔案格式，包括 Gzip 檔案。  **若要在使用 gzip 文字檔時最大化輸送量，請將檔分成60或更多檔案，以最大化負載的平行處理。**  如需更快的總輸送量，請考慮同時載入資料。

另請參閱[載入資料][Load data]、[使用 PolyBase 的指南][Guide for using PolyBase]、 [SQL 集區載入模式和策略][Azure SQL Data Warehouse loading patterns and strategies]、[使用 Azure Data Factory 載入資料][Load Data with Azure Data Factory]、[使用 Azure Data Factory 移動資料][Move data with Azure Data Factory]、[建立外部檔案格式][CREATE EXTERNAL FILE FORMAT]、[建立table as select （CTAS）][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>載入並查詢外部資料表
雖然 Polybase (也稱為外部資料表) 可能是載入資料最快的方法，卻並非最適合查詢。 Polybase 資料表目前僅支援 Azure blob 檔案和 Azure Data Lake 儲存體。 這些檔案沒有任何支援的計算資源。  

因此，SQL 分析無法卸載此工作，因此必須將整個檔案載入至 tempdb 以讀取資料，以讀取該檔案。  所以，如果您有數個將會查詢此資料的查詢，最好能一次載入此資料，並讓查詢使用本機資料表。

另請參閱 [使用 PolyBase 的指南][Guide for using PolyBase]

## <a name="hash-distribute-large-tables"></a>雜湊分散大型資料表
根據預設，資料表是以「循環配置資源」方式分散。  這可讓使用者更容易開始建立資料表，而不必決定應該如何分散其資料表。  循環配置資源的資料表在某些工作負載中執行良好，但某些狀況下選取分散資料行的執行效能會更好。  依資料行分散資料表的效能遠勝於循環配置資源資料表的最常見例子，是聯結兩個大型事實資料表。  

例如，如果您有一個依 order_id 分散的訂單資料表，以及一個也是依 order_id 分散的交易資料表，當您將訂單資料聯結至交易資料表上的 order_id，此查詢會變成傳遞查詢，也就是資料移動作業會被消除。  較少的步驟代表較快的查詢。  較少的資料移動也會讓查詢更快。  

載入分散的資料表時，請確定您的內送資料的分散式索引鍵沒有排序，因為這會拖慢載入。  如需有關如何選取散發資料行來改善效能，以及如何在 CREATE TABLE 語句的 WITH 子句中定義分散式資料表的詳細資訊，請參閱下列連結。

另請參閱[資料表概觀][Table overview]、[資料表散發][Table distribution]、[選取資料表散發][Selecting table distribution]、[CREATE TABLE][CREATE TABLE]、[CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>不要過度執行資料分割
雖然分割資料可以透過分割區切換或使用分割區刪除來優化掃描，而有效地維護您的資料，但有太多資料分割可能會使查詢變慢。  通常，高細微性的資料分割策略（可能適用于 SQL Server 在 SQL 分析中可能無法順利運作）。  

如果每個資料分割的資料列少於 1 百萬，太多個資料分割也會減少叢集資料行存放區索引的效率。  請記住，在幕後，SQL 分析會將您的資料分割成60資料庫，因此，如果您建立具有100個磁碟分割的資料表，這實際上會導致6000個磁碟分割。  

每個工作負載都不同，因此最佳建議是嘗試不同的分割，找出最適合您工作負載的分割。  請考慮比您的 SQL Server 上運作良好的資料粒度更低的粒度。  例如，考慮使用每週或每月資料分割，而不是每日資料分割。

另請參閱 [資料表分割][Table partitioning]

## <a name="minimize-transaction-sizes"></a>將交易大小最小化
在交易中執行的 INSERT、UPDATE、DELETE 陳述式，失敗時必須回復。  為了將長時間回復的可能性降到最低，請盡可能將交易大小最小化。  這可以透過將 INSERT、UPDATE、DELETE 陳述式分成小部分來達成。  

例如，如果您有預期會花費1小時的插入，可能的話，請將插入分成四個部分，每個元件會在15分鐘內執行。  利用特殊的最低限度記錄案例，像是 CTAS、TRUNCATE、DROP TABLE 或 INSERT 空資料表，來降低回復的風險。  

另一個消除回復的作法是使用「僅中繼資料」作業 (像是資料分割切換) 進行資料管理。  例如，不要執行 DELETE 陳述式來刪除資料表中所有 order_date 為 2001 年 10 月的資料列，而是將資料每月分割後，再從另一個資料表將有空分割之資料的分割調動出來 (請參閱 ALTER TABLE 範例)。  

針對未分割的資料表，請考慮使用 CTAS 來寫入您想要保留在資料表中的資料，而不是使用 DELETE。  如果 CTAS 採用相同的時間量，則執行的作業會更安全，因為它具有最少的交易記錄，而且可以視需要快速取消。

另請參閱[了解交易][Understanding transactions]、[最佳化交易][Optimizing transactions]、[資料表分割][Table partitioning]、[TRUNCATE TABLE][TRUNCATE TABLE]、[ALTER TABLE][ALTER TABLE]、[Create table as select (CTAS)][Create table as select (CTAS)]

## <a name="reduce-query-result-sizes"></a>減少查詢結果大小  
此步驟可協助您避免由大型查詢結果造成的用戶端問題。  您可以編輯您的查詢，以減少傳回的資料列數目。 某些查詢產生工具可讓您在每個查詢中加入 "top N" 語法。  您也可以將查詢結果 CETAS 至臨時表，然後使用 PolyBase export 進行舊版處理。

## <a name="use-the-smallest-possible-column-size"></a>使用最小的可能資料行大小
定義 DDL 時，使用可支援您資料的最小資料類型，將可改善查詢效能。  這對 CHAR 和 VARCHAR 資料行尤其重要。  如果資料行中最長的值是 25 個字元，請將您的資料行定義為 VARCHAR(25)。  避免將所有字元資料行定義為較大的預設長度。  此外，將資料行定義為 VARCHAR (當它只需要這樣的大小時) 而非 NVARCHAR。

另請參閱[資料表概觀][Table overview]、[資料表的資料類型][Table data types]、[CREATE TABLE][CREATE TABLE]

## <a name="use-temporary-heap-tables-for-transient-data"></a>針對暫時性資料使用暫存堆積資料表
當您暫時登陸資料時，您可能會發現使用堆積資料表會讓整個程式更快速。  如果您載入資料只是在做執行更多轉換之前的預備，將資料表載入堆積資料表將會遠快於將資料載入叢集資料行存放區資料表。  

此外，將資料載入暫存資料表也會比將資料表載入永久儲存體更快速。  臨時表以「#」開頭，而且只能由建立它的會話存取，因此只能在有限的情況下使用。   堆積資料表是在 CREATE TABLE 的 WITH 子句中定義。  如果您使用暫存資料表，請記得也在該暫存資料表上建立統計資料。

另請參閱[暫存資料表][Temporary tables]、[CREATE TABLE][CREATE TABLE]、[CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="optimize-clustered-columnstore-tables"></a>將叢集資料行存放區資料表最佳化
叢集資料行存放區索引是您可以在 SQL 分析中儲存資料的其中一個最有效率的方式。  根據預設，SQL 分析中的資料表會建立為叢集資料行存放區。  為了讓資料行存放區資料表的查詢獲得最佳效能，良好的區段品質很重要。  

當資料列在記憶體不足的狀態下寫入資料行存放區資料表時，資料行存放區區段品質可能會降低。  壓縮的資料列群組中的資料列數目可以測量區段品質。  如需偵測和改善叢集資料行存放區資料表區段品質的逐步指示，請參閱[資料表索引][Causes of poor columnstore index quality]一文中的[資料行存放區索引品質不佳的原因][Table indexes]。  

由於高品質的資料行存放區區段很重要，因此最好使用中型或大型資源類別中的使用者識別碼來載入資料。 使用較低的[資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)，表示您想要將更大型的資源類別指派給正在載入的使用者。

由於資料行存放區資料表通常不會將資料推送到壓縮的資料行存放區區段中，直到每個資料表有超過1000000個數據列，而且每個 SQL 分析資料表都分割成60個數據表時，資料行存放區資料表不會受益于查詢，除非資料表有超過60000000個數據列。  小於 6 千萬列的資料表使用資料行存放區索引似乎不太合理，  但也無傷大雅。  

此外，如果您將資料分割，則您要考慮的是每個資料分割必須有 1 百萬個資料列，使用叢集資料行存放區索引才有益。  如果資料表有 100 個分割，則它至少必須擁有 60 億個資料列才會受益於叢集資料行存放區 (60 個散發 * 100 個分割 * 1 百萬個資料列)。  

如果在此範例中，您的資料表並沒有 60 億個資料列，請減少資料分割數目，或考慮改用堆積資料表。  使用次要索引搭配堆積資料表而不是資料行存放區資料表，也可能是值得進行的實驗，看看是否可以獲得較佳的效能。

查詢資料行存放區資料表時，如果您只選取您需要的資料行，查詢執行會更快速。  

另請參閱[資料表索引][Table indexes]、[資料行存放區索引指南][Columnstore indexes guide]、[重建資料行存放區索引][Rebuilding columnstore indexes]

## <a name="use-larger-resource-class-to-improve-query-performance"></a>使用較大的資源類別來改善查詢效能
SQL 分析會使用資源群組做為將記憶體配置給查詢的方式。  根據預設，所有使用者都會指派給小型資源類別，每個散發會授與 100 MB 的記憶體。  因為永遠會有 60 個散發，每個散發有至少 100 MB，整個系統的總記憶體配置為 6000 MB 或是剛好接近 6 GB。  

有些查詢，像是大型聯結或載入叢集資料行存放區資料表，將受益於較大的記憶體配置。  有些查詢，像是純掃描，則沒有任何好處。  另一方面，使用較大的資源類別可減少平行存取，因此您必須先將這項影響納入考慮，再將所有使用者移至大型資源類別。

另請參閱[適用於工作負載管理的資源類別](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>使用較小的資源類別來增加並行存取
如果您注意到使用者查詢似乎延遲很長，可能是您的使用者在較大的資源類別中執行，而且耗用許多並行位置，導致其他查詢排入佇列。  若要確認使用者的查詢是否被排入佇列，請執行 `SELECT * FROM sys.dm_pdw_waits` 看看是否會傳回任何資料列。

另請參閱[適用于工作負載管理的資源類別](resource-classes-for-workload-management.md)， [sys. dm_pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>其他資源
另請參閱 [疑難排解][Troubleshooting] 一文中的常見問題和解決方案。

如果您在本文中找不到您要尋找的內容，請嘗試使用此頁面左側的「搜尋檔」來搜尋所有的 Azure Synapse 檔。  [Azure Synapse 論壇][Azure SQL Data Warehouse MSDN Forum]可讓您向其他使用者和 Azure Synapse 產品小組提出問題。 

我們會主動監看這個論壇，以確保您的問題有其他使用者或是我們回答。  如果您想要在 Stack Overflow 上詢問問題，我們也有[Azure Synapse Stack Overflow 論壇][Azure SQL Data Warehouse Stack Overflow Forum]。

最後，請使用[Azure Synapse 意見][Azure SQL Data Warehouse Feedback]反應頁面來提出功能要求。  加入您的要求或票選其他要求確實可協助我們決定功能的優先順序。

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ./load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
