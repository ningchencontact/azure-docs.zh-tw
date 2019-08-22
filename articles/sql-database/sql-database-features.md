---
title: Azure SQL Database 功能比較 | Microsoft Docs
description: 本文會比較不同類型之 Azure SQL Database 中可用的 SQL Server 功能。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: bonova, sstein
ms.date: 05/10/2019
ms.openlocfilehash: a8d36e48558432edfaa242b9db13c59adacf5619
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876346"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>功能比較：Azure SQL Database 與 SQL Server

Azure SQL Database 與 SQL Server 共用通用的程式碼基底。 Azure SQL Database 所支援的 SQL Server 功能取決於您所建立的 Azure SQL 資料庫類型。 使用 Azure SQL Database 時，您可以將資料庫建立為[受控執行個體](sql-database-managed-instance.md)的一部分、作為單一資料庫，或為彈性集區的一部分。

Microsoft 會持續為 Azure SQL Database 新增功能。 請使用下列篩選來瀏覽 Azure 的「服務更新」網頁，以尋找最新更新：

- 篩選出 [SQL Database 服務](https://azure.microsoft.com/updates/?service=sql-database)。
- 篩選出 SQL Database 功能的「正式運作 [(GA)」公告](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) 。

如果您需要更多有關差異的詳細資料, 您可以在[單一資料庫和彈性](sql-database-transact-sql-information.md)集區的個別頁面中找到它們, 或[受控執行個體](sql-database-managed-instance-transact-sql-information.md)。

## <a name="sql-features"></a>SQL 功能

下表列出 SQL Server 的主要功能, 並提供有關此功能在受控執行個體或單一資料庫和彈性集區中是否部分或完全支援的資訊, 以及有關此功能的詳細資訊連結。

| **SQL 功能** | **單一資料庫和彈性集區** | **受控實例** |
| --- | --- | --- |
| [一律加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | 是 - 請參閱[憑證存放區](sql-database-always-encrypted.md)和[金鑰保存庫](sql-database-always-encrypted-azure-key-vault.md) | 是 - 請參閱[憑證存放區](sql-database-always-encrypted.md)和[金鑰保存庫](sql-database-always-encrypted-azure-key-vault.md) |
| [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | 每個資料庫皆隨附[高可用性](sql-database-high-availability.md)。 [Azure SQL Database 的業務連續性概觀](sql-database-business-continuity.md)會討論災害復原 | 每個資料庫都包含[高可用性](sql-database-high-availability.md), 而且[不能由使用者管理](sql-database-managed-instance-transact-sql-information.md#always-on-availability)。 [Azure SQL Database 的業務連續性概觀](sql-database-business-continuity.md)會討論災害復原 |
| [連結資料庫](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | 否 | 否 |
| [應用程式角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | 是 | 是 |
| [稽核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [是](sql-database-auditing.md)| [是](sql-database-managed-instance-auditing.md), 但有一些[差異](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [自動備份](sql-database-automated-backups.md) | 是的。 每隔7天、差異12小時和記錄備份每隔5-10 分鐘會執行一次完整備份。 | 是的。 每隔7天、差異12小時和記錄備份每隔5-10 分鐘會執行一次完整備份。 |
| [自動調整 (計劃強制)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) \(機器翻譯\)| [是](sql-database-automatic-tuning.md)| [是](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [自動調整 (索引)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) \(機器翻譯\)| [是](sql-database-automatic-tuning.md)| 否 |
| [備份命令](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | 否，僅限系統起始的自動備份 - 請參閱[自動備份](sql-database-automated-backups.md) | 是, 使用者已起始僅限複本備份到 Azure Blob 儲存體 (使用者無法起始自動系統備份)-請參閱[備份差異](sql-database-managed-instance-transact-sql-information.md#backup) |
| [內建函式](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大部分 - 請參閱個別函式 | 是 - 請參閱[預存程序、函式、觸發程序差異](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) | 
| [BULK INSERT 語句](https://docs.microsoft.com/sql/relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server) | 是, 但只從 Azure Blob 儲存體作為來源。 | 是, 但只從 Azure Blob 儲存體做為來源-請參閱[差異](sql-database-managed-instance-transact-sql-information.md#bulk-insert--openrowset)。 |
| [憑證和非對稱金鑰](https://docs.microsoft.com/sql/relational-databases/security/sql-server-certificates-and-asymmetric-keys) | 是, 不需要存取和`BACKUP` `CREATE`作業的檔案系統。 | 是, 不需要存取`BACKUP`和`CREATE`作業的檔案系統-請參閱[憑證差異](sql-database-managed-instance-transact-sql-information.md#certificates)。 | 
| [變更資料捕獲-CDC](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 否 | 是 |
| [變更追蹤](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | 是 |是 |
| [定序 - 資料庫](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation) | 是 | 是 |
| [定序 - 伺服器/執行個體](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation) | 否, 一律會使用預設`SQL_Latin1_General_CP1_CI_AS`邏輯伺服器定序。 | 是, 可以在[建立實例](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)時設定, 而且之後無法更新。 |
| [資料行存放區索引](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | 是 - [進階層、標準層 - S3 以上、一般用途層和業務關鍵層](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |是 |
| [Common language runtime-CLR](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | 否 | 是, 但不能存取語句中`CREATE ASSEMBLY`的檔案系統-請參閱[CLR 差異](sql-database-managed-instance-transact-sql-information.md#clr) |
| [自主資料庫](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | 是 | 是 |
| [自主使用者](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | 是 | 是 |
| [控制流程語言關鍵字](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | 是 | 是 |
| [認證](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/credentials-database-engine) | 是, 但只有[資料庫範圍認證](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql)。 | 是, 但只支援 Azure Key Vault `SHARED ACCESS SIGNATURE`和, 請參閱[詳細資料](sql-database-managed-instance-transact-sql-information.md#credential) |
| [跨資料庫/三部分名稱查詢](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 - 請參閱[彈性查詢](sql-database-elastic-query-overview.md) | 是，再加上[彈性查詢](sql-database-elastic-query-overview.md) |
| [跨資料庫交易](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 | 是, 在實例內。 請參閱跨實例查詢的[連結伺服器差異](sql-database-managed-instance-transact-sql-information.md#linked-servers)。 |
| [資料指標](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 是 |是 |
| [資料壓縮](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | 是 |是 |
| [Database mail-DbMail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | 否 | 是 |
| [資料庫鏡像](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 否 | [否](sql-database-managed-instance-transact-sql-information.md#database-mirroring) |
| [資料庫組態設定](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | 是 | 是 |
| [資料庫快照集](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | 否 | 否 |
| [資料類型](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | 是 |是 |
| [DBCC 陳述式](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | 大部分 - 請參閱個別陳述式 | 是 - 請參閱 [DBCC 差異](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [DDL 陳述式](https://docs.microsoft.com/sql/t-sql/statements/statements) | 大部分 - 請參閱個別陳述式 | 是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [DDL 觸發程序](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | 僅限資料庫 |  是 |
| [分散式分割區檢視](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | 否 | 是 |
| [分散式交易 - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 否 - 請參閱[彈性交易](sql-database-elastic-transactions-overview.md) |  否-請參閱[連結的伺服器差異](sql-database-managed-instance-transact-sql-information.md#linked-servers) |
| [DML 陳述式](https://docs.microsoft.com/sql/t-sql/queries/queries) | 是 | 是 |
| [DML 觸發程序](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | 大部分 - 請參閱個別陳述式 |  是 |
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 大部分 - 請參閱個別 DMV |  是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [動態資料遮罩](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[是](sql-database-dynamic-data-masking-get-started.md)| [是](sql-database-dynamic-data-masking-get-started.md) |
| [事件通知](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | 否 - 請參閱[警示](sql-database-insights-alerts-portal.md) | 否 |
| [運算式](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |是 | 是 |
| [擴充的事件 (XEvent)](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | 部分請參閱 [SQL Database 中的擴充事件](sql-database-xevent-db-diff-from-svr.md) | 是 - 請參閱[擴充事件差異](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [擴充預存程序](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | 否 | 否 |
| [檔案和檔案群組](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | 僅限主要檔案群組 | 是的。 系統會自動指派檔案路徑, 而且無法在`ALTER DATABASE ADD FILE` [語句](sql-database-managed-instance-transact-sql-information.md#alter-database-statement)中指定檔案位置。  |
| [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | 否 | [否](sql-database-managed-instance-transact-sql-information.md#filestream-and-filetable) |
| [全文檢索搜尋 (FTS)](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  是, 但不支援協力廠商斷詞工具 | 是, 但[不支援協力廠商斷詞](sql-database-managed-instance-transact-sql-information.md#full-text-semantic-search)工具 |
| [函式](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大部分 - 請參閱個別函式 | 是 - 請參閱[預存程序、函式、觸發程序差異](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) |
| [圖表處理](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | 是 | 是 |
| [記憶體內部最佳化](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | 是- [Premium 和業務關鍵層僅](sql-database-in-memory.md)限於非持續性記憶體內建物件 (例如資料表類型) 的有限支援 | 是 - [僅限業務關鍵層](sql-database-managed-instance.md) |
| [JSON 資料支援](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [是](sql-database-json-features.md) | [是](sql-database-json-features.md) |
| [語言元素](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | 大部分 - 請參閱個別元素 |  是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [連結的伺服器](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 - 請參閱[彈性查詢](sql-database-elastic-query-horizontal-partitioning.md) | 是的。 只有在沒有分散式交易的情況下, 才[SQL Server 和 SQL Database](sql-database-managed-instance-transact-sql-information.md#linked-servers) 。 |
| 從檔案讀取的[連結伺服器](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine)(CSV、Excel)| 資料分割 使用[BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file)或[OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file)作為 CSV 格式的替代方法。 | 資料分割 使用[BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file)或[OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file)作為 CSV 格式的替代方法。 在[受控執行個體意見專案](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)上追蹤此要求|
| [記錄傳送](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | 每個資料庫皆隨附[高可用性](sql-database-high-availability.md)。 [Azure SQL Database 的業務連續性概觀](sql-database-business-continuity.md)會討論災害復原 | 以原生方式內建為 DMS 遷移程式的一部分。 無法當做高可用性解決方案使用, 因為其他[高可用性](sql-database-high-availability.md)方法會包含在每個資料庫中, 不建議使用記錄傳送作為 HA 替代方案。 嚴重損壞修復會在[Azure SQL Database 的商務持續性總覽](sql-database-business-continuity.md)中討論。 無法當做資料庫之間的複寫機制使用-在[業務關鍵層](sql-database-service-tier-business-critical.md)、[自動容錯移轉群組](sql-database-auto-failover-group.md)或[異動複寫](sql-database-managed-instance-transactional-replication.md)上使用次要複本做為替代方案。 |
| [登入和使用者](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) | 是, 但`CREATE`和`ALTER` login 語句不提供所有選項 (沒有 Windows 和伺服器層級的 Azure Active Directory 登入)。 `EXECUTE AS LOGIN`不受支援-請`EXECUTE AS USER`改用。  | 是, 但有一些[差異](sql-database-managed-instance-transact-sql-information.md#logins-and-users)。 Windows 登入不受支援, 而且應該以 Azure Active Directory 登入來取代。 |
| [最低記錄大量匯入](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | 否, 只支援完整復原模式。 | 否, 只支援完整復原模式。 |
| [修改系統資料](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | 否 | 是 |
| [OLE Automation](https://docs.microsoft.com/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) | 否 | 否 |
| [線上索引作業](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | 是 | 是 |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|否|是, 僅適用于其他 Azure SQL 資料庫和 SQL Server。 請參閱[t-sql 差異](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|是|是|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|否|是, 僅適用于其他 Azure SQL 資料庫和 SQL Server。 請參閱[t-sql 差異](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|是, 僅適用于從 Azure Blob 儲存體匯入。 |是, 僅適用于其他 Azure SQL 資料庫和 SQL Server, 以及從 Azure Blob 儲存體匯入。 請參閱[t-sql 差異](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|是|是|
| [運算子](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | 大部分 - 請參閱個別運算子 |是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [分割](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | 是 | 是 |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | 資料分割 您可以使用`OPENROWSET`函數查詢放在 Azure Blob 儲存體的檔案中的資料。 | 資料分割 您可以使用`OPENROWSET`函數查詢放在 Azure Blob 儲存體的檔案中的資料。 |
| [述詞](https://docs.microsoft.com/sql/t-sql/queries/predicates) | 是 | 是 |
| [查詢通知](https://docs.microsoft.com/sql/relational-databases/native-client/features/working-with-query-notifications) | 否 | 是 |
| [R 服務](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | 是，現已在[公開預覽版](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)中推出  | 否 |
| [復原模式](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) | 僅支援保證高可用性的完整復原。 簡單和大量記錄復原模式無法使用。 | 僅支援保證高可用性的完整復原。 簡單和大量記錄復原模式無法使用。 | 
| [資源管理員](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | 否 | 是 |
| [RESTORE 陳述式](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | 否 | 是, 在 Azure Blob 儲存體`FROM URL`上放置備份檔案的強制選項。 請參閱[還原差異](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [從備份還原資料庫](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 僅限從自動備份 - 請參閱[SQL Database 復原](sql-database-recovery-using-backups.md) | 從自動備份-請參閱[SQL Database](sql-database-recovery-using-backups.md)復原以及放在 Azure Blob 儲存體上的完整備份-請參閱[備份差異](sql-database-managed-instance-transact-sql-information.md#backup) |
| [將資料庫還原至 SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 資料分割 請使用 BACPAC 或 BCP, 而不是原生還原。 | 否, 因為在受控執行個體中使用的 SQL Server 資料庫引擎版本高於內部部署所使用之任何 RTM 版本的 SQL Server。 請改用 BACPAC、BCP 或異動複寫。 |
| [資料列層級安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | 是 | 是 |
| [語意搜尋](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | 否 | 否 |
| [序號](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | 是 | 是 |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | 否 | 是, 但只能在實例內。 請參閱[Service Broker 差異](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [伺服器組態設定](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | 否 | 是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [SET 陳述式](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | 大部分 - 請參閱個別陳述式 | 是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md)|
| [空間](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | 是 | 是 |
| [SQL Server 代理程式](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | 否 - 請參閱[彈性工作](elastic-jobs-overview.md) | 是 - 請參閱 [SQL Server Agent 差異](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server 稽核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | 否 - 請參閱 [SQL Database 稽核](sql-database-auditing.md) | 是 - 請參閱[稽核差異](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [預存程序](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | 是 | 是 |
| [系統預存函式](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 大部分 - 請參閱個別函式 | 是 - 請參閱[預存程序、函式、觸發程序差異](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) |
| [系統預存程序](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 部分 - 請參閱個別預存程序 | 是 - 請參閱[預存程序、函式、觸發程序差異](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) |
| [系統資料表](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | 部分 - 請參閱個別資料表 | 是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [系統目錄檢視](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 部分 - 請參閱個別檢視 | 是 - 請參閱 [T-SQL 差異](sql-database-managed-instance-transact-sql-information.md) |
| [TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database) | 是的。 [每個資料庫的每個核心32GB 大小](sql-database-vcore-resource-limits-single-databases.md)。 | 是的。 [適用于整個 GP 層的每個 vCore 的24GB 大小, 並受限於 BC 層的實例大小](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)  |
| [暫存資料表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | 本機和資料庫範圍的全域暫存資料表 | 本機和執行個體範圍的全域暫存資料表 |
| [暫存資料表](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [是](sql-database-temporal-tables.md) | [是](sql-database-temporal-tables.md) |
| 時區選擇 | 否 | [是](sql-database-managed-instance-timezone.md), 而且必須在建立受控執行個體時設定。 |
| 威脅偵測|  [是](sql-database-threat-detection.md)|[是](sql-database-managed-instance-threat-detection.md)|
| [追蹤旗標](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) \(機器翻譯\) | 否 | 是, 但只有有限的全域追蹤旗標集合。 請參閱[DBCC 差異](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [異動複寫](sql-database-managed-instance-transactional-replication.md) | 是,[僅限交易式和快照式複寫訂閱者](sql-database-single-database-migrate.md) | 是, 在[公開預覽](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance)中。 請參閱[這裡](sql-database-managed-instance-transact-sql-information.md#replication)的條件約束。 |
| [變數](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 是 | 是 |
| [透明資料加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | 是 - 僅限一般用途與商務關鍵服務層級| [是](transparent-data-encryption-azure-sql.md) |
| [Windows Server 容錯移轉叢集](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | 資料分割 提供[高可用性](sql-database-high-availability.md)的其他技術會包含在每個資料庫中。 [Azure SQL Database 的業務連續性概觀](sql-database-business-continuity.md)會討論災害復原 | 資料分割 提供[高可用性](sql-database-high-availability.md)的其他技術會包含在每個資料庫中。 [Azure SQL Database 的業務連續性概觀](sql-database-business-continuity.md)會討論災害復原 |
| [XML 索引](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | 是 | 是 |

## <a name="platform-capabilities"></a>平台功能

Azure 平臺提供一些 PaaS 功能, 可新增為標準資料庫功能的額外價值。 有一些外部服務可與 Azure SQL Database 服務搭配使用。 

| **平臺功能** | **單一資料庫和彈性集區** | **受控實例** |
| --- | --- | --- |
| [主動式異地複寫](sql-database-active-geo-replication.md) | 是-超大規模資料庫以外的所有服務層級 | 否, 請參閱[自動容錯移轉群組 (預覽)](sql-database-auto-failover-group.md)做為替代方案 |
| [自動容錯移轉群組](sql-database-auto-failover-group.md) | 是-超大規模資料庫以外的所有服務層級 | 是，現已在[公開預覽版](sql-database-auto-failover-group.md)中推出|
| 自動調整規模 | 是, 在[無伺服器模型](sql-database-serverless.md)中 | 否, 您必須選擇保留的計算和儲存體。 |
| [Azure Active Directory (AAD) 驗證](sql-database-aad-authentication.md) | 是的。 僅限 AAD 使用者。 | 是的。 包括伺服器層級的 AAD 登入。 |
| [Azure 資源健康狀態](/azure/service-health/resource-health-overview) | 是 | 否 |
| 備份保留 | 是的。 預設值為7天, 最大值為35天。 | 是的。 預設值為7天, 最大值為35天。 |
| [資料移轉服務 (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | 是 | 是 |
| 檔案系統存取 | 資料分割 使用[BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage)或[OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage) , 從 Azure Blob 儲存體存取和載入資料, 以做為替代方式。 | 資料分割 使用[BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage)或[OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage) , 從 Azure Blob 儲存體存取和載入資料, 以做為替代方式。 |
| [異地還原](sql-database-recovery-using-backups.md#geo-restore) | 是-超大規模資料庫以外的所有服務層級 | 是-使用[Azure PowerShell](https://medium.com/azure-sqldb-managed-instance/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa)。 |
| [超大規模資料庫架構](sql-database-service-tier-hyperscale.md) | 是 | 否 |
| [長期備份保留-LTR](sql-database-long-term-retention.md) | 是, 保持自動備份最多10年。 | 尚未提供。 使用`COPY_ONLY` [手動備份](sql-database-managed-instance-transact-sql-information.md#backup)做為暫時的因應措施。 |
| 暫停/繼續 | 是, 在[無伺服器模型](sql-database-serverless.md)中 | 否 | 
| [原則式管理](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | 否 | 否 |
| 公用 IP 位址 | 是的。 您可以使用防火牆或服務端點來限制存取權。  | 是的。 需要明確啟用, 而且必須在 NSG 規則中啟用埠3342。 如有需要, 可以停用公用 IP。 如需詳細資訊, 請參閱[公用端點](sql-database-managed-instance-public-endpoint-securely.md)。 | 
| [資料庫還原時間點](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | 是-超大規模資料庫以外的所有服務層級-請參閱[SQL Database](sql-database-recovery-using-backups.md#point-in-time-restore)復原 | 是 - 請參閱 [SQL Database 復原](sql-database-recovery-using-backups.md#point-in-time-restore) |
| 資源集區 | 是, 身為[彈性](sql-database-elastic-pool.md)集區 | 資料分割 單一受控 mnstance 可以有多個共用相同資源集區的資料庫。 受控實例無法共用資源。 |
| 相應增加或相應減少 (線上) | 是, 您可以在最短的停機時間內變更 DTU 或保留的虛擬核心或最大儲存體。 | 是, 您可以在最短的停機時間內變更保留的虛擬核心或最大儲存體。 |
| [SQL 分析](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) | 是 | 是 |
| [SQL 資料同步](sql-database-get-started-sql-data-sync.md) | 是 | 否 |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | 否, [azure Analysis Services](https://azure.microsoft.com/services/analysis-services/)是個別的 azure 雲端服務。 | 否, [azure Analysis Services](https://azure.microsoft.com/services/analysis-services/)是個別的 azure 雲端服務。 |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | 是，使用 Azure Data Factory (ADF) 環境中的受控 SSIS，其中的套件會儲存於 Azure SQL Database 所裝載的 SSISDB 中，並於 Azure SSIS Integration Runtime (IR) 上執行，請參閱[在 ADF 中建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。 <br/><br/>若要比較 SQL Database 伺服器與受控執行個體中的 SSIS 功能，請參閱[比較 Azure SQL Database 單一資料庫/彈性集區與受控執行個體](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)。 | 是，使用 Azure Data Factory (ADF) 環境中的受控 SSIS，其中的套件會儲存於受控執行個體所裝載的 SSISDB 中，並於 Azure SSIS Integration Runtime (IR) 上執行，請參閱[在 ADF 中建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。 <br/><br/>若要比較 SQL Database 與受控執行個體中的 SSIS 功能，請參閱[比較 Azure SQL Database 單一資料庫/彈性集區與受控執行個體](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)。 |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | 否 - 請參閱 [Power BI](https://docs.microsoft.com/power-bi/) | 否 - 請參閱 [Power BI](https://docs.microsoft.com/power-bi/) |
| [查詢效能深入解析 (QPI)](sql-database-query-performance.md) | 是 | 資料分割 使用 SQL Server Management Studio 和 Azure Data Studio 中的內建報表。 |
| [VNet](../virtual-network/virtual-networks-overview.md) | 部分, 它會使用[VNet 端點](sql-database-vnet-service-endpoint-rule-overview.md)來啟用限制存取 | 是, 受控執行個體會插入客戶的 VNet 中。 請參閱[子網](sql-database-managed-instance-transact-sql-information.md#subnet)和[VNet](sql-database-managed-instance-transact-sql-information.md#vnet) |
| VNet 服務端點 | [是](sql-database-vnet-service-endpoint-rule-overview.md) | 否 |

## <a name="tools"></a>工具
Azure SQL database 支援各種資料工具, 可協助您管理資料。

| **工具** | **單一資料庫和彈性集區** | **受控實例** |
| --- | --- | --- |
| Azure 入口網站 | 是 | 是 |
| Azure CLI | 是 | 是|
| [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) | 是 | 是 |
| Azure Powershell | 是 | 是 |
| [BACPAC 檔案 (匯出)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | 是 - 請參閱 [SQL Database 匯出](sql-database-export.md) | 是 - 請參閱 [SQL Database 匯出](sql-database-export.md) |
| [BACPAC 檔案 (匯入)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | 是 - 請參閱 [SQL Database 匯入](sql-database-import.md) | 是 - 請參閱 [SQL Database 匯入](sql-database-import.md) |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | 否 | 否 |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | 否 | 否 |
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) \(機器翻譯\) | [是](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) | 是[版本 150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) |
| [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | 是 | 是 |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | 是 | 是[18.0 版和更高版本](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | 是 | 是 |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | 否 - 請參閱[擴充事件](sql-database-xevent-db-diff-from-svr.md) | 是 |
| [System Center Operations Manager-SCOM](https://docs.microsoft.com/system-center/scom/welcome) | [是](https://www.microsoft.com/download/details.aspx?id=38829) | 否 |

## <a name="next-steps"></a>後續步驟

- 如需了解 Azure SQL Database 服務的相關資訊，請參閱[什麼是 SQL Database？](sql-database-technical-overview.md)
- 如需有關「受控執行個體」的資訊，請參閱[什麼是受控執行個體？](sql-database-managed-instance.md)。
