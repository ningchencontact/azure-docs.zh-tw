---
title: 超大規模資料庫服務層級中的 Azure SQL Database 效能診斷 |Microsoft Docs
description: 本文說明如何針對 Azure SQL Database 中的超大規模資料庫效能問題進行疑難排解。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: 92a1fda85e5ee49f12a13123e8a296492fd9eb4b
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598179"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>SQL 超大規模資料庫效能疑難排解診斷


若要對超大規模資料庫資料庫中的效能問題進行疑難排解，Azure SQL database 計算節點上的[一般效能微調方法](sql-database-monitor-tune-overview.md)是效能調查的起點。 不過，假設超大規模資料庫的[分散式架構](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)，則已新增額外的診斷來提供協助。 本文說明超大規模資料庫特有的診斷資料。


## <a name="log-rate-throttling-waits"></a>記錄速率節流等候


每個 Azure SQL Database 服務層級都會透過[記錄速率治理](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)來強制執行記錄產生率限制。 在超大規模資料庫中，不論服務層級為何，記錄檔產生的限制目前都設定為 100 MB/秒。 不過，有時主要計算複本上的記錄產生速率必須經過節流處理，以維持復原能力 Sla。 當[頁面伺服器或另一個計算複本](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)大幅落後套用記錄服務的新記錄檔記錄時，就會發生此節流。

下列等候類型（在[_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)中）說明在主要計算複本上進行記錄速率節流的原因：

|等候類型    |描述                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | 當超大規模資料庫資料庫主要計算節點記錄產生速率因為頁面伺服器的延遲記錄耗用量而受到節流時發生。         |
|RBIO_RG_DESTAGE        | 當超大規模資料庫資料庫計算節點記錄產生速率因為長期記錄儲存體的延遲記錄耗用量而受到節流時發生。         |
|RBIO_RG_REPLICA        | 當超大規模資料庫資料庫計算節點記錄產生速率因為可讀取的次要複本延遲記錄耗用量而受到節流時發生。         |
|RBIO_RG_LOCALDESTAGE   | 當超大規模資料庫資料庫計算節點記錄產生速率因為記錄服務的延遲記錄耗用量而受到節流時發生。         |


## <a name="page-server-reads"></a>頁面伺服器讀取

計算複本不會在本機快取資料庫的完整複本。 計算複本的本機資料會儲存在緩衝集區（記憶體中）和本機復原緩衝集區延伸模組（RBPEX）快取中，這是資料頁的部分（非涵蓋）快取。 這個本機 RBPEX 快取的大小會按比例調整成計算大小，而計算層的記憶體則是3倍。 RBPEX 類似于緩衝集區，因為它具有最常存取的資料。 另一方面，每個頁面伺服器都有一個涵蓋的 RBPEX 快取，可供其維護的資料庫部分使用。
 
在計算複本上發出讀取時，如果資料不存在於緩衝集區或本機 RBPEX 快取中，就會發出 getPage （pageId，LSN）函式呼叫，而且會從對應的頁面伺服器提取該頁面。 從頁面伺服器讀取是遠端讀取，因此速度比從本機 RBPEX 讀取還慢。 針對 IO 相關的效能問題進行疑難排解時，我們必須能夠透過相對較慢的遠端頁面伺服器讀取來得知已完成的 Io 數目。

有數個 Dmv 和擴充事件具有資料行和欄位，可指定頁面伺服器的遠端讀取數目，而這些資料可以與讀取總數進行比較。 

- 報表頁面伺服器讀取的資料行可在執行 Dmv 中使用，例如：
    - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys.databases _exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
- 頁面伺服器讀取會加入至下列擴充事件：
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - 查詢-store_execution_runtime_info
- ActualPageServerReads/ActualPageServerReadAheads 會加入至實際計畫的查詢計劃 XML。 例如：

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> 若要在 SSMS 的 [查詢計劃屬性] 視窗中查看這些屬性，您將需要 SSMS 18.3 或更新版本。


## <a name="virtual-file-stats-and-io-accounting"></a>虛擬檔案統計資料和 IO 帳戶處理

在 Azure SQL Database 中， [_io_virtual_file_stats （）](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF 是監視 SQL Server io 的主要方式。 超大規模資料庫上的 IO 特性因其[分散式架構](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)而有所不同。 在本節中，我們將焦點放在資料檔案的 IO （讀取和寫入），如此 DMF 中所示。 在超大規模資料庫中，此 DMF 中可見的每個資料檔都會對應至遠端頁面伺服器。 此處所提及的 RBPEX 快取是以本機 SSD 為基礎的快取，其為計算複本上的非涵蓋快取。


### <a name="local-rbpex-cache-usage"></a>本機 RBPEX 快取使用量

本機 RBPEX 快取存在於本機 SSD 儲存體上的計算節點上。 因此，此 RBPEX 快取上的 IO 比遠端頁面伺服器上的 IO 更快速。 目前，超大規模資料庫資料庫中的[_io_virtual_file_stats （）](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/)有一個特殊資料列，報告在計算複本上本機 RBPEX 快取上完成的 io。 此資料列的值為0，適用于 `database_id` 和 `file_id` 資料行。 例如，下列查詢會傳回自資料庫啟動後的 RBPEX 使用量統計資料。

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

在 RBPEX 上完成的讀取比例與所有其他資料檔案上的匯總讀取數相比，會提供 RBPEX 快取點擊率。


### <a name="data-reads"></a>資料讀取

- 當計算複本上的 SQL Server 引擎發出讀取時，它們可能是由本機 RBPEX 快取或遠端頁面伺服器提供，或是在讀取多個頁面的情況下結合這兩者。
- 當計算複本從特定檔案讀取某些頁面時（例如 file_id 1），如果此資料僅位於本機 RBPEX 快取中，則會針對 file_id 0 （RBPEX）考慮此讀取的所有 IO。 如果該資料的某些部分位於本機 RBPEX 快取中，而某些部分位於遠端頁面伺服器上，則 IO 會針對從 RBPEX 提供的部分進行 file_id 0 的計算，而從遠端頁面伺服器提供的元件則會計入 file_id 1。 
- 當計算複本從頁面伺服器要求特定[lsn](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/)的頁面時，如果頁面伺服器未趕上到所要求的 lsn，計算複本上的讀取將會等到頁面伺服器一直到達之後，才會將頁面傳回到計算複本。 如果是從計算複本上的頁面伺服器讀取，您會看到 PAGEIOLATCH_ * 等候類型（如果它正在等待該 IO）。 這段等候時間包含將頁面伺服器上要求的頁面攔截到所需 LSN 的時間，以及將頁面從頁面伺服器傳輸到計算複本所需的時間。
- 大量讀取（例如預先讀取）通常是使用「[散佈-收集」讀取](/sql/relational-databases/reading-pages/)來完成。 這允許一次讀取最多 4 MB 的頁面，視為 SQL Server 引擎中的單一讀取。 不過，當讀取的資料在 RBPEX 時，這些讀取會視為多個個別的 8 KB 讀取，因為緩衝集區和 RBPEX 一律會使用 8 KB 的頁面。 因此，針對 RBPEX 看到的讀取 Io 數目可能會大於引擎所執行的實際 Io 數目。


### <a name="data-writes"></a>資料寫入

- 主要計算複本不會直接寫入頁面伺服器。 相反地，記錄服務的記錄檔記錄會在對應的頁面伺服器上重新執行。 
- 在計算複本上發生的寫入主要是寫入本機 RBPEX （file_id 0）。 對於大於 8 KB （也就是使用[收集-write](/sql/relational-databases/writing-pages/)完成的邏輯檔案）的寫入，每個寫入作業都會轉譯為多個 8 kb 的個別寫入至 RBPEX，因為緩衝集區和 RBPEX 一律使用 8 kb 頁面。 因此，針對 RBPEX 所見的寫入 Io 數目可能會大於引擎所執行的實際 Io 數目。
- 非 RBPEX 檔案，或對應至頁面伺服器的 file_id 0 以外的資料檔案，也會顯示寫入。 在超大規模資料庫服務層級中，這些寫入會進行模擬，因為計算複本永遠不會直接寫入頁面伺服器。 寫入 IOPS 和輸送量是在計算複本上發生的，但 file_id 0 以外的資料檔案延遲不會反映頁面伺服器寫入的實際延遲。

### <a name="log-writes"></a>記錄檔寫入

- 在主要計算中，會在 _io_virtual_file_stats 的 file_id 2 中考慮記錄寫入。 主要計算上的記錄寫入是記錄登陸區域的寫入。
- 認可上的次要複本上不會強化記錄檔記錄。 在超大規模資料庫中，Xlog 服務會將記錄套用到遠端複本。 由於記錄檔寫入實際上不會發生在次要複本上，因此次要複本上的任何記錄 IO 帳戶處理僅供追蹤之用。

## <a name="additional-resources"></a>其他資源

- 如需超大規模資料庫單一資料庫的 vCore 資源限制，請參閱[超大規模資料庫服務層 VCore 限制](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier-for-provisioned-compute)
- 如需 Azure SQL Database 的效能微調，請參閱[Azure SQL Database 中的查詢效能](sql-database-performance-guidance.md)
- 如需使用查詢存放區進行效能調整，請參閱[使用查詢存放區的效能監視](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- 如需 DMV 監視腳本，請參閱[使用動態管理檢視監視效能 Azure SQL Database](sql-database-monitoring-with-dmvs.md)
