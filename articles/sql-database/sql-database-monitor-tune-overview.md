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
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 2fa43fcd48736a3d044deb07ed690af580c3b987
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416287"
---
# <a name="monitoring-and-performance-tuning"></a>監視和效能微調

Azure SQL Database 可讓您輕鬆地監視使用狀況、 新增或移除資源 （CPU、 記憶體、 I/O），疑難排解潛在的問題，並尋找可改善資料庫效能的建議。 Azure SQL Database 有許多功能，可以自動修正問題在您的資料庫中如果您想要讓資料庫調整您的工作負載，並自動最佳化效能。 不過，有一些自訂的問題，您可能需要進行疑難排解。 這篇文章會說明一些最佳作法和疑難排解效能問題，您可以使用的工具。

有兩項主要活動，您需要執行以確保您資料庫執行時並沒有問題：
- [監視資料庫效能](#monitoring-database-performance)若要確定指派給您的資料庫的資源可以處理您的工作負載。 如果您看到您達到資源限制，您必須識別熱門資源取用查詢並最佳化，或藉由升級服務層中新增更多資源。
- [針對效能問題進行疑難排解](#troubleshoot-performance-issues)為了找出一些潛在問題發生的原因，找出的問題和動作，將會修正此問題的根本原因。

## <a name="monitoring-database-performance"></a>監視資料庫效能

在 Azure 中監視 SQL Database 的效能，必須從監視您選擇之資料庫效能等級相關的資源使用率開始。 您需要監視下列資源：
 - **CPU 使用量**-您需要檢查在較長的一段時間達到 100%的 CPU 使用量。 這可能表示您可能需要升級您的資料庫或執行個體或找出並調整使用的計算能力的大部分的查詢。
 - **等候統計資料**-您需要檢查為什麼您的查詢正在等候某些資源。 Queriesmig 等候資料，以擷取或儲存至資料庫檔案，因為某些資源的限制達到等待，依此類推。
 - **IO 使用量**-您需要檢查會到達基礎儲存區的 IO 限制。
 - **記憶體使用量**-您的資料庫或執行個體是虛擬機器數目成正比，您需要檢查可用的記憶體數量是它有足夠的工作負載。 頁面存留時間期望值是其中一個參數，可以指出您的頁面快速地從記憶體中移除。

Azure SQL Database**提供建議，可協助您疑難排解並修正潛在的效能問題**。 您可以輕鬆地識別提升與最佳化查詢效能，而不需要變更資源檢閱機會[效能微調建議](sql-database-advisor.md)。 遺漏索引與查詢最佳化不足是資料庫效能不佳的常見原因。 您可以套用這些調整建議來改善工作負載的效能。 您也可以套用所有發現的建議並驗證能否改善資料庫效能，讓 Azure SQL 資料庫[自動最佳化查詢](sql-database-automatic-tuning.md)。

您有下列選項可以用來針對資料庫效能進行監視和疑難排解：

- 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [SQL Database]  、選取資料庫，然後使用監視圖表尋找接近其上限的資源。 默认情况下将显示 DTU 消耗量。 按一下 [編輯]  來變更所顯示的時間範圍和值。
- SQL Server Management Studio 之類的工具可讓您提供許多有用的報表喜歡[績效儀表板](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard?view=sql-server-2017)，您可以監視資源使用率，並識別熱門資源取用查詢，或[查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed)您可以在其中識別效能迴歸查詢。
- 使用[查詢效能深入解析](sql-database-query-performance.md) [Azure 入口網站](https://portal.azure.com)中找出花費最多資源的查詢。 這項功能僅適用於單一資料庫和彈性集區。
- 使用 [SQL Database Advisor](sql-database-advisor-portal.md) 來檢視建立和卸除索引、參數化查詢，以及修正結構描述問題的建議。 這項功能僅適用於單一資料庫和彈性集區。
- 使用 [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) 來自動監視您的資料庫效能。 在偵測到效能問題之後，系統就會產生包含問題詳細資料和「根本原因分析」(RCA) 的診斷記錄。 在可能的情況下會提供效能改進建議。
- [啟用自動調整](sql-database-automatic-tuning-enable.md)並讓 Azure SQL Database 自動修正發現的效能問題。
- 使用[動態管理檢視 (DMV)](sql-database-monitoring-with-dmvs.md)、[擴充的事件](sql-database-xevent-db-diff-from-svr.md)和[查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)，更深入了解如何進行效能問題的疑難排解。

> [!TIP]
> 使用上述一或多個方法識別出效能問題之後，請參閱[效能指引](sql-database-performance-guidance.md)以尋找可用來改善 Azure SQL Database 效能的技術。

## <a name="troubleshoot-performance-issues"></a>針對效能問題進行疑難排解

若要診斷並解決效能問題，一開始先了解每個作用中查詢的狀態，以及導致與每個工作負載狀態相關之效能問題的情況。 若要改善 Azure SQL Database 效能，請了解您應用程式的每個作用中查詢是在執行中還是等候狀態。 在 Azure SQL Database 中進行效能問題的疑難排解時，請在閱讀本文時牢記下圖以診斷並解決效能問題。

![工作負載狀態](./media/sql-database-monitor-tune-overview/workload-states.png)

對於發生效能問題的工作負載，效能問題可能是來自 CPU 爭用 (**與執行相關**的狀況)，或是個別查詢正在等候某些內容 (**與等候相關**的狀況)。

原因或**執行相關**問題可能是：
- **編譯問題**-SQL 查詢最佳化工具可能會產生次佳的計畫，因為過時的統計資料、 將處理的資料列數目的估計不正確或所需的記憶體的估計。 如果您知道在過去，或其他執行個體 （受控執行個體或 SQL Server 執行個體） 上更快速執行該查詢，需要比較以查看它們與實際執行計畫不同。 嘗試套用查詢提示或重建統計資料或索引，以取得更佳的計劃。 啟用自動計畫更正，以自動緩和這些問題的 Azure SQL Database 中。
- **執行問題**-如果是最佳的查詢計劃則它可能達到某些記錄檔寫入輸送量等資料庫中的資源限制或它可能會使用重組應該重建的索引。 大量的浪費資源的並行查詢，可能也會執行問題的原因。 **等候相關**問題會在大部分情況下，與執行的問題，因為未有效率地執行的查詢可能會等待某些資源。

原因或**等候相關**問題可能是：
- **封鎖**-一個查詢可能會鎖定在資料庫中的某些物件時保留其他人嘗試存取相同的物件。 您可以輕鬆地識別封鎖的查詢使用 DMV 或監視工具。
- **IO 問題**-查詢可能會等候寫入資料或記錄檔的頁面。 在此情況下，您會看到`INSTANCE_LOG_RATE_GOVERNOR`， `WRITE_LOG`，或`PAGEIOLATCH_*`等候統計資料 DMV 中的。
- **TempDB 問題**-如果您使用許多暫存資料表，或您會看到您的計劃您可能會有問題的 TempDB 輸送量查詢大量 TempDB 溢出。 
- **記憶體相關問題**-您可能沒有足夠的記憶體為您的工作負載讓您的頁面存留時間期望值可能會卸除，或您的查詢會收到較少的記憶體授與比所需。 在某些情況下，內建的智慧，查詢最佳化工具將會修正這些問題。
 
下列各節將說明如何識別及疑難排解其中一些問題。

## <a name="running-related-performance-issues"></a>執行相關的效能問題

一般來說，如果您的 CPU 使用率持續為等於或高於 80%，就有執行相關的效能問題。 如果您有執行相關的問題，則其可能因為 CPU 資源不足所導致，或者可能與下列其中一個情況相關：

- 太多執行的查詢
- 太多編譯的查詢
- 一個或多個正在執行的查詢在使用次佳的查詢計劃

如果您判斷有執行相關的效能問題，您的目標就是使用一或多個方法來識別出確切的問題。 用來識別執行相關問題的最常見方法如下：

- 使用 [Azure 入口網站](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal)來監視 CPU 百分比使用率。
- 使用下列[動態管理檢視](sql-database-monitoring-with-dmvs.md)：

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) 會傳回 Azure SQL Database 資料庫的 CPU、I/O 和記憶體耗用量。 每 15 秒會有一列，即使資料庫沒有任何活動，也會有一列。 歷程記錄資料會保留一個小時。
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) 會傳回 Azure SQL Database 的 CPU 使用量和儲存體資料。 每五分鐘會收集和彙總資料一次。

> [!IMPORTANT]
> 如需一組使用這些 DMV 進行 CPU 使用率問題疑難排解的 T-SQL 查詢，請參閱[識別 CPU 效能問題](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues)。

### <a name="ParamSniffing"></a> 查詢參數區分查詢執行計劃問題進行疑難排解

參數區分計畫 (PSP) 問題是指查詢最佳化工具產生僅適用於特定參數值 (或一組值) 的查詢執行計畫的情況，然後快取計畫不適用於連續執行中使用的參數值。 然後，非最佳計畫可能會導致查詢效能問題和整體工作負載輸送量降低。 如需有關參數探測和查詢處理的詳細資訊，請參閱 <<c0> [ 查詢處理架構指南](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)。

有數個用來減輕問題的因應措施，每一種都有相關聯的取捨和缺點：

- 在每次查詢執行時使用 [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查詢提示。 此因應措施會交易編譯時間和增加的 CPU，以獲得更好的計劃品質。 針對需要高輸送量的工作負載，通常無法使用 `RECOMPILE` 選項。
- 使用 [OPTION (OPTIMIZE FOR…)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查詢提示，以典型參數值覆寫實際參數值，可為大多數參數值的可能性提供夠完善的計畫。   此選項需要充分了解最佳的參數值和相關聯的計畫特性。
- 使用 [OPTION (OPTIMIZE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查詢提示來覆寫實際參數值以換取使用密度向量的平均值。 另一種方法是將傳入的參數值擷取到區域變數中，然後使用述詞內的區域變數，而不是使用參數本身。 對於此特定的修正程式，平均密度必須*夠好*。
- 使用 [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查詢提示完全停用參數探查。
- 使用 [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查詢提示，以避免在快取中重新編譯。 此因應措施假設*夠好的*常見計畫已是快取中的計畫。 您還可以停用自動更新統計資料，以減少良好計畫被收回以及編譯新的錯誤計畫的可能性。
- 透過明確地使用 [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查詢提示 (透過明確地指定、使用查詢存放區設定特定計畫，或啟用[自動調整](sql-database-automatic-tuning.md)) 強制執行計畫。
- 使用程序的的巢狀集合取代單一程序，每個程序都可以根據條件式邏輯和相關聯的參數值來使用。
- 建立靜態程序定義的動態字串執行替代方案。

如需解決這類問題的詳細資訊，請參閱：

- 這[我嗅覺參數](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)部落格文章
- 此[動態 sql 與參數化查詢的計畫品質](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/) \(英文\) 部落格文章
- 這[SQL Server 中的 SQL 查詢最佳化技巧：參數 Sniffing](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)部落格文章

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>疑難排解由於不正確的參數化所導致的編譯活動失敗

當查詢包含常值時，資料庫引擎會選擇自動參數化陳述式，或使用者可以明確地將它參數化以減少編譯數目。 使用相同模式但不同常值之查詢的大量編譯，可能會導致高 CPU 使用率。 同樣地，如果您僅部分參數化繼續使用常值的查詢，則資料庫引擎不會進一步參數化。  以下是部分參數化查詢的範例：

```sql
SELECT * FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

在先前的範例中，`t1.c1` 會採用 `@p1` 但 `t2.c2` 繼續接受 GUID 作為常值。 在這種情況下，如果變更 `c2` 的值，查詢將會被視為不同的查詢，並且將進行新的編譯。 若要減少先前範例中的編譯，解決方案也還是將 GUID 參數化。

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

不同於原先快取項目產生的查詢計劃可能會造成查詢執行計畫重新編譯。 有各種原因可能會自動編譯現有的原始方案的原因：
- 正在查詢所參考的結構描述中的變更
- 查詢正在參考資料表的資料變更 
- 查詢內容選項的變更 

已編譯的計畫可能會退出從各種不同的原因，包括執行個體重新啟動快取、 資料庫範圍組態變更，記憶體不足的壓力，明確清除快取的要求。 此外，使用 RECOMPILE 提示，表示不會快取計畫。

重新編譯 （或快取收回後的重新編譯） 仍會導致從其中一個原先所觀察到相同的查詢執行計畫的產生。  如果，不過，相較於先前或原始的計劃的計劃的變更，以下是最常見的說明，針對查詢執行計劃變更的原因：

- **變更實體設計**。 例如，新的索引建立，更有效地涵蓋查詢的需求可能會用在新的編譯，如果查詢最佳化工具決定它是較佳，無法利用該新的索引，不使用原本選取的第一個版本的資料結構執行查詢。  參考物件的任何實體變更可能會導致編譯時期的新計畫選擇。

- **伺服器資源差異**。 在計劃與 「 系統 A 」 與 「 系統 B 」 – 可用性的資源，例如可用的處理器數目的不同的案例中可能會影響產生什麼計劃。  例如，如果系統有較高數目的處理器，可能會選擇平行計畫。 

- **不同的統計資料**。 參考的物件相關聯的統計資料已變更，或未不同於原始系統的統計資料。  如果統計資料變更，並且重新編譯時，查詢最佳化工具就會使用該特定點的統計資料的時間。 修改過的統計資料可能會有明顯不同的資料分佈，而不是在原始的編譯的頻率。  這些變更用來預估基數估計值 （預期的流量通過邏輯的查詢樹狀結構的資料列數目）。  基數估計值的變更可能會導致我們選擇不同的實體運算子和的作業相關聯的 order。  即使是小幅度變更統計資料可能會導致已變更的查詢執行計畫。

- **變更的資料庫相容性層級或基數估計工具版本**。  變更資料庫相容性層級可以啟用新的策略和可能會導致不同的查詢執行計畫的功能。  超過資料庫相容性層級停用或啟用追蹤旗標 4199 或變更 QUERY_OPTIMIZER_HOTFIXES 可能也會影響資料庫限域組態的狀態查詢在編譯時期的執行計畫選擇。  追蹤旗標 9481 (強制舊版 CE) 和 2312 （強制執行預設 CE） 也會影響計劃。 

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

一旦確信您未遇到高 CPU 的執行相關效能問題之後，則您所遇到的就是等候相關的效能問題。 也就是說，並未有效率地使用您的 CPU 資源，因為 CPU 正在等候其他資源。 在此情況下，您的下一個步驟就是識別出哪些 CPU 資源正處於等候狀態。 顯示最常等候項目類型類別的最常見方法：

- [查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)可提供一段時間每個查詢的等候統計資料。 在查詢存放區中，等候類型會合併到等候類別中。 等候類別與等候類型的對應位於 [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table)。
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) 會傳回作業期間執行緒執行時所遇到的所有等候相關資訊。 您可以使用此彙總的檢視來診斷關於 Azure SQL Database 及特定查詢和批次的效能問題。
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) 會傳回正在等候某些資源的工作等候佇列相關資訊。

在高 CPU 的情況下，查詢存放區並等候統計資料並不一定會反映 CPU 使用率，原因有兩個：

- 高 CPU 取用查詢可能仍在執行，而查詢尚未完成
- 發生容錯移轉時，高 CPU 取用查詢正在執行

查詢存放區和等候統計資料追蹤動態管理檢視僅顯示已完成和逾時查詢的結果，並且不會顯示目前正在執行之陳述式的資料 (直到它們完成)。 動態管理檢視 [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 允許您追蹤目前正在執行的查詢和相關聯的背景工作時間。

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
> - [TigerToolbox-等候和閂鎖](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox-usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improving-database-performance-with-more-resources"></a>使用更多資源提升資料庫效能

最後，如果沒有可改善資料庫效能的可操作項目，您可以變更 Azure SQL Database 中可用的資源數量。 您可以隨時變更單一資料庫的 [DTU 服務層](sql-database-service-tiers-dtu.md)或增加彈性集區的 eDTU。 或者，如果您使用[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)，便可以變更服務層或增加配置給資料庫的資源。

1. 若是單一資料庫，您可以視需要[變更服務層級](sql-database-single-database-scale.md)或[計算資源](sql-database-single-database-scale.md)以提升資料庫效能。
2. 若是多個資料庫，請考慮使用[彈性集區](sql-database-elastic-pool-guidance.md)來自動調整資源。

## <a name="tune-and-refactor-application-or-database-code"></a>調整和重構應用程式或資料庫程式碼

您可以變更應用程式程式碼，針對工作負載以最佳方式使用資料庫、變更索引、強制執行計劃，或使用提示來手動調整資料庫。 在[效能指引主題](sql-database-performance-guidance.md)文章中可找到手動調整或重寫程式碼的一些方針和秘訣。

## <a name="next-steps"></a>後續步驟

- 若要在 Azure SQL Database 中啟用自動調整，並讓自動調整功能完全管理您的工作負載，請參閱[啟用自動調整](sql-database-automatic-tuning-enable.md)。
- 若要使用手動調整，您可以檢閱 [Azure 入口網站中的調整建議](sql-database-advisor-portal.md)並手動套用可改善查詢效能的建議。
- 變更 [Azure SQL Database 服務層](sql-database-performance-guidance.md)來變更資料庫中可用的資源
