---
title: 監視和效能調整-Azure SQL Database |Microsoft Docs
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
ms.openlocfilehash: 5df9df1474489d7f1b1fb4e1089143cca63a3e42
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2019
ms.locfileid: "71935610"
---
# <a name="monitoring-and-performance-tuning"></a>監視和效能微調

Azure SQL Database 提供的工具和方法可讓您輕鬆地監視使用方式、新增或移除資源（例如 CPU、記憶體或 i/o）、疑難排解潛在問題，以及提出建議以改善資料庫的效能。 Azure SQL Database 中的功能可以自動修正資料庫中的問題。 

自動調整可讓資料庫適應工作負載，並自動優化效能。 不過，某些自訂問題可能需要進行疑難排解。 本文說明一些最佳做法，以及一些您可以用來對效能問題進行疑難排解的工具。

若要確保資料庫執行時沒有發生問題，您應該：
- [監視資料庫效能](#monitor-database-performance)，確保指派給資料庫的資源可以處理工作負載。 如果資料庫達到資源限制，請考慮：
   - 識別和優化最常耗用資源的查詢。
   - 藉由[升級服務層](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources)來新增更多資源。
- 針對[效能問題進行疑難排解](#troubleshoot-performance-problems)，找出發生潛在問題的原因，並找出問題的根本原因。 找出根本原因之後，請採取步驟來修正問題。

## <a name="monitor-database-performance"></a>監視資料庫效能

若要監視 Azure 中 SQL database 的效能，請從監視相對於所選資料庫效能層級的資源開始。 監視下列資源：
 - **CPU 使用量**：檢查資料庫是否有一段頗長的時間達到 100% 的 CPU 使用量。 高 CPU 使用率可能表示您需要識別並調整使用最多計算能力的查詢。 高 CPU 使用率也可能表示資料庫或實例應該升級至較高的服務層級。 
 - **等候統計資料**：使用[_os_wait_stats （transact-sql）](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)來判斷查詢正在等候的時間長度。 查詢可以等候資源、佇列等候或外部等候。 
 - **IO 使用**方式：請檢查資料庫是否達到基礎儲存體的 IO 限制。
 - **記憶體使用量**：資料庫或實例可用的記憶體數量與虛擬核心的數目成正比。 請確定記憶體足以應付工作負載。 分頁生命週期是其中一個參數, 可表示從記憶體中移除頁面的速度。

Azure SQL Database 服務包含工具和資源，可協助您進行疑難排解並修正潛在的效能問題。 您可以藉由查看[效能微調建議](sql-database-advisor.md)，找出改善和優化查詢效能的機會，而不需要變更資源。 

遺漏索引與查詢最佳化不足是資料庫效能不佳的常見原因。 您可以套用微調建議，以改善工作負載的效能。 您也可以藉由套用所有已識別的建議，讓 Azure SQL Database[自動優化查詢的效能](sql-database-automatic-tuning.md)。 然後確認建議改善資料庫效能。

> [!NOTE]
> 索引編制僅適用于單一資料庫和彈性集區。 無法在受控實例中使用索引。

從下列選項中選擇，以監視和疑難排解資料庫效能：

- 在  [Azure 入口網站](https://portal.azure.com)中，選取  **SQL 資料庫**] 並選取資料庫。 在 [**監視**] 圖表中，尋找已接近其最大使用率的資源。 預設會顯示 DTU 耗用量。 選取 [**編輯**] 以變更所顯示的時間範圍和值。
- SQL Server Management Studio 之類的工具提供許多有用的報表，例如[效能儀表板](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard)。 使用這些報告來監視資源使用狀況，並識別最常耗用資源的查詢。 您可以使用[查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed)來識別效能已回歸的查詢。
- 在[Azure 入口網站](https://portal.azure.com)中，使用[查詢效能深入解析](sql-database-query-performance.md)來識別使用大部分資源的查詢。 這項功能僅適用于單一資料庫和彈性集區。
- 使用[SQL Database Advisor](sql-database-advisor-portal.md)來查看建議，以協助您建立和卸載索引、參數化查詢，以及修正架構問題。 這項功能僅適用于單一資料庫和彈性集區。
- 使用[AZURE SQL Intelligent Insights](sql-database-intelligent-insights.md)自動監視資料庫效能。 當偵測到效能問題時，就會產生診斷記錄。 記錄檔會提供問題的詳細資料和根本原因分析（RCA）。 可能的話，會提供效能改進建議。
- [啟用自動調整](sql-database-automatic-tuning-enable.md)，讓 Azure SQL Database 自動修正效能問題。
- 使用[動態管理檢視（dmv）](sql-database-monitoring-with-dmvs.md)、[擴充的事件](sql-database-xevent-db-diff-from-svr.md)和[查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)，以取得更詳細的效能問題疑難排解的協助。

> [!TIP]
> 找出效能問題之後，請查看我們的[效能指引](sql-database-performance-guidance.md)，以尋找改善 Azure SQL Database 效能的技巧。

## <a name="troubleshoot-performance-problems"></a>針對效能問題進行疑難排解

若要診斷並解決效能問題，請先找出每個作用中查詢的狀態，以及導致每個工作負載狀態相關效能問題的條件。 若要改善 Azure SQL Database 效能，您必須瞭解應用程式中的每個使用中查詢要求都處於執行中狀態或等候狀態。 當您針對 Azure SQL Database 中的效能問題進行疑難排解時，請記住下列圖表。

![工作負載狀態](./media/sql-database-monitor-tune-overview/workload-states.png)

工作負載中的效能問題可能是因 CPU 爭用（執行*相關*的條件）或正在等候某個事物的個別查詢（*等待相關*的條件）所造成。

執行相關的問題可能是由下列原因所造成：
- **編譯問題**：SQL 查詢最佳化工具可能會產生較佳的計畫，因為過期的統計資料、要處理的資料列數目的估計值不正確，或所需的記憶體估計不精確。 如果您知道查詢是在過去或另一個實例（受控實例或 SQL Server 實例）上更快執行，請比較實際的執行計畫，以查看它們是否不同。 請嘗試套用查詢提示或重建統計資料或索引，以取得更好的計畫。 請在 Azure SQL Database 中啟用自動計畫更正，以自動減輕這些問題。
- **執行問題**：如果查詢計劃是最佳的，可能會達到資料庫的資源限制，例如記錄寫入輸送量。 或者，它可能使用應該重建的分散索引。 當大量的並行查詢需要相同的資源時，也可能會發生執行問題。 *等待相關*的問題通常與執行問題有關，因為未有效率執行的查詢可能會等候某些資源。

等待相關的問題可能是由下列原因所造成：
- **封鎖**：一個查詢可能會持有資料庫中物件的鎖定，而有些則嘗試存取相同的物件。 您可以使用 Dmv 或監視工具來識別封鎖查詢。
- **IO 問題**：查詢可能正在等候頁面寫入資料或記錄檔。 在此情況下，請`INSTANCE_LOG_RATE_GOVERNOR`檢查`WRITE_LOG`DMV 中`PAGEIOLATCH_*`的、或等候統計資料。
- **TempDB 問題**：如果工作負載使用臨時表，或計畫中有 TempDB 溢出，則查詢可能會有 TempDB 輸送量的問題。 
- **記憶體相關問題**：如果工作負載沒有足夠的記憶體，則分頁生命預期可能會下降，或查詢可能會取得比所需更少的記憶體。 在某些情況下，查詢最佳化工具中的內建智慧功能將會修正記憶體相關問題。
 
下列各節說明如何識別和疑難排解某些類型的問題。

## <a name="performance-problems-related-to-running"></a>與執行相關的效能問題

一般的指導方針是，如果 CPU 使用量一致地等於或高於 80%，表示您的效能問題正在執行-相關。 執行相關的問題可能是因為 CPU 資源不足所造成。 或者，它可能與下列其中一個條件相關：

- 太多執行的查詢
- 太多編譯的查詢
- 一或多個使用次佳查詢計劃的執行中查詢

如果您發現執行相關的效能問題，您的目標是使用一或多個方法來找出精確的問題。 這些方法是識別執行相關問題最常見的方式：

- 使用[Azure 入口網站](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal)來監視 CPU 使用量百分比。
- 使用下列[dmv](sql-database-monitoring-with-dmvs.md)：

  - [_Db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV 會傳回 SQL 資料庫的 CPU、i/o 和記憶體耗用量。 每隔15秒的間隔會有一個資料列，即使資料庫中沒有任何活動。 歷程記錄資料會保留一個小時。
  - [Resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV 會傳回 AZURE SQL DATABASE 的 CPU 使用量和儲存體資料。 資料會收集並匯總五分鐘的間隔。

> [!IMPORTANT]
> 若要針對使用 _db_resource_stats 和 resource_stats Dmv 的 T-SQL 查詢進行 CPU 使用量問題的疑難排解，請參閱[識別 cpu 效能問題](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues)。

### <a name="ParamSniffing"></a>具有 PSP 問題的查詢

當查詢最佳化工具產生的查詢執行計畫僅適用于特定參數值（或一組值）時，就會發生「參數敏感性計畫」（PSP）問題，而快取的計畫則不會針對連續使用的參數值進行優化。job. 不是最佳的計畫會導致查詢效能問題，並降低整體工作負載輸送量。 

如需參數探查和查詢處理的詳細資訊，請參閱[查詢處理架構指南](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)。

有數種因應措施可減輕 PSP 問題。 每個因應措施都有相關聯的取捨和缺點：

- 在每次查詢執行時使用 [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查詢提示。 此因應措施會交易編譯時間和增加的 CPU，以獲得更好的計劃品質。 此`RECOMPILE`選項通常無法用於需要高輸送量的工作負載。
- 使用[選項（OPTIMIZE FOR ...）](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)查詢提示，以產生足以滿足大部分參數值可能性的計畫之一般參數值來覆寫實際的參數值。 此選項需要充分了解最佳的參數值和相關聯的計畫特性。
- 使用[選項（[針對未知的優化）](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) ] 查詢提示來覆寫實際的參數值，改為使用密度向量平均值。 您也可以在本機變數中捕捉傳入參數值，然後在述詞中使用區域變數，而不是使用參數本身來執行此動作。 針對此修正，平均密度必須*夠好*。
- 使用[DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)查詢提示來完全停用參數探查。
- 使用[KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)查詢提示來避免在快取中重新編譯。 這個因應措施假設已有足夠的一般計畫，也就是快取中的方案。 您也可以停用自動統計資料更新，以降低將會收回良好計畫的機會，並編譯新的錯誤計畫。
- 藉由重寫查詢並在查詢文字中加入提示，以明確地使用[USE plan](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)查詢提示來強制執行計畫。 或藉由使用查詢存放區或啟用[自動調整](sql-database-automatic-tuning.md)來設定特定計劃。
- 使用程序的的巢狀集合取代單一程序，每個程序都可以根據條件式邏輯和相關聯的參數值來使用。
- 建立靜態程序定義的動態字串執行替代方案。

如需解決 PSP 問題的詳細資訊，請參閱下列 blog 文章：

- [我嗅覺一個參數](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)
- [Conor 與動態 SQL 與程式的比較，以及參數化查詢的計畫品質](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [SQL Server 中的 SQL 查詢優化技術：參數探查](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>參數化不正確所造成的編譯活動

當查詢具有常值時，資料庫引擎會自動參數化語句或使用者明確參數化語句，以減少編譯的次數。 使用相同模式但不同常值之查詢的大量編譯，可能會導致高 CPU 使用率。 同樣地，如果您只部分參數化繼續具有常值的查詢，則資料庫引擎不會進一步將查詢參數化。  

以下是部分參數化查詢的範例：

```sql
SELECT * 
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

在此範例中`t1.c1` ， `@p1`會採用`t2.c2` ，但會繼續將 GUID 當做常值。 在此情況下，如果您變更的值`c2`，則會將查詢視為不同的查詢，並會進行新的編譯。 為了減少此範例中的編譯，您也會將 GUID 參數化。

下列查詢會依查詢雜湊顯示查詢計數，以判斷查詢是否已正確參數化：

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

### <a name="factors-that-affect-query-plan-changes"></a>影響查詢計劃變更的因素

查詢執行計畫重新編譯可能會產生與原始快取計畫不同的查詢計劃。 現有的原始方案可能會因為各種原因而自動重新編譯：
- 查詢會參考架構中的變更。
- 查詢會參考資料表的資料變更。 
- 查詢內容選項已變更。

已編譯的計畫可能會因為各種原因而從快取中取出，例如：

- 實例重新開機。
- 資料庫範圍的設定變更。
- 記憶體壓力。
- 明確要求清除快取。

如果您使用重新編譯提示，則不會快取計畫。

重新編譯（或快取收回之後的全新編譯）仍然可能產生與原始相同的查詢執行計畫。 當計畫從先前或原始計畫變更時，這些說明可能如下：

- **已變更的實體設計**：例如，新建立的索引會更有效地涵蓋查詢的需求。 新的索引可能會在新的編譯中使用，如果查詢最佳化工具決定使用新的索引比原先針對第一個查詢執行版本所選取的資料結構更理想。  對參考物件進行的任何實體變更，可能會在編譯時期產生新的計畫選擇。

- **伺服器資源差異**：當某個系統中的計畫與另一個系統中的方案不同時，資源可用性（例如可用的處理器數目）可能會影響所產生的計畫。  例如，如果一個系統有更多的處理器，可能會選擇平行計畫。 

- **不同的統計資料**：與參考物件相關聯的統計資料可能已變更，或可能與原始系統的統計資料不同。  如果統計資料變更，而且發生重新編譯，則查詢最佳化工具會在其變更時，使用從開始的統計資料。 修訂過的統計資料「資料散發」和「頻率」可能與原始編譯的不同。  這些變更會用來建立基數估計值。 （*基數估計值*是預期會流經邏輯查詢樹狀結構的資料列數目）。基數估計值的變更可能會導致您選擇不同的實體運算子和相關聯的作業順序。  即使是統計資料的次要變更, 也可能會導致變更的查詢執行計畫。

- **變更的資料庫相容性層級或基數估計工具版本**：資料庫相容性層級的變更可以啟用可能會產生不同查詢執行計畫的新策略和功能。  除了資料庫相容性層級之外，已停用或啟用的追蹤旗標4199或資料庫範圍設定 QUERY_OPTIMIZER_HOTFIXES 的已變更狀態，也可能會在編譯時期影響查詢執行計畫選擇。  追蹤旗標9481（強制舊版 CE）和2312（強制預設 CE）也會影響計畫。 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>解決問題查詢或提供更多資源

找出問題之後，您可以微調問題查詢，或升級計算大小或服務層級，以增加 SQL 資料庫的容量以吸收 CPU 需求。 

如需詳細資訊，請參閱在[Azure SQL Database 中調整單一資料庫資源](sql-database-single-database-scale.md)和[在 Azure SQL Database 中調整彈性集區資源](sql-database-elastic-pool-scale.md)。 如需調整受控實例的相關資訊，請參閱[服務層資源限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)。

### <a name="performance-problems-caused-by-increased-workload-volume"></a>因工作負載量增加而造成的效能問題

應用程式流量和工作負載量的增加，可能會造成 CPU 使用量增加。 但是您必須小心地診斷這個問題。 當您看到高 CPU 問題時，請回答這些問題，以判斷增加是否由工作負載磁片區的變更所造成：

- 應用程式中的查詢是否為高 CPU 問題的原因？
- 針對您可以識別的最高 CPU 耗用查詢：

   - 是否有多個與相同查詢相關聯的執行計畫？ 若是如此，為什麼？
   - 針對具有相同執行計畫的查詢，執行時間是否一致？ 執行計數是否增加？ 若是如此，工作負載的增加可能會造成效能問題。

總而言之，如果查詢執行計畫未以不同的方式執行，但 CPU 使用量隨著執行計數增加，則效能問題可能與工作負載增加有關。

找出驅動 CPU 問題的工作負載磁片區變更並不容易。 請考慮下列因素： 

- **已變更的資源使用量**：例如，假設有一段長時間 CPU 使用量增加到 80% 的案例。  CPU 使用量本身並不表示工作負載磁片區已變更。 即使應用程式執行相同的工作負載，查詢執行計畫中的回歸和資料散發中的變更也可能會導致更多的資源使用量。

- **新查詢的外觀**：應用程式可能會在不同的時間驅動一組新的查詢。

- **要求數目增加或減少**：此案例是最明顯的工作負載量值。 查詢數目不一定會與更多的資源使用率相對應。 不過，此計量仍然是很重要的信號，假設其他因素不變。

## <a name="waiting-related-performance-problems"></a>等待相關的效能問題 

如果您確定效能問題與高 CPU 使用量無關，或無法執行，則您的問題與等候有關。 也就是，您的 CPU 資源未有效率地使用，因為 CPU 正在等候某些其他資源。 在此情況下，請找出您的 CPU 資源正在等待的狀態。 

這些方法通常用來顯示等候類型的最上層類別：

- 使用[查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)來尋找一段時間內每個查詢的等候統計資料。 在查詢存放區中，等候類型會合併到等候類別中。 您可以在[query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table)中找到等候類別的對應至等候類型。
- 使用[sys.databases _db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) ，傳回作業期間執行的執行緒所遇到之所有等候的相關資訊。 您可以使用這個匯總視圖來診斷 Azure SQL Database 的效能問題，以及特定查詢和批次。
- 使用[sys.databases _os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) ，傳回有關在某些資源上等候的工作佇列的資訊。

在高 CPU 案例中，如果下列情況，查詢存放區和等候統計資料可能不會反映 CPU 使用量：

- 高 CPU 耗用的查詢仍在執行中。
- 發生容錯移轉時，耗用大量 CPU 的查詢正在執行。

追蹤查詢存放區和等候統計資料的 Dmv 只會顯示成功完成和超時查詢的結果。 它們在語句完成之前，不會顯示目前正在執行之語句的資料。 使用動態管理檢視[_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)來追蹤目前執行中的查詢和相關聯的背景工作時間。

本文開頭附近的圖表說明最常見的等候：

- 鎖定 (封鎖)
- I/O
- 與 TempDB 相關的爭用
- 記憶體授與等候

> [!IMPORTANT]
> 如需使用 Dmv 對等待相關問題進行疑難排解的一組 T-SQL 查詢，請參閱：
>
> - [識別 I/O 效能問題](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [識別記憶體授與等候](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox 等候和閂鎖](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improve-database-performance-with-more-resources"></a>使用更多資源提升資料庫效能

如果沒有可操作的專案可以改善您的資料庫效能，您可以變更 Azure SQL Database 中可用的資源數量。 藉由變更單一資料庫的[DTU 服務層級](sql-database-service-tiers-dtu.md)來指派更多資源。 或在任何時間增加彈性集區的 Edtu。 或者，如果您使用 VCore 為[基礎的購買模型](sql-database-service-tiers-vcore.md)，請變更服務層級，或增加配置給資料庫的資源。

針對單一資料庫，您可以視需要[變更服務層級或計算資源](sql-database-single-database-scale.md)，以改善資料庫效能。 若是多個資料庫，請考慮使用[彈性集區](sql-database-elastic-pool-guidance.md)來自動調整資源。

## <a name="tune-and-refactor-application-or-database-code"></a>調整和重構應用程式或資料庫程式碼

您可以將資料庫、變更索引、強制執行計畫或使用提示的應用程式程式碼優化，以手動將資料庫調整為您的工作負載。 如需手動調整和重寫程式碼的相關資訊，請參閱[效能微調指導](sql-database-performance-guidance.md)方針。

## <a name="next-steps"></a>後續步驟

- 若要在 Azure SQL Database 中啟用自動調整，並讓自動調整功能完全管理您的工作負載，請參閱[啟用自動調整](sql-database-automatic-tuning-enable.md)。
- 若要使用手動微調，請參閱[Azure 入口網站中的微調建議](sql-database-advisor-portal.md)。 手動套用改善查詢效能的建議。
- 藉由變更[Azure SQL Database 服務層級](sql-database-performance-guidance.md)，變更資料庫中可用的資源。
