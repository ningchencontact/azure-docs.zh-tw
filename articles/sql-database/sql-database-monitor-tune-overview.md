---
title: 監視和效能微調 - Azure SQL Database | Microsoft Docs
description: 透過評估和改進來調整 Azure SQL Database 效能的秘訣。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: ac87ce2198296b82ef5655d7d75443a0bd49df3c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57875128"
---
# <a name="monitoring-and-performance-tuning"></a>監視和效能微調

Azure SQL Database 是有彈性的自動管理資料服務，您可以輕鬆監視使用狀況、新增或移除資源 (CPU、資源、I/O)、尋找可改善資料庫效能的建議，或讓資料庫來適應您的工作負載並自動最佳化效能。

## <a name="monitoring-database-performance"></a>監視資料庫效能

在 Azure 中監視 SQL Database 的效能，必須從監視您選擇之資料庫效能等級相關的資源使用率開始。 Azure SQL Database 可讓您檢閱[效能微調建議](sql-database-advisor.md)，找出不必變更資源卻能改善和最佳化查詢效能的機會。 遺漏索引與查詢最佳化不足是資料庫效能不佳的常見原因。 您可以套用這些調整建議來改善工作負載的效能。 您也可以套用所有發現的建議並驗證能否改善資料庫效能，讓 Azure SQL Database [自動最佳化查詢](sql-database-automatic-tuning.md)。

您有下列選項可以用來針對資料庫效能進行監視和疑難排解：

- 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [SQL Database]、選取資料庫，然後使用監視圖表尋找接近其上限的資源。 默认情况下将显示 DTU 消耗量。 按一下 [編輯]  來變更所顯示的時間範圍和值。
- 使用[查詢效能深入解析](sql-database-query-performance.md)來找出花費最多資源的查詢。
- 使用 [SQL Database Advisor](sql-database-advisor-portal.md) 來檢視建立和卸除索引、參數化查詢，以及修正結構描述問題的建議。
- 使用 [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) 來自動監視您的資料庫效能。 在偵測到效能問題之後，系統就會產生包含問題詳細資料和「根本原因分析」(RCA) 的診斷記錄。 在可能的情況下會提供效能改進建議。
- [啟用自動調整](sql-database-automatic-tuning-enable.md)並讓 Azure SQL Database 自動修正發現的效能問題。
- 使用[動態管理檢視 (DMV)](sql-database-monitoring-with-dmvs.md)、[擴充的事件](sql-database-xevent-db-diff-from-svr.md)和[查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)，更深入了解如何進行效能問題的疑難排解。

> [!TIP]
> 使用上述一或多個方法識別出效能問題之後，請參閱[效能指引](sql-database-performance-guidance.md)以尋找可用來改善 Azure SQL Database 效能的技術。

## <a name="monitor-databases-using-the-azure-portal"></a>使用 Azure 入口網站監視資料庫

在  [Azure 入口網站](https://portal.azure.com/)，您可以監視個別資料庫的使用率選取您的資料庫，然後按一下**監視**圖表。 如此會帶出您可變更的 [度量] 視窗，只要按一下 [編輯圖表] 按鈕即可。 添加以下指标：

- CPU 百分比
- DTU 百分比
- 資料 IO 百分比
- 資料庫大小百分比

新增這些計量之後，您就能在 [計量] 視窗上含有詳細資訊的 [監視] 圖表中繼續檢視它們。 這四個度量都會顯示與資料庫 **DTU** 相對的平均使用率百分比。 如需服務層的相關詳細資訊，請參閱 [DTU 形式的購買模型](sql-database-service-tiers-dtu.md)和[虛擬核心形式的購買模型](sql-database-service-tiers-vcore.md)文章。  

![資料庫效能的服務層監視。](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

还可针对性能指标配置警报。 按一下 [度量] 視窗中的 [新增警示] 按鈕。 按照向导说明来配置警报。 您可以選擇在度量超出或低於特定臨界值時發出警示。

例如，如果您預期資料庫中的工作負載會成長，可以選擇設定電子郵件警示，以便在資料庫的任何效能度量達到 80% 時收到警示。 您可以使用此警示作為早期警告，協助您判斷何時需要切換至更高的計算大小。

效能度量也可協助您判斷您是否能夠降級至較低的計算大小。 假設您使用標準 S2 資料庫，且所有效能度量皆顯示在指定時間內，資料庫平均使用率沒有超過 10%。 則該資料庫可能在標準 S1 中會運作得不錯。 不過，在您決定將資料庫移至較低計算大小前，請先注意暴增或大幅變動的工作負載。

## <a name="troubleshoot-performance-issues"></a>針對效能問題進行疑難排解

若要診斷並解決效能問題，一開始先了解每個作用中查詢的狀態，以及導致與每個工作負載狀態相關之效能問題的情況。 若要改善 Azure SQL Database 效能，請了解您應用程式的每個作用中查詢是在執行中還是等候狀態。 在 Azure SQL Database 中進行效能問題的疑難排解時，請在閱讀本文時牢記下圖以診斷並解決效能問題。

![工作負載狀態](./media/sql-database-monitor-tune-overview/workload-states.png)

對於發生效能問題的工作負載，效能問題可能是來自 CPU 爭用 (**與執行相關**的狀況)，或是個別查詢正在等候某些內容 (**與等候相關**的狀況)。

## <a name="running-related-performance-issues"></a>執行相關的效能問題

一般來說，如果您的 CPU 使用率持續為等於或高於 80%，就有執行相關的效能問題。 如果您有執行相關的問題，則其可能因為 CPU 資源不足所導致，或者可能與下列其中一個情況相關：

- 太多執行的查詢
- 太多編譯的查詢
- 一個或多個正在執行的查詢在使用次佳的查詢計劃

如果您判斷有執行相關的效能問題，您的目標就是使用一或多個方法來識別出確切的問題。 用來識別執行相關問題的最常見方法如下：

- 使用 [Azure 入口網站](#monitor-databases-using-the-azure-portal)來監視 CPU 百分比使用率。
- 使用下列[動態管理檢視](sql-database-monitoring-with-dmvs.md)：

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) 會傳回 Azure SQL Database 資料庫的 CPU、I/O 和記憶體耗用量。 每 15 秒會有一列，即使資料庫沒有任何活動，也會有一列。 歷程記錄資料會保留一個小時。
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) 會傳回 Azure SQL Database 的 CPU 使用量和儲存體資料。 每五分鐘會收集和彙總資料一次。

> [!IMPORTANT]
> 如需一組使用這些 DMV 進行 CPU 使用率問題疑難排解的 T-SQL 查詢，請參閱[識別 CPU 效能問題](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues)。

### <a name="troubleshoot-queries-with-parameter-sensitive-query-execution-plan-issues"></a>針對包含參數區分查詢執行計畫問題的查詢進行疑難排解

參數區分計畫 (PSP) 問題是指查詢最佳化工具產生僅適用於特定參數值 (或一組值) 的查詢執行計畫的情況，然後快取計畫不適用於連續執行中使用的參數值。 然後，非最佳計畫可能會導致查詢效能問題和整體工作負載輸送量降低。

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

- 此[參數的特徵](https://blogs.msdn.microsoft.com/queryoptteam/20../../i-smell-a-parameter/) \(英文\) 部落格文章
- 此[參數探查問題和因應措施](https://blogs.msdn.microsoft.com/turgays/20../../parameter-sniffing-problem-and-possible-workarounds/) \(英文\) 部落格文章
- 此[大象與老鼠的參數探查](https://www.brentozar.com/archive/2013/06/the-elephant-and-the-mouse-or-parameter-sniffing-in-sql-server/) \(英文\) 部落格文章
- 此[動態 sql 與參數化查詢的計畫品質](https://blogs.msdn.microsoft.com/conor_cunningham_msft/20../../conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/) \(英文\) 部落格文章

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>疑難排解由於不正確的參數化所導致的編譯活動失敗

當查詢包含常值時，資料庫引擎會選擇自動參數化陳述式，或使用者可以明確地將它參數化以減少編譯數目。 使用相同模式但不同常值之查詢的大量編譯，可能會導致高 CPU 使用率。 同樣地，如果您僅部分參數化繼續使用常值的查詢，則資料庫引擎不會進一步參數化。  以下是部分參數化查詢的範例：

```sql
select * from t1 join t2 on t1.c1=t2.c1
where t1.c1=@p1 and t2.c2='961C3970-0E54-4E8E-82B6-5545BE897F8F'
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

### <a name="resolve-problem-queries-or-provide-more-resources"></a>解決問題查詢或提供更多資源

當您識別出問題之後，您可以調整問題查詢，或是升級計算大小或服務層級，以增加您 Azure SQL Database 的容量來滿足 CPU 需求。 如需調整單一資料庫資源的相關資訊，請參閱[在 Azure SQL Database 中調整單一資料庫資源](sql-database-single-database-scale.md)，如需調整彈性集區資源的相關資訊，請參閱[在 Azure SQL Database 中調整彈性集區](sql-database-elastic-pool-scale.md)。 如需調整受控執行個體的相關資訊，請參閱[執行個體層級的資源限制](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)。

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

查詢存放區和等候統計資料追蹤動態管理檢視僅顯示已完成和逾時查詢的結果，並且不會顯示目前正在執行之陳述式的資料 (直到它們完成)。  動態管理檢視 [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 允許您追蹤目前正在執行的查詢和相關聯的背景工作時間。

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

## <a name="improving-database-performance-with-more-resources"></a>使用更多資源提升資料庫效能

最後，如果沒有可改善資料庫效能的可操作項目，您可以變更 Azure SQL Database 中可用的資源數量。 您可以隨時變更單一資料庫的 [DTU 服務層](sql-database-service-tiers-dtu.md)或增加彈性集區的 eDTU。 或者，如果您使用[虛擬核心形式的購買模型](sql-database-service-tiers-vcore.md)，便可以變更服務層或增加配置給資料庫的資源。

1. 若是單一資料庫，您可以視需要[變更服務層級](sql-database-single-database-scale.md)或[計算資源](sql-database-single-database-scale.md)以提升資料庫效能。
2. 若是多個資料庫，請考慮使用[彈性集區](sql-database-elastic-pool-guidance.md)來自動調整資源。

## <a name="tune-and-refactor-application-or-database-code"></a>調整和重構應用程式或資料庫程式碼

您可以變更應用程式程式碼，針對工作負載以最佳方式使用資料庫、變更索引、強制執行計劃，或使用提示來手動調整資料庫。 在[效能指引主題](sql-database-performance-guidance.md)文章中可找到手動調整或重寫程式碼的一些方針和秘訣。

## <a name="next-steps"></a>後續步驟

- 若要在 Azure SQL Database 中啟用自動調整，並讓自動調整功能完全管理您的工作負載，請參閱[啟用自動調整](sql-database-automatic-tuning-enable.md)。
- 若要使用手動調整，您可以檢閱 [Azure 入口網站中的調整建議](sql-database-advisor-portal.md)並手動套用可改善查詢效能的建議。
- 變更 [Azure SQL Database 服務層](sql-database-performance-guidance.md)來變更資料庫中可用的資源
