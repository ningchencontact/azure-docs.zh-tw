---
title: 監視和效能微調 - Azure SQL Database | Microsoft Docs
description: 透過評估和改進來調整 Azure SQL Database 效能的秘訣。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: ee4bd9d61856ef4ea1afdd027d6f39e730b92d78
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129210"
---
# <a name="monitoring-and-performance-tuning"></a>監視和效能微調

Azure SQL Database 提供工具和方法, 可輕鬆地監視使用方式、新增或移除資源 (CPU、記憶體、i/o)、針對潛在問題進行疑難排解, 以及提出改善資料庫效能的建議。 Azure SQL Database 具有可自動修正資料庫中問題的功能。 自動調整可讓資料庫適應工作負載, 並自動優化效能。 不過, 有些自訂問題可能需要進行疑難排解。 本文說明一些可用於針對效能問題進行疑難排解的最佳做法和工具。

有兩個主要的活動應完成, 以確保資料庫在執行時不會發生問題:
- [監視資料庫效能](#monitoring-database-performance), 確保指派給資料庫的資源可以處理工作負載。 如果您看到資料庫達到資源限制, 請考慮:
   - 識別和優化熱門資源取用查詢。
   - 藉由升級服務層來新增更多資源。
- 針對[效能問題進行疑難排解](#troubleshoot-performance-issues), 找出可能發生問題的原因, 找出問題的根本原因。 根本原因一旦決定了修正問題的步驟即可。

## <a name="monitoring-database-performance"></a>監視資料庫效能

監視 Azure 中 SQL database 的效能, 一開始會監視相對於所選資料庫效能層級的資源。 應監視下列資源:
 - **Cpu 使用量**-檢查資料庫是否長時間達到 100% 的 CPU 使用量。 高 CPU 使用率可能表示應該識別並微調使用最多計算能力的查詢。 或者, 高 CPU 使用率可能表示資料庫或實例應該升級至較高的服務層級。 
 - **等候統計資料**: 使用[_os_wait_stats (transact-sql)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)來判斷查詢遇到的等候。 查詢可以等候資源、佇列等候或外部等候。 
 - **Io 使用量**-檢查資料庫是否達到基礎儲存體的 IO 限制。
 - **記憶體使用量**-資料庫或實例可用的記憶體數量與虛擬核心的數目成正比。 確認記憶體足以應付工作負載。 分頁生命週期是其中一個參數, 可表示從記憶體中移除頁面的速度。

Azure SQL Database 服務**包含工具和資源, 可協助疑難排解並修正潛在的效能問題**。 您可以藉由查看[效能微調建議](sql-database-advisor.md), 識別機會來改善和優化查詢效能, 而不需要變更資源。 遺漏索引與查詢最佳化不足是資料庫效能不佳的常見原因。 您可以套用這些微調建議, 以改善工作負載的效能。 我們也可以藉由套用所有已識別的建議並確認建議改善資料庫效能, 讓 Azure SQL database[自動優化查詢的效能](sql-database-automatic-tuning.md)。

下列選項可用於監視和疑難排解資料庫效能:

- 在 [Azure 入口網站](https://portal.azure.com)中, 按一下 [ **SQL 資料庫**], 選取資料庫, 然後使用 [監視] 圖表來尋找已接近其最大使用率的資源。 預設會顯示 DTU 耗用量。 按一下 [編輯] 來變更所顯示的時間範圍和值。
- SQL Server Management Studio 之類的工具提供許多有用的報表, 例如[效能儀表板](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard?view=sql-server-2017), 用來監視資源使用率, 並識別最常使用的資源查詢。[查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed)可以用來識別具有回歸效能的查詢。
- 使用[Azure 入口網站](https://portal.azure.com)中的[查詢效能深入解析](sql-database-query-performance.md)來識別利用大部分資源的查詢。 這項功能僅適用于單一資料庫和彈性集區。
- 使用 [SQL Database Advisor](sql-database-advisor-portal.md) 來檢視建立和卸除索引、參數化查詢，以及修正結構描述問題的建議。 這項功能僅適用于單一資料庫和彈性集區。
- 使用[AZURE SQL Intelligent Insights](sql-database-intelligent-insights.md)來自動監視資料庫效能。 在偵測到效能問題之後，系統就會產生包含問題詳細資料和「根本原因分析」(RCA) 的診斷記錄。 在可能的情況下會提供效能改進建議。
- [啟用自動調整](sql-database-automatic-tuning-enable.md)並讓 Azure SQL 資料庫自動修正發現的效能問題。
- 使用[動態管理檢視 (DMV)](sql-database-monitoring-with-dmvs.md)、[擴充的事件](sql-database-xevent-db-diff-from-svr.md)和[查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)，更深入了解如何進行效能問題的疑難排解。

> [!TIP]
> 使用上述一或多個方法識別出效能問題之後，請參閱[效能指引](sql-database-performance-guidance.md)以尋找可用來改善 Azure SQL Database 效能的技術。

## <a name="troubleshoot-performance-issues"></a>針對效能問題進行疑難排解

若要診斷並解決效能問題，一開始先了解每個作用中查詢的狀態，以及導致與每個工作負載狀態相關之效能問題的情況。 若要改善 Azure SQL Database 效能, 請瞭解來自應用程式的每個使用中查詢要求都處於執行中或等待中狀態。 當您針對 Azure SQL Database 中的效能問題進行疑難排解時, 請記住下列圖表, 因為我們已閱讀本文, 以診斷並解決效能問題。

![工作負載狀態](./media/sql-database-monitor-tune-overview/workload-states.png)

對於發生效能問題的工作負載，效能問題可能是來自 CPU 爭用 (**與執行相關**的狀況)，或是個別查詢正在等候某些內容 (**與等候相關**的狀況)。

執行**相關**問題的原因可能是:
- **編譯問題**-SQL 查詢最佳化工具可能會因為過時的統計資料、不正確估計要處理的資料列數目, 或所需記憶體的估計值, 而產生不佳的計畫。 如果我們知道查詢是在過去或其他實例 (受控執行個體或 SQL Server 實例) 執行得更快, 請採用實際執行計畫並加以比較, 以查看它們是否不同。 請嘗試套用查詢提示或重建統計資料, 或重建索引以取得更好的計畫。 請在 Azure SQL Database 中啟用自動計畫更正, 以自動減輕這些問題。
- **執行問題**-如果查詢計劃是最佳的, 則可能會達到資料庫中的資源限制, 例如記錄寫入輸送量, 或使用應該重建的分散索引。 許多需要相同資源的並行查詢, 也可能是執行問題的原因。 大部分案例中的**等候相關**問題都與執行問題相關, 因為未有效率地執行的查詢可能會等候某些資源。

**等待相關**問題的原因可能是:
- **封鎖**: 一個查詢可能會持有資料庫中物件的鎖定, 而有些則嘗試存取相同的物件。 使用 Dmv 或監視工具可以輕鬆地識別封鎖查詢。
- **IO 問題**-查詢可能正在等候頁面寫入資料或記錄檔。 在此情況下`INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG`請參閱`PAGEIOLATCH_*` DMV 中的、或等候統計資料。
- **TempDB 問題**-如果工作負載使用臨時表, 或在計畫中有 tempdb 溢出, 則查詢可能會有 tempdb 輸送量的問題。 
- **記憶體相關問題**-可能沒有足夠的記憶體可供工作負載使用, 因此頁面生命預期可能會下降, 或查詢所取得的記憶體比所需的還要少。 在某些情況下, 查詢最佳化工具中的內建智慧功能將會修正這些問題。
 
 下列各節將說明如何識別和疑難排解其中一些問題。

## <a name="running-related-performance-issues"></a>執行相關的效能問題

一般來說, 如果 CPU 使用率一致等於或高於 80%, 則會有執行相關的效能問題。 如果有執行相關的問題, 可能是因為 CPU 資源不足, 或可能與下列其中一個情況有關:

- 太多執行的查詢
- 太多編譯的查詢
- 一或多個執行中查詢使用的是次佳查詢計劃

如果判斷有執行相關的效能問題, 其目標就是使用一或多個方法來找出精確的問題。 用來識別執行相關問題的最常見方法如下：

- 使用 [Azure 入口網站](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal)來監視 CPU 百分比使用率。
- 使用下列[動態管理檢視](sql-database-monitoring-with-dmvs.md)：

  - [_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use)會傳回 AZURE SQL DATABASE 的 CPU、i/o 和記憶體耗用量。 每隔15秒的間隔會有一個資料列, 即使資料庫中沒有活動也一樣。 歷程記錄資料會保留一個小時。
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) 會傳回 Azure SQL Database 的 CPU 使用量和儲存體資料。 每五分鐘會收集和彙總資料一次。

> [!IMPORTANT]
> 如需使用 _db_resource_stats 和 resource_stats Dmv 的 T-SQL 查詢, 以針對 CPU 使用率問題進行疑難排解, 請參閱[找出 cpu 效能問題](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues)。

### <a name="ParamSniffing"></a>針對具有參數敏感性查詢執行計畫問題的查詢進行疑難排解

參數區分計畫 (PSP) 問題是指查詢最佳化工具產生僅適用於特定參數值 (或一組值) 的查詢執行計畫的情況，然後快取計畫不適用於連續執行中使用的參數值。 然後，非最佳計畫可能會導致查詢效能問題和整體工作負載輸送量降低。 如需參數探查和查詢處理的詳細資訊, 請參閱[查詢處理架構指南](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)。

有數個因應措施可用來減輕這些問題, 每個都有相關聯的取捨和缺點:

- 在每次查詢執行時使用 [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查詢提示。 此因應措施會將編譯時間和增加的 CPU, 以獲得更好的計畫品質。 針對需要高輸送量的工作負載，通常無法使用 `RECOMPILE` 選項。
- 使用 [OPTION (OPTIMIZE FOR…)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查詢提示，以典型參數值覆寫實際參數值，可為大多數參數值的可能性提供夠完善的計畫。   此選項需要充分了解最佳的參數值和相關聯的計畫特性。
- 使用 [OPTION (OPTIMIZE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查詢提示來覆寫實際參數值以換取使用密度向量的平均值。 另一種方法是將傳入的參數值擷取到區域變數中，然後使用述詞內的區域變數，而不是使用參數本身。 對於此特定的修正程式，平均密度必須*夠好*。
- 使用 [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查詢提示完全停用參數探查。
- 使用 [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查詢提示，以避免在快取中重新編譯。 此因應措施假設*夠好的*常見計畫已是快取中的計畫。 您還可以停用自動更新統計資料，以減少良好計畫被收回以及編譯新的錯誤計畫的可能性。
- 透過明確地使用 [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查詢提示 (透過明確地指定、使用查詢存放區設定特定計畫，或啟用[自動調整](sql-database-automatic-tuning.md)) 強制執行計畫。
- 使用程序的的巢狀集合取代單一程序，每個程序都可以根據條件式邏輯和相關聯的參數值來使用。
- 建立靜態程序定義的動態字串執行替代方案。

如需解決這些類型問題的詳細資訊, 請參閱 blog 文章:

- [我嗅覺一個參數](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)
- [參數化查詢的動態 sql 與計畫品質](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [SQL Server 中的 SQL 查詢優化技術:參數探查](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>疑難排解由於不正確的參數化所導致的編譯活動失敗

當查詢包含常值時，資料庫引擎會選擇自動參數化陳述式，或使用者可以明確地將它參數化以減少編譯數目。 使用相同模式但不同常值之查詢的大量編譯, 可能會導致高 CPU 使用率。 同樣地，如果您僅部分參數化繼續使用常值的查詢，則資料庫引擎不會進一步參數化。  以下是部分參數化查詢的範例：

```sql
SELECT * 
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

在先前的範例中`t1.c1` , `@p1`會`t2.c2`採用, 但繼續接受 GUID 作為常值。 在這種情況下，如果變更 `c2` 的值，查詢將會被視為不同的查詢，並且將進行新的編譯。 若要減少先前範例中的編譯，解決方案也還是將 GUID 參數化。

下列查詢顯示依查詢雜湊的查詢計數，以確定查詢是否已正確參數化：

```sql
SELECT  TOP 10  
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p 
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs 
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC
```
### <a name="factors-influencing-query-plan-changes"></a>影響查詢計劃變更的因素

查詢執行計畫重新編譯可能會導致產生的查詢計劃與原先快取的不同。 有各種原因會自動重新編譯現有的原始方案:
- 查詢參考的架構變更
- 查詢所參考之資料表的資料變更 
- 查詢內容選項的變更 

已編譯的計畫可能會因為各種原因而從快取中取出, 包括實例重新開機、資料庫範圍設定變更、記憶體壓力, 以及清除快取的明確要求。 此外, 使用重新編譯提示表示不會快取計畫。

重新編譯 (或快取收回之後的全新編譯) 仍然可以產生與原先觀察到的相同查詢執行計畫。  如果計畫的變更與先前或原始方案相比較, 以下是查詢執行計畫的變更最常見的說明:

- **已變更的實體設計**。 例如, 建立新的索引可更有效地涵蓋查詢的需求。 新的索引可能會在新的編譯中使用, 如果查詢最佳化工具決定利用該新索引的最佳做法, 比使用原本針對第一個查詢執行版本所選取的資料結構。  對參考物件進行的任何實體變更, 可能會在編譯時期產生新的計畫選擇。

- **伺服器資源差異**。 在一個方案與「系統 A」和「系統 B」不同的情況下, 資源的可用性 (例如可用的處理器數目) 可能會影響所產生的計畫。  例如, 如果某個系統的處理器數目較高, 則可以選擇平行計畫。 

- **不同的統計資料**。 與參考物件相關聯的統計資料已變更, 或與原始系統的統計資料非常不同。  如果統計資料變更, 而且發生重新編譯, 則查詢最佳化工具會使用統計資料 (從該特定時間點開始)。 修訂的統計資料可能會有不同于原始編譯的資料分佈和頻率。  這些變更是用來估計基數估計值 (預期會流經邏輯查詢樹狀結構的資料列數目)。  變更基數估計值可能會讓我們選擇不同的實體運算子和相關聯的作業順序。  即使是統計資料的次要變更, 也可能會導致變更的查詢執行計畫。

- **已變更資料庫相容性層級或基數估計工具版本**。  資料庫相容性層級的變更可以啟用可能會產生不同查詢執行計畫的新策略和功能。  除了資料庫相容性層級之外, 停用或啟用追蹤旗標4199或變更資料庫範圍設定 QUERY_OPTIMIZER_HOTFIXES 的狀態, 也可能會影響在編譯時期的查詢執行計畫選擇。  追蹤旗標 9481 (強制舊版 CE) 和 2312 (強制預設 CE) 也會影響計畫。 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>解決問題查詢或提供更多資源

當您識別出問題之後，您可以調整問題查詢，或是升級計算大小或服務層級，以增加您 Azure SQL 資料庫的容量來滿足 CPU 需求。 如需調整單一資料庫資源的相關資訊，請參閱[在 Azure SQL Database 中調整單一資料庫資源](sql-database-single-database-scale.md)，如需調整彈性集區資源的相關資訊，請參閱[在 Azure SQL Database 中調整彈性集區](sql-database-elastic-pool-scale.md)。 如需調整受控執行個體的相關資訊，請參閱[執行個體層級的資源限制](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)。

### <a name="determine-if-running-issues-due-to-increase-workload-volume"></a>判斷是否因為增加工作負載磁碟區而導致執行問題

應用程式流量和工作負載的增加將代表 CPU 使用率的增加，但您必須小心正確診斷這個問題。 在高 CPU 案例中，回答下列問題以確定 CPU 增加是否確實是因為工作負載磁碟區變更引起的：

1. 來自應用程式的查詢是否為高 CPU 問題的原因？
2. 針對 CPU 取用最多的查詢 (可以識別)：

   - 判斷是否存在與相同的查詢相關聯的多個執行計畫。 如果是的話，判斷原因。
   - 針對具有相同執行計畫的查詢，判斷執行時間是否一致以及執行計數是否增加。 如果是，則可能是因為工作負載增加而導致效能問題。

總結來說，如果查詢執行計畫沒有以不同的方式執行，但 CPU 使用率隨著執行計數而增加，則可能存在與工作負載增加相關的效能問題。

推斷是否存在導致 CPU 問題的工作負載磁碟區變更並不一定很容易。   需考量的因素： 

- **資源使用量變更**

  例如，假設 CPU 長時間增加到 80% 的情況。  CPU 使用率本身並不表示工作負載磁碟區產生變更。  即使應用程式正在執行相同的實際工作負載，查詢執行計畫的迴歸和資料散發變更也可能導致更多的資源使用量。

- **出現新的查詢**

   應用程式可以在不同的時間驅動一組新的查詢。

- **要求數目的增加或減少**

   此案例是最明顯的工作負載量值。 查詢數目不一定會與更多的資源使用率相對應。 然而，假設其他因素不變，此計量仍然是重要的訊號。

## <a name="waiting-related-performance-issues"></a>等候相關的效能問題

一旦確信您未遇到高 CPU 的執行相關效能問題之後，則您所遇到的就是等候相關的效能問題。 也就是說，並未有效率地使用您的 CPU 資源，因為 CPU 正在等候其他資源。 在此情況下，您的下一個步驟就是識別出哪些 CPU 資源正處於等候狀態。 最常見的等候類型類別目錄顯示方法為:

- [查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)可提供一段時間每個查詢的等候統計資料。 在查詢存放區中，等候類型會合併到等候類別中。 等候類別與等候類型的對應位於 [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table)。
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) 會傳回作業期間執行緒執行時所遇到的所有等候相關資訊。 您可以使用此彙總的檢視來診斷關於 Azure SQL Database 及特定查詢和批次的效能問題。
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) 會傳回正在等候某些資源的工作等候佇列相關資訊。

在高 CPU 的情況下，查詢存放區並等候統計資料並不一定會反映 CPU 使用率，原因有兩個：

- 高 CPU 取用查詢可能仍在執行，而查詢尚未完成
- 發生容錯移轉時，高 CPU 取用查詢正在執行

查詢存放區和等候統計資料追蹤動態管理檢視僅顯示已完成和逾時查詢的結果，並且不會顯示目前正在執行之陳述式的資料 (直到它們完成)。 動態管理檢視[_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)可讓您追蹤目前執行中的查詢和相關聯的背景工作時間。

如先前的圖表中所示，最常見的等候是：

- 鎖定 (封鎖)
- I/O
- `tempdb` 相關的爭用
- 記憶體授與等候

> [!IMPORTANT]
> 如需一組使用這些 DMV 進行這些等候相關問題疑難排解的 T-SQL 查詢，請參閱：
>
> - [識別 I/O 效能問題](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [識別 `tempdb` 效能問題](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [識別記憶體授與等候](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox-等待和閂鎖](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox - usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improving-database-performance-with-more-resources"></a>使用更多資源提升資料庫效能

最後，如果沒有可改善資料庫效能的可操作項目，您可以變更 Azure SQL Database 中可用的資源數量。 變更單一資料庫的[DTU 服務層級](sql-database-service-tiers-dtu.md), 或隨時增加彈性集區的 edtu, 以指派更多資源。 或者, 如果您使用 VCore 為[基礎的購買模型](sql-database-service-tiers-vcore.md), 請變更服務層級, 或增加配置給資料庫的資源。

1. 若是單一資料庫，您可以視需要[變更服務層級](sql-database-single-database-scale.md)或[計算資源](sql-database-single-database-scale.md)以提升資料庫效能。
2. 若是多個資料庫，請考慮使用[彈性集區](sql-database-elastic-pool-guidance.md)來自動調整資源。

## <a name="tune-and-refactor-application-or-database-code"></a>調整和重構應用程式或資料庫程式碼

您可以變更應用程式程式碼，針對工作負載以最佳方式使用資料庫、變更索引、強制執行計劃，或使用提示來手動調整資料庫。 在[效能指引主題](sql-database-performance-guidance.md)文章中尋找手動微調和重寫程式碼的指引和秘訣。

## <a name="next-steps"></a>後續步驟

- 若要在 Azure SQL Database 中啟用自動調整，並讓自動調整功能完全管理您的工作負載，請參閱[啟用自動調整](sql-database-automatic-tuning-enable.md)。
- 若要使用手動調整，您可以檢閱 [Azure 入口網站中的調整建議](sql-database-advisor-portal.md)並手動套用可改善查詢效能的建議。
- 變更 [Azure SQL Database 服務層級](sql-database-performance-guidance.md)來變更資料庫中可用的資源
