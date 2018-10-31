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
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 10/23/2018
ms.openlocfilehash: 0d728d81a29c5520938c8553c026727c0f94cc43
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956998"
---
# <a name="monitoring-and-performance-tuning"></a>監視和效能微調

Azure SQL Database 是有彈性的自動管理資料服務，您可以輕鬆監視使用狀況、新增或移除資源 (CPU、資源、I/O)、尋找可改善資料庫效能的建議，或讓資料庫來適應您的工作負載並自動最佳化效能。

## <a name="monitoring-database-performance"></a>監視資料庫效能

在 Azure 中監視 SQL Database 的效能，必須從監視您選擇之資料庫效能等級相關的資源使用率開始。 Azure SQL Database 可讓您檢閱[效能微調建議](sql-database-advisor.md)，找出不必變更資源卻能改善和最佳化查詢效能的機會。 遺漏索引與查詢最佳化不足是資料庫效能不佳的常見原因。 您可以套用這些調整建議來改善工作負載的效能。 您也可以套用所有發現的建議並驗證能否改善資料庫效能，讓 Azure SQL Database [自動最佳化查詢](sql-database-automatic-tuning.md)。

您有下列選項可以用來針對資料庫效能進行監視和疑難排解：

- 在 [Azure 入口網站](https://portal.azure.com)中，按一下 [SQL Database]、選取資料庫，然後使用監視圖表尋找接近其上限的資源。 預設會顯示 DTU 耗用量。 按一下 [編輯]  來變更所顯示的時間範圍和值。
- 使用[查詢效能深入解析](sql-database-query-performance.md)來找出花費最多資源的查詢。
- 使用 [SQL Database Advisor](sql-database-advisor-portal.md) 來檢視建立和卸除索引、參數化查詢，以及修正結構描述問題的建議。
- 使用 [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) 來自動監視您的資料庫效能。 在偵測到效能問題之後，系統就會產生包含問題詳細資料和「根本原因分析」(RCA) 的診斷記錄。 在可能的情況下會提供效能改進建議。
- [啟用自動調整](sql-database-automatic-tuning-enable.md)並讓 Azure SQL Database 自動修正發現的效能問題。
- 使用[動態管理檢視 (DMV)](sql-database-monitoring-with-dmvs.md)、[擴充的事件](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-db-diff-from-svr)和[查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)，更深入了解如何進行效能問題的疑難排解。

> [!TIP]
> 使用上述一或多個方法識別出效能問題之後，請參閱[效能指引](sql-database-performance-guidance.md)以尋找可用來改善 Azure SQL Database 效能的技術。

## <a name="monitor-databases-using-the-azure-portal"></a>使用 Azure 入口網站監視資料庫

在 [Azure 入口網站](https://portal.azure.com/)中，您可以透過選取資料庫，然後按一下 [監視] 圖表，來監視單一資料庫的使用率。 如此會帶出您可變更的 [度量] 視窗，只要按一下 [編輯圖表] 按鈕即可。 新增下列度量：

- CPU 百分比
- DTU 百分比
- 資料 IO 百分比
- 資料庫大小百分比

新增這些計量之後，您就能在 [計量] 視窗上含有詳細資訊的 [監視] 圖表中繼續檢視它們。 這四個度量都會顯示與資料庫 **DTU** 相對的平均使用率百分比。 如需服務層的相關詳細資訊，請參閱 [DTU 形式的購買模型](sql-database-service-tiers-dtu.md)和[虛擬核心形式的購買模型](sql-database-service-tiers-vcore.md)文章。  

![資料庫效能的服務層監視。](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

您也可以在效能度量中設定警示。 按一下 [度量] 視窗中的 [新增警示] 按鈕。 遵循精靈的指示以設定警示。 您可以選擇在度量超出或低於特定臨界值時發出警示。

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

當您識別出問題之後，您可以調整問題查詢，或是升級計算大小或服務層級，以增加您 Azure SQL Database 的容量來滿足 CPU 需求。 如需調整單一資料庫資源的相關資訊，請參閱[在 Azure SQL Database 中調整單一資料庫資源](sql-database-single-database-scale.md)，如需調整彈性集區資源的相關資訊，請參閱[在 Azure SQL Database 中調整彈性集區](sql-database-elastic-pool-scale.md)。 如需調整受控執行個體的相關資訊，請參閱[執行個體層級的資源限制](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)。

## <a name="waiting-related-performance-issues"></a>等候相關的效能問題

一旦確信您未遇到高 CPU 的執行相關效能問題之後，則您所遇到的就是等候相關的效能問題。 也就是說，並未有效率地使用您的 CPU 資源，因為 CPU 正在等候其他資源。 在此情況下，您的下一個步驟就是識別出哪些 CPU 資源正處於等候狀態。 顯示最常等候項目類型類別的最常見方法：

- [查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)可提供一段時間每個查詢的等候統計資料。 在查詢存放區中，等候類型會合併到等候類別中。 等候類別與等候類型的對應位於 [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql?view=sql-server-2017#wait-categories-mapping-table)。
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) 會傳回作業期間執行緒執行時所遇到的所有等候相關資訊。 您可以使用此彙總的檢視來診斷關於 Azure SQL Database 及特定查詢和批次的效能問題。
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) 會傳回正在等候某些資源的工作等候佇列相關資訊。

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

1. 若是單一資料庫，您可以視需要[變更服務層級](sql-database-service-tiers-dtu.md)或[計算資源](sql-database-service-tiers-vcore.md)以提升資料庫效能。
2. 若是多個資料庫，請考慮使用[彈性集區](sql-database-elastic-pool-guidance.md)來自動調整資源。

## <a name="tune-and-refactor-application-or-database-code"></a>調整和重構應用程式或資料庫程式碼

您可以變更應用程式程式碼，針對工作負載以最佳方式使用資料庫、變更索引、強制執行計劃，或使用提示來手動調整資料庫。 在[效能指引主題](sql-database-performance-guidance.md)文章中可找到手動調整或重寫程式碼的一些方針和秘訣。

## <a name="next-steps"></a>後續步驟

- 若要在 Azure SQL Database 中啟用自動調整，並讓自動調整功能完全管理您的工作負載，請參閱[啟用自動調整](sql-database-automatic-tuning-enable.md)。
- 若要使用手動調整，您可以檢閱 [Azure 入口網站中的調整建議](sql-database-advisor-portal.md)並手動套用可改善查詢效能的建議。
- 變更 [Azure SQL Database 服務層](sql-database-performance-guidance.md)來變更資料庫中可用的資源
