---
title: Azure SQL Database 功能比較 | Microsoft Docs
description: 本文會將 Azure SQL Database 的功能與「受控執行個體」彼此及與 SQL Server 做比較。
services: sql-database
author: jovanpop-msft
ms.reviewer: bonova, carlrab
ms.service: sql-database
ms.topic: article
ms.date: 03/30/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: bf9069df55352b4d7884e989be741fc42e06bfdf
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>功能比較：Azure SQL Database 與 SQL Server 

Azure SQL Database 與 SQL Server 共用通用的程式碼基底。 Azure SQL Database 所支援的 SQL Server 功能取決於您所建立的 Azure SQL Database 類型。 使用 Azure SQL Database 時，您可以將資料庫建立成[受控執行個體](sql-database-managed-instance.md) (目前為公開預覽版) 的一部分，或是將資料庫建立成單一資料庫或彈性集區的成員資料庫。 

Microsoft 會持續為 Azure SQL Database 新增功能。 請使用下列篩選來瀏覽 Azure 的「服務更新」網頁，以尋找最新更新：

* 篩選出 [SQL Database 服務](https://azure.microsoft.com/updates/?service=sql-database)。
* 篩選出 SQL Database 功能的「正式運作 [(GA)」公告](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) 。

## <a name="sql-server-and-sql-database-feature-support"></a>SQL Server 及 SQL Database 功能支援

下表列出 SQL Server 的主要功能，並提供有關該功能是部分支援還是完整支援功能的資訊，以及該功能的相關詳細資訊連結。 

| **SQL 功能** | **在 Azure SQL Database 中支援** | **受控執行個體 (預覽)** |
| --- | --- | --- |
| [一律加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | 是 - 請參閱[憑證存放區](sql-database-always-encrypted.md)和[金鑰保存庫](sql-database-always-encrypted-azure-key-vault.md) | 是 - 請參閱[憑證存放區](sql-database-always-encrypted.md)和[金鑰保存庫](sql-database-always-encrypted-azure-key-vault.md) |
| [AlwaysOn 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | 每個資料庫皆隨附[高可用性](sql-database-high-availability.md)。 [Azure SQL Database 的業務連續性概觀](sql-database-business-continuity.md)會討論災害復原 | 每個資料庫皆隨附[高可用性](sql-database-high-availability.md)。 [Azure SQL Database 的業務連續性概觀](sql-database-business-continuity.md)會討論災害復原 |
| [連結資料庫](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | 否 | 否 |
| [應用程式角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | yes | yes |
|[稽核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [是](sql-database-auditing.md)| [是](sql-database-managed-instance-auditing.md) |
| [自動備份](sql-database-automated-backups.md) | yes | yes |
| [自動調整 (計劃強制)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) \(機器翻譯\)| [是](sql-database-automatic-tuning.md)| [是](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [自動調整 (索引)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) \(機器翻譯\)| [是](sql-database-automatic-tuning.md)| 否 |
| [BACPAC 檔案 (匯出)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | 是 - 請參閱 [SQL Database 匯出](sql-database-export.md) | yes |
| [BACPAC 檔案 (匯入)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | 是 - 請參閱 [SQL Database 匯入](sql-database-import.md) | yes |
| [備份命令](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | 否，僅限系統起始的自動備份 - 請參閱[自動備份](sql-database-automated-backups.md) | 系統起始的自動備份和使用者起始的只複製備份 - 請參閱[備份差異](sql-database-managed-instance-transact-sql-information.md#backup) |
| [內建函式](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大部分 - 請參閱個別函式 | 是 - 請參閱[預存程序、函式、觸發程序差異](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [變更資料擷取](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 否 | yes |
| [變更追蹤](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | yes |yes |
| [定序陳述式](https://docs.microsoft.com/sql/t-sql/statements/collations) | yes | yes |
| [資料行存放區索引](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | 是 - [進階層、標準層 - S3 以上、一般用途層和業務關鍵層](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |yes |
| [通用語言執行平台 (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | 否 | 是 - 請參閱 [CLR 差異](sql-database-managed-instance-transact-sql-information.md#clr) |
| [自主資料庫](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | yes | yes |
| [自主使用者](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | yes | yes |
| [控制流程語言關鍵字](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | yes | yes |
| [跨資料庫查詢](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 - 請參閱[彈性查詢](sql-database-elastic-query-overview.md) | 是，再加上[彈性查詢](sql-database-elastic-query-overview.md) |
| [跨資料庫交易](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 | yes |
| [資料指標](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | yes |yes | 
| [資料壓縮](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | yes |yes |
| [Database Mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | 否 | yes |
| [資料移轉服務 (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | yes | yes |
| [資料庫鏡像](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 否 | 否 |
| [資料庫組態設定](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | yes | yes |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | 否 | 否 |
| [資料庫快照集](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | 否 | 否 |
| [資料類型](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | yes |yes |
| [DBCC 陳述式](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | 大部分 - 請參閱個別陳述式 | 是 - 請參閱 [DBCC 差異](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [DDL 陳述式](https://docs.microsoft.com/sql/t-sql/statements/statements) | 大部分 - 請參閱個別陳述式 | 是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [DDL 觸發程序](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | 僅限資料庫 |  yes |
| [分散式分割區檢視](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | 否 | yes |
| [分散式交易 - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 否 - 請參閱[彈性交易](sql-database-elastic-transactions-overview.md) |  否 - 請參閱[彈性交易](sql-database-elastic-transactions-overview.md) |
| [DML 陳述式](https://docs.microsoft.com/sql/t-sql/queries/queries) | yes | yes |
| [DML 觸發程序](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | 大部分 - 請參閱個別陳述式 |  yes |
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 大部分 - 請參閱個別 DMV |  是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
|[動態資料遮罩](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[是](sql-database-dynamic-data-masking-get-started.md)| yes |
| [彈性集區](sql-database-elastic-pool.md) | yes | 單一「受控執行個體」可以有共用相同資源集區的多個資料庫 |
| [事件通知](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | 否 - 請參閱[警示](sql-database-insights-alerts-portal.md) | yes |
| [運算式](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |yes | yes |
| [擴充事件](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | 部分請參閱 [SQL Database 中的擴充事件](sql-database-xevent-db-diff-from-svr.md) | 是 - 請參閱[擴充事件差異](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [擴充預存程序](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | 否 | 否 |
[檔案和檔案群組](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | 僅限主要檔案群組 | yes |
| [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | 否 | 否 |
| [全文檢索搜尋](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  不支援第三方斷詞工具 |不支援第三方斷詞工具 |
| [函式](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大部分 - 請參閱個別函式 | 是 - 請參閱[預存程序、函式、觸發程序差異](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [異地還原](sql-database-recovery-using-backups.md#geo-restore) | yes | 否 – 您可以還原定期建立的 COPY_ONLY 完整備份 - 請參閱[備份差異](sql-database-managed-instance-transact-sql-information.md#backup)和[還原差異](sql-database-managed-instance-transact-sql-information.md#restore-statement). |
| [異地複寫](sql-database-geo-replication-overview.md) | yes | 否 |
| [圖表處理](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | yes | yes |
| [記憶體內部最佳化](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | 是 - [僅限進階和業務關鍵層](sql-database-in-memory.md) | 否 |
| [JSON 資料支援](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | yes | yes |
| [語言元素](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | 大部分 - 請參閱個別元素 |  是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [連結的伺服器](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 - 請參閱[彈性查詢](sql-database-elastic-query-horizontal-partitioning.md) | 僅供 SQL Server 及 SQL Database |
| [記錄傳送](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | 每個資料庫皆隨附[高可用性](sql-database-high-availability.md)。 [Azure SQL Database 的業務連續性概觀](sql-database-business-continuity.md)會討論災害復原 |每個資料庫皆隨附[高可用性](sql-database-high-availability.md)。 [Azure SQL Database 的業務連續性概觀](sql-database-business-continuity.md)會討論災害復原 |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | 否 | 否 |
| [最低記錄大量匯入](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | 否 | 否 |
| [修改系統資料](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | 否 | yes |
| [線上索引作業](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | yes | yes |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|否|是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|yes|yes|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|否|是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|否|是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|yes|yes|
| [運算子](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | 大部分 - 請參閱個別運算子 |是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [分割](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | yes | yes |
| [資料庫還原時間點](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | 是 - 請參閱 [SQL Database 復原](sql-database-recovery-using-backups.md#point-in-time-restore) | 是 - 請參閱 [SQL Database 復原](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | 否 | 否 |
| [原則式管理](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | 否 | 否 |
| [述詞](https://docs.microsoft.com/sql/t-sql/queries/predicates) | yes | yes |
| [R 服務](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | 預覽版；請參閱[機器學習服務的新功能](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | 否 |
| [資源管理員](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | 否 | 否 |
| [RESTORE 陳述式](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | 否 | 是 - 請參閱[還原差異](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [從備份還原資料庫](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 僅限從自動備份 - 請參閱[SQL Database 復原](sql-database-recovery-using-backups.md) | 從自動備份 - 請參閱 [SQL Database 復原](sql-database-recovery-using-backups.md)和從完整備份 - 請參閱[備份差異](sql-database-managed-instance-transact-sql-information.md#backup) |
| [資料列層級安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | yes | yes |
| [語意搜尋](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | 否 | 否 |
| [序號](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | yes | yes |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | 否 | 是 - 請參閱 [Service Broker 差異](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [伺服器組態設定](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | 否 | 是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [SET 陳述式](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | 大部分 - 請參閱個別陳述式 | 是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md)|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) \(機器翻譯\) | yes | yes |
| [空間](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | yes | yes |
| [SQL 資料同步](sql-database-get-started-sql-data-sync.md) | yes | 否 |
| [SQL Operations Studio](https://docs.microsoft.com/sql/sql-operations-studio/what-is) \(機器翻譯\) | yes | yes |
| [SQL Server 代理程式](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | 否 - 請參閱[彈性工作](sql-database-elastic-jobs-getting-started.md) | 是 - 請參閱 [SQL Server Agent 差異](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | 否 - 請參閱 [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | 否 - 請參閱 [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [SQL Server 稽核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | 否 - 請參閱 [SQL Database 稽核](sql-database-auditing.md) | 是 - 請參閱[稽核差異](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [SQL Server Data Tools (SSDT)] (https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | yes | yes |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | 是，使用 Azure Data Factory (ADF) 環境中的受控 SSIS，其中的套件會儲存於 Azure SQL Database 所裝載的 SSISDB 中，並於 [Azure SSIS Integration Runtime](../data-factory/tutorial-deploy-ssis-packages-azure-powershell.md) 上執行。 | 是，使用 Azure Data Factory (ADF) 環境中的受控 SSIS，其中的套件會儲存於受控執行個體所裝載的 SSISDB 中，並於 [Azure SSIS Integration Runtime](../data-factory/tutorial-deploy-ssis-packages-azure-powershell.md) 上執行。 |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | yes | yes |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | yes | yes |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | 否 - 請參閱[擴充事件](sql-database-xevent-db-diff-from-svr.md) | yes |
| [SQL Server 複寫](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [僅限交易和快照複寫訂閱者](sql-database-cloud-migrate.md) | 否 |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | 否 - 請參閱 [Power BI](https://docs.microsoft.com/power-bi/) | 否 - 請參閱 [Power BI](https://docs.microsoft.com/power-bi/) |
| [預存程序](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | yes | yes |
| [系統預存函式](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 大部分 - 請參閱個別函式 | 是 - 請參閱[預存程序、函式、觸發程序差異](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [系統預存程序](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 部分 - 請參閱個別預存程序 | 是 - 請參閱[預存程序、函式、觸發程序差異](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [系統資料表](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | 部分 - 請參閱個別資料表 | 是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [系統目錄檢視](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 部分 - 請參閱個別檢視 | 是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [暫存資料表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | 本機和資料庫範圍的全域暫存資料表 | 本機和執行個體範圍的全域暫存資料表 |
| [暫存資料表](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | yes | yes |
|威脅偵測|  [是](sql-database-threat-detection.md)|[是](sql-database-managed-instance-threat-detection.md)|
| [追蹤旗標](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) \(機器翻譯\) | 否 | 否 |
| [變數](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | yes | yes |
| [透明資料加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | yes | 否，在公開預覽版中不支援 |
[VNet](../virtual-network/virtual-networks-overview.md) | 部分 - 請參閱 [VNET 端點](sql-database-vnet-service-endpoint-rule-overview.md) | 是，僅限 Resource Manager 模型 |
| [Windows Server 容錯移轉叢集](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | 每個資料庫皆隨附[高可用性](sql-database-high-availability.md)。 [Azure SQL Database 的業務連續性概觀](sql-database-business-continuity.md)會討論災害復原 | 每個資料庫皆隨附[高可用性](sql-database-high-availability.md)。 [Azure SQL Database 的業務連續性概觀](sql-database-business-continuity.md)會討論災害復原 |
| [XML 索引](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | yes | yes |

## <a name="next-steps"></a>後續步驟

- 如需了解 Azure SQL Database 服務的相關資訊，請參閱[什麼是 SQL Database？](sql-database-technical-overview.md)
- 如需有關「受控執行個體」的資訊，請參閱[什麼是受控執行個體？](sql-database-managed-instance.md)。
