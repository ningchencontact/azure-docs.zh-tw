---
title: Azure SQL Database 受控執行個體 T-SQL 差異 |Microsoft Docs
description: 本文將討論 Azure SQL Database 中的受控執行個體與 SQL Server之間的 T-SQL 差異
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
manager: craigg
ms.date: 07/07/2019
ms.custom: seoapril2019
ms.openlocfilehash: 6b0e10ce48088853090958dca9d8c1fad20780e7
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723252"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Azure SQL Database 受控執行個體的 T-SQL 差異

本文摘要說明，並說明 Azure SQL Database 受控執行個體和內部部署 SQL Server Database Engine 之間的語法和行為差異。 將討論下列主題： <a name="Differences"></a>

- [可用性](#availability)包含的差異[Alwayson](#always-on-availability)並[備份](#backup)。
- [安全性](#security)包含的差異[稽核](#auditing)，[憑證](#certificates)，[認證](#credential)，[密碼編譯提供者](#cryptographic-providers)，[登入和使用者](#logins-and-users)，而[服務金鑰和服務主要金鑰](#service-key-and-service-master-key)。
- [組態](#configuration)包含的差異[緩衝集區延伸模組](#buffer-pool-extension)，[定序](#collation)，[相容性層級](#compatibility-levels)，[資料庫鏡像](#database-mirroring)，[資料庫選項](#database-options)， [SQL Server Agent](#sql-server-agent)，和[資料表選項](#tables)。
- [功能](#functionalities)包含[大量插入/OPENROWSET](#bulk-insert--openrowset)， [CLR](#clr)， [DBCC](#dbcc)，[分散式交易](#distributed-transactions)， [擴充事件](#extended-events)，[外部程式庫](#external-libraries)， [filestream 和 FileTable](#filestream-and-filetable)，[全文檢索語意搜尋](#full-text-semantic-search)， [連結的伺服器](#linked-servers)， [PolyBase](#polybase)，[複寫](#replication)，[還原](#restore-statement)， [Service Broker](#service-broker)， [預存程序、 函數和觸發程序](#stored-procedures-functions-and-triggers)。
- [環境設定](#Environment)例如 Vnet 和子網路組態。
- [中具有不同行為的功能受管理的執行個體](#Changes)。
- [暫時性限制與已知的問題](#Issues)。

受控執行個體的部署選項提供對內部部署 SQL Server 資料庫引擎的高度相容性。 受控執行個體支援大部分的 SQL Server 資料庫引擎功能。

![移轉](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>可用性

### <a name="always-on-availability"></a>Always On

[高可用性](sql-database-high-availability.md)內建受控執行個體，而無法由使用者控制。 不支援下列陳述式：

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr)子句[ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql)陳述式

### <a name="backup"></a>Backup

受管理的執行個體都有自動備份，因此使用者可以建立完整資料庫`COPY_ONLY`備份。 不支援差異、 記錄和檔案快照集備份。

- 與受管理的執行個體中，您可以執行個體資料庫只備份至 Azure Blob 儲存體帳戶：
  - 只支援 `BACKUP TO URL`。
  - `FILE``TAPE`，並不支援備份裝置。
- 大部分的一般`WITH`支援選項。
  - `COPY_ONLY` 是必要的。
  - 不支援 `FILE_SNAPSHOT`。
  - 磁帶選項： `REWIND`， `NOREWIND`， `UNLOAD`，和`NOUNLOAD`不支援。
  - 記錄特定選項： `NORECOVERY`， `STANDBY`，和`NO_TRUNCATE`不支援。

限制： 

- 與受管理的執行個體中，您可以執行個體資料庫備份至備份最多可包含 32 個等量分散，這足夠資料庫最多 4 TB 如果使用備份壓縮。
- 使用最大的備份等量大小`BACKUP`受管理的執行個體中的命令為 195 GB，這是最大 blob 大小。 在備份命令中增加條帶 (Stripe) 數目，可減少個別條帶 (Stripe) 的大小並維持在此限制內。

    > [!TIP]
    > 若要解決這項限制，當您從 SQL Server 中的內部部署環境或虛擬機器中備份資料庫時，您可以：
    >
    > - 備份至`DISK`而不是備份至`URL`。
    > - 將備份檔案上傳至 Blob 儲存體。
    > - 將還原至受控執行個體。
    >
    > `Restore`受管理的執行個體中的命令會支援備份檔案中較大 blob 大小因為不同的 blob 類型用來上傳備份檔案的儲存體。

如需使用 T-SQL 進行備份的相關資訊，請參閱[備份](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql)。

## <a name="security"></a>安全性

### <a name="auditing"></a>稽核

Azure SQL Database 中的資料庫和 SQL Server 中的資料庫兩者之間的主要稽核差異在於：

- 在 Azure SQL Database 受控執行個體部署選項，稽核伺服器層級的運作方式。 `.xel`記錄檔會儲存在 Azure Blob 儲存體中。
- 在使用 Azure SQL Database 中的單一資料庫和彈性集區部署選項時，稽核作業則會在資料庫層級運作。
- 在 SQL Server 內部部署或虛擬機器中，稽核伺服器層級的運作方式。 事件會儲存在檔案系統 」 或 「 Windows 事件記錄檔。
 
受控執行個體中的 XEvent 稽核支援 Azure Blob 儲存體目標。 不支援檔案與 Windows 記錄。

向 Azure Blob 儲存體進行稽核的 `CREATE AUDIT` 語法有一個主要差異：

- 一種新語法`TO URL`是前提是您可用來指定 Azure Blob 儲存體容器的 URL 位置`.xel`檔案會放置。
- 語法`TO FILE`不支援，因為受控執行個體無法存取 Windows 檔案共用。

如需詳細資訊，請參閱： 

- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [稽核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>憑證

受管理的執行個體無法存取檔案共用及 Windows 資料夾，因此適用下列限制：

- `CREATE FROM` / `BACKUP TO`檔案不支援的憑證。
- `CREATE` / `BACKUP`憑證從`FILE` / `ASSEMBLY`不受支援。 無法使用私密金鑰檔案。 

請參閱 [CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) 和 [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql)。 
 
**因應措施**：為憑證或私用的索引鍵編寫指令碼，將儲存為.sql 檔案，並建立從二進位檔：

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>認證

僅支援Azure Key Vault 和 `SHARED ACCESS SIGNATURE` 身分識別。 不支援 Windows 使用者。

請參閱 [CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) 和 [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql)。

### <a name="cryptographic-providers"></a>密碼編譯提供者

受管理的執行個體無法存取檔案，所以無法建立密碼編譯提供者：

- 不支援 `CREATE CRYPTOGRAPHIC PROVIDER`。 請參閱 [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql)。
- 不支援 `ALTER CRYPTOGRAPHIC PROVIDER`。 請參閱 [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)。

### <a name="logins-and-users"></a>登入和使用者

- 使用所建立的 SQL 登入`FROM CERTIFICATE`， `FROM ASYMMETRIC KEY`，和`FROM SID`支援。 請參閱 [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql)。
- Azure Active Directory (Azure AD) 的伺服器主體 （登入） 以建立[CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)語法或[建立使用者的登入 [Azure AD 登入]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current)支援語法 （公開預覽狀態）。 在伺服器層級，會建立這些登入。

    受控執行個體支援使用 `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` 語法的 Azure AD 資料庫主體。 這項功能就是所謂 Azure AD 自主資料庫使用者。

- 使用建立的 Windows 登入`CREATE LOGIN ... FROM WINDOWS`語法不受支援。 使用 Azure Active Directory 登入和使用者。
- 建立執行個體的 Azure AD 使用者具有[不受限制的系統管理員權限](sql-database-manage-logins.md#unrestricted-administrative-accounts)。
- 非系統管理員的 Azure AD 資料庫層級的使用者可以建立使用`CREATE USER ... FROM EXTERNAL PROVIDER`語法。 請參閱 [CREATE USER ...FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users)。
- Azure AD 伺服器主體 （登入） 支援只有一個受控執行個體中的 SQL 功能。 需要跨執行個體互動，無論是否它們是在相同的 Azure AD 租用戶或不同的租用戶的功能不支援 Azure AD 使用者。 這類功能的範例如下：

  - SQL 異動複寫中。
  - 連結伺服器。

- 不支援將對應至 Azure AD 群組的 Azure AD 登入設定為資料庫擁有者。
- 支援的 Azure AD 使用其他 Azure AD 主體的伺服器層級主體的模擬，例如[EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql)子句。 EXECUTE AS 限制如下：

  - EXECUTE AS USER 不支援 Azure AD 使用者名稱與網域不同時登入名稱。 舉例來說，當透過語法建立使用者 [myAadUser] 從登入建立使用者 [john@contoso.com]，並模擬時會透過 EXEC AS USER = _myAadUser_。 當您建立**使用者**從 Azure AD 伺服器主體 （登入），指定從相同的 login_name user_name**登入**。
  - 只有 SQL Server 層級主體 （登入） 屬於`sysadmin`角色可以執行下列作業的目標 Azure AD 主體：

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Azure AD 伺服器主體 （登入） 的公開預覽限制：

  - 受控執行個體的 active Directory 系統管理員限制：

    - 用來設定受管理的執行個體的 Azure AD 系統管理員無法用來建立 Azure AD 伺服器主體 （登入） 內的 managed 執行個體中。 您必須建立第一個 Azure AD 伺服器主體 （登入） 使用 SQL Server 帳戶的`sysadmin`角色。 Azure AD 伺服器主體 （登入） 已公開上市之後，將會移除此暫時性的限制。 如果您嘗試使用 Azure AD 系統管理員帳戶建立登入，您會看到下列錯誤： `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - 目前，master 資料庫中建立的第一個 Azure AD 登入都必須使用標準的 SQL Server 帳戶 (非 Azure AD) 來建立這`sysadmin`使用的角色[CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)從外部提供者。 正式運作之後，將會移除這項限制。 然後您可以建立初始 Azure AD 登入受控執行個體中使用 Active Directory 系統管理員。
    - Azure AD 登入不支援搭配 SQL Server Management Studio 或 SqlPackage 的 DacFx （匯出/匯入）。 Azure AD 伺服器主體 （登入） 已公開上市之後，將會移除這項限制。
    - 使用 SQL Server Management Studio 中的 Azure AD 伺服器主體 （登入）：

      - 不支援指令碼使用任何已驗證的登入的 Azure AD 登入。
      - IntelliSense 無法辨識的建立登入從外部提供者的陳述式，並顯示紅色底線。

- 只有伺服器層級主體登入，它由佈建程序，伺服器角色的成員時，例如 「 受控執行個體`securityadmin`或`sysadmin`，或其他登入具有伺服器層級的 ALTER ANY LOGIN 權限可以建立 Azure AD受控執行個體的 master 資料庫中的伺服器主體 （登入）。
- 如果登入 SQL 主體，只有登入屬於`sysadmin`角色可以使用 create 命令來建立登入 Azure AD 帳戶。
- Azure AD 登入必須是 Azure AD 用於 Azure SQL Database 受控執行個體在相同目錄內的成員。
- Azure AD 伺服器主體 （登入） 會顯示在 [物件總管] 中從 SQL Server Management Studio 18.0 preview 5 開始的。
- 可允許 Azure AD 伺服器主體 (登入) 與 Azure AD 系統管理員帳戶重疊。 Azure AD 伺服器主體 （登入） 會優先於 Azure AD 系統管理員，當您解決主體，並將權限套用至受控執行個體。
- 在驗證期間，下列順序套用至解析驗證的主體：

    1. 如果 Azure AD 帳戶存在，會直接對應至 Azure AD 的伺服器主體 （登入），即存在於 sys.server_principals 中為"E"的類型，授與存取，以及適用於 Azure AD 伺服器主體 （登入） 的權限。
    2. Azure AD 帳戶是否對應至 Azure AD 的伺服器主體 （登入），也就是存在於 sys.server_principals 中，輸入"X"時，Azure AD 群組的成員授與存取權，以及適用於 Azure AD 群組登入的權限。
    3. 如果 Azure AD 帳戶是特殊的入口網站設定 Azure AD 系統管理員的受控執行個體，這不存在於受管理的執行個體的系統檢視表，套用固定的特殊使用權限的 Azure AD 系統管理員管理的執行個體 （傳統模式）。
    4. 以直接對應到 Azure AD 使用者在資料庫中，會存在於 sys.database_principals 中做為類型"E"的形式存在的 Azure AD 帳戶授與存取權，並套用的 Azure AD 資料庫使用者的權限。
    5. Azure AD 帳戶是否會對應到在資料庫中，即存在於 sys.database_principals 中做為類型"X"，在 Azure AD 使用者的 Azure AD 群組的成員授與存取權，以及適用於 Azure AD 群組登入的權限。
    6. 如果沒有對應至 Azure AD 使用者帳戶或 Azure AD 群組帳戶，這會解析成驗證的使用者，Azure AD 登入將會套用來自此 Azure AD 登入的所有權限。

### <a name="service-key-and-service-master-key"></a>服務金鑰和服務主要金鑰

- [主要金鑰備份](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql)不支援 （由 SQL Database 服務管理）。
- [主要金鑰還原](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql)不支援 （由 SQL Database 服務管理）。
- [服務主要金鑰備份](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql)不支援 （由 SQL Database 服務管理）。
- [服務主要金鑰還原](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql)不支援 （由 SQL Database 服務管理）。

## <a name="configuration"></a>組態

### <a name="buffer-pool-extension"></a>緩衝集區延伸

- [緩衝集區延伸](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension)不受支援。
- 不支援 `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`。 請參閱 [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql)。

### <a name="collation"></a>Collation

預設執行個體定序為 `SQL_Latin1_General_CP1_CI_AS`，而且可指定為建立參數。 請參閱[定序](https://docs.microsoft.com/sql/t-sql/statements/collations)。

### <a name="compatibility-levels"></a>相容性層級

- 支援的相容性層級為 100、 110、 120、 130 和 140。
- 不支援低於 100 的相容性層級。
- 新資料庫的預設相容性層級為 140。 針對已還原的資料庫，相容性層級維持不變的如果它是 100 和更新版本。

請參閱 [ALTER DATABASE 相容性層級](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。

### <a name="database-mirroring"></a>資料庫鏡像

不支援資料庫鏡像。

- 不支援 `ALTER DATABASE SET PARTNER` 和 `SET WITNESS` 選項。
- 不支援 `CREATE ENDPOINT … FOR DATABASE_MIRRORING`。

如需詳細資訊，請參閱 [ALTER DATABASE SET PARTNER and SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) 和 [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)。

### <a name="database-options"></a>資料庫選項

- 不支援多個記錄檔。
- 「一般用途」服務層級中不支援記憶體內部物件。 
- 沒有限制為每個一般用途執行個體，這表示每個資料庫的 280 個檔案最多 280 個檔案。 一般用途層中的資料和記錄檔都會計入這項限制中。 [業務關鍵層支援每個資料庫的 32,767 檔案](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。
- 資料庫不能包含 filestream 資料檔案群組。 還原會失敗，如果.bak 包含`FILESTREAM`資料。 
- 每個檔案都位於 Azure Blob 儲存體中。 每個檔案的 IO 和輸送量均取決於每個個別檔案的大小。

#### <a name="create-database-statement"></a>CREATE DATABASE 陳述式

下列限制適用於`CREATE DATABASE`:

- 無法定義檔案和檔案群組。 
- `CONTAINMENT`選項不受支援。 
- `WITH` 不支援選項。 
   > [!TIP]
   > 因應措施，請使用`ALTER DATABASE`之後`CREATE DATABASE`來設定資料庫選項，將檔案新增或設定內含項目。 

- `FOR ATTACH`選項不受支援。
- `AS SNAPSHOT OF`選項不受支援。

如需詳細資訊，請參閱 [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql)。

#### <a name="alter-database-statement"></a>ALTER DATABASE 陳述式

有些檔案屬性無法設定或變更：

- 檔案路徑中不能指定`ALTER DATABASE ADD FILE (FILENAME='path')`T-SQL 陳述式。 從指令碼中移除 `FILENAME`，因為受控執行個體會自動放置這些檔案。 
- 檔案名稱無法變更使用`ALTER DATABASE`陳述式。

以下為預設選項且無法變更：

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

下列選項無法修改：

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

如需詳細資訊，請參閱 [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options)。

### <a name="sql-server-agent"></a>SQL Server 代理程式

- 啟用和停用 SQL Server Agent 目前不支援受管理的執行個體中。 SQL Agent 一直在執行中。
- SQL Server Agent 設定為唯讀的。 此程序`sp_set_agent_properties`受控執行個體中不支援。 
- 工作
  - 支援 T-SQL 作業步驟。
  - 支援下列複寫作業：
    - 交易記錄讀取器
    - 快照
    - 散發者
  - 支援 SSIS 作業步驟。
  - 目前不支援其他類型的作業步驟：
    - 不支援合併式複寫作業步驟。 
    - 不支援佇列讀取器。 
    - 尚不支援命令殼層。
  - 受管理的執行個體無法存取外部資源，透過 robocopy 的網路共用，例如。 
  - SQL Server Analysis Services 不支援。
- 部分支援通知。
- 支援電子郵件通知，不過需要您設定 Database Mail 設定檔。 SQL Server 代理程式可以使用只有一個 Database Mail 設定檔，並必須呼叫`AzureManagedInstance_dbmail_profile`。 
  - 不支援呼叫器。
  - 不支援 NetSend。
  - 目前尚不支援警示。
  - 不支援的 proxy。
- 不支援 EventLog。

目前不支援下列 SQL 代理程式的功能：

- Proxy
- 在閒置的 CPU 上排程工作
- 啟用或停用代理程式
- 警示

如需有關 SQL Server Agent 的資訊，請參閱 [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)。

### <a name="tables"></a>資料表

不支援下列資料表：

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED` 

如需有關如何建立及變更資料表的資訊，請參閱 < [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql)並[ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)。

## <a name="functionalities"></a>功能

### <a name="bulk-insert--openrowset"></a>大量插入 / openrowset

受管理的執行個體無法存取，檔案共用及 Windows 資料夾中，必須從 Azure Blob 儲存體匯入檔案：

- `DATASOURCE` 所需要的是`BULK INSERT`命令，而您從 Azure Blob 儲存體匯入檔案。 請參閱[大量插入](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql)。
- `DATASOURCE` 所需要的是`OPENROWSET`函式，當您從 Azure Blob 儲存體讀取檔案的內容。 請參閱 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)。

### <a name="clr"></a>CLR

受管理的執行個體無法存取檔案共用及 Windows 資料夾，因此適用下列限制：

- 只支援 `CREATE ASSEMBLY FROM BINARY`。 請參閱 [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)。 
- 不支援 `CREATE ASSEMBLY FROM FILE`。 請參閱 [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)。
- `ALTER ASSEMBLY` 無法參考檔案。 請參閱 [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql)。

### <a name="dbcc"></a>DBCC

未記載之已啟用 SQL Server 中的 DBCC 陳述式並不支援受管理的執行個體。

- 不支援 `Trace flags`。 請參閱[追蹤旗標](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)。
- 不支援 `DBCC TRACEOFF`。 請參閱 [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql)。
- 不支援 `DBCC TRACEON`。 請參閱 [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql)。

### <a name="distributed-transactions"></a>分散式交易

MSDTC 和[彈性交易](sql-database-elastic-transactions-overview.md)目前並不支援受管理的執行個體。

### <a name="extended-events"></a>擴充事件

不支援某些 Windows 特定目標的擴充事件 (XEvents):

- `etw_classic_sync`目標不支援。 存放區`.xel`Azure Blob 儲存體中的檔案。 請參閱 [etw_classic_sync 目標](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)。
- `event_file`目標不支援。 存放區`.xel`Azure Blob 儲存體中的檔案。 請參閱 [event_file 目標](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)。

### <a name="external-libraries"></a>外部程式庫

資料庫內 R 和 Python，外部程式庫尚未支援。 請參閱 [SQL Server Machine Learning 服務](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services)。

### <a name="filestream-and-filetable"></a>Filestream 和 FileTable

- 不支援 filestream 資料。
- 資料庫不能包含具有檔案群組`FILESTREAM`資料。
- 不支援 `FILETABLE`。
- 資料表不能有 `FILESTREAM` 類型
- 以下是不支援的函式：
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

如需詳細資訊，請參閱 [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) 和 [Filetable](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server)。

### <a name="full-text-semantic-search"></a>全文檢索的語意搜尋

不支援[語意搜尋](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server)。

### <a name="linked-servers"></a>連結的伺服器

在受控執行個體中，連結伺服器支援的目標數有限：

- 支援的目標是 SQL Server 和 SQL Database。
- 不支援的目標是檔案、 Analysis Services 和其他 RDBMS。

作業

- 不支援跨執行個體寫入交易。
- 支援使用 `sp_dropserver` 卸除連結的伺服器。 請參閱 [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)。
- `OPENROWSET`函式可用來只在 SQL Server 執行個體上執行查詢。 它們可以是受管理，在內部，或在虛擬機器。 請參閱 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)。
- `OPENDATASOURCE`函式可用來只在 SQL Server 執行個體上執行查詢。 它們可以是受管理，在內部，或在虛擬機器。 只有`SQLNCLI`， `SQLNCLI11`，和`SQLOLEDB`做為提供者支援的值。 例如 `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`。 請參閱 [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)。

### <a name="polybase"></a>PolyBase

外部資料表的參考 HDFS 或 Azure Blob 儲存體中的檔案不支援。 PolyBase 的相關資訊，請參閱[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)。

### <a name="replication"></a>複寫

[異動複寫](sql-database-managed-instance-transactional-replication.md)適用於公開預覽版的受控執行個體，具有某些條件約束：
- 所有類型的複寫參與者 （發行者、 散發者、 提取訂閱者和推送訂閱者） 可以都放在受控執行個體，但發行者和散發者不能都放在不同的執行個體。
- 支援交易式、 快照和雙向複寫類型。 不支援合併式複寫、 對等項目-複寫和可更新的訂閱。
- 受控執行個體可以與 SQL Server 的新版本進行通訊。 請參閱支援的版本[此處](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems)。
- 異動複寫有一些[額外的網路需求](sql-database-managed-instance-transactional-replication.md#requirements)。

如需設定複寫的資訊，請參閱[複寫教學課程](replication-with-sql-database-managed-instance.md)。

### <a name="restore-statement"></a>RESTORE 陳述式 

- 支援的語法：
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- 不支援的語法：
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- 來源： 
  - `FROM URL` （azure Blob 儲存體） 是唯一支援的選項。
  - 不支援 `FROM DISK`/`TAPE`/備份裝置。
  - 不支援備份組。
- `WITH` 不支援的選項，例如否`DIFFERENTIAL`或`STATS`。
- `ASYNC RESTORE`:即使用戶端連線中斷，仍會繼續還原。 如果您的連線中斷，您可以檢查`sys.dm_operation_status`檢視狀態是否為還原作業，以及建立和卸除資料庫。 請參閱 [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)。 

下列資料庫選項會設定或覆寫稍後無法變更： 

- `NEW_BROKER` 如果訊息代理程式未在.bak 檔案中啟用。 
- `ENABLE_BROKER` 如果訊息代理程式未在.bak 檔案中啟用。 
- `AUTO_CLOSE=OFF` 如果.bak 檔案中的資料庫具有`AUTO_CLOSE=ON`。 
- `RECOVERY FULL` 如果.bak 檔案中的資料庫具有`SIMPLE`或`BULK_LOGGED`復原模式。
- 加入記憶體最佳化檔案群組，並呼叫 XTP，如果來源.bak 檔案中。 
- 任何現有的記憶體最佳化檔案群組已重新命名為 XTP。 
- `SINGLE_USER` 並`RESTRICTED_USER`選項會轉換成`MULTI_USER`。

限制： 

- `.BAK` 無法還原包含多個備份組的檔案。 
- `.BAK` 無法還原包含多個記錄檔的檔案。
- 還原會失敗，如果.bak 包含`FILESTREAM`資料。
- 包含有使用中記憶體中物件的資料庫的備份無法還原的一般用途執行個體上。 Restore 陳述式的相關資訊，請參閱[RESTORE 陳述式](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)。

### <a name="service-broker"></a>Service broker

不支援跨執行個體的服務訊息代理程式：

- `sys.routes`:先決條件是，您必須從 sys.routes 選取位址。 位址必須位於本機上每個路由。 請參閱 [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql)。
- `CREATE ROUTE`:您無法使用`CREATE ROUTE`具有`ADDRESS`以外的其他`LOCAL`。 請參閱 [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql)。
- `ALTER ROUTE`:您無法使用`ALTER ROUTE`具有`ADDRESS`以外的其他`LOCAL`。 請參閱 [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql)。 

### <a name="stored-procedures-functions-and-triggers"></a>預存程序、 函數和觸發程序

- `NATIVE_COMPILATION` 不支援在一般目的層。
- 不支援下列 [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 選項： 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- 不支援 `sp_execute_external_scripts`。 請參閱 [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)。
- 不支援 `xp_cmdshell`。 請參閱 [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)。
- `Extended stored procedures` 不支援，其中包含`sp_addextendedproc` 和`sp_dropextendedproc`。 請參閱[擴充預存程序](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)。
- 不支援 `sp_attach_db`、`sp_attach_single_file_db` 和 `sp_detach_db`。 請參閱 [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql)、[sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 和 [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)。

## <a name="Environment"></a>環境的條件約束

### <a name="subnet"></a>Subnet
- 在 保留供您受控執行個體的子網路中，您不能放置任何其他資源 （例如虛擬機器）。 將這些資源放在其他子網路。
- 子網路必須有足夠數目的可用[IP 位址](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。 最小值會是 16，而建議是將在至少 32 個 IP 位址子網路中。
- [服務端點不能與受管理的執行個體子網路相關聯](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。 請確定當您建立虛擬網路服務端點選項已停用。
- 虛擬核心數目和類型的執行個體，您可以部署在區域中有一些[條件約束和限制](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)。
- 有一些[子網路必須套用的安全性規則](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。

### <a name="vnet"></a>VNET
- 您可以使用資源模型部署 VNet-不支援 vnet 的傳統模型。
- 某些服務，例如 App Service 環境、 Logic apps 和 （使用異地複寫，異動複寫，或透過連結的伺服器） 的受控執行個體無法存取在不同區域中的 受控執行個體，如果其會使用連線的 Vnet [全域對等互連](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)。 您可以連接到這些資源，透過 ExpressRoute 或 VNet 對 VNet 透過 VNet 閘道。

## <a name="Changes"></a> 行為變更

下列變數、函式和檢視會傳回不同的結果：

- `SERVERPROPERTY('EngineEdition')` 傳回值為 8。 此屬性只會識別出受控執行個體。 請參閱 [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)。
- `SERVERPROPERTY('InstanceName')` 因為執行個體作為它的概念存在，SQL Server 不會套用至受控執行個體，則傳回 NULL。 請參閱 [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)。
- `@@SERVERNAME` 傳回完整的 DNS 「 可連線 」 名稱，例如，my-managed-instance.wcus17662feb9ce98.database.windows.net。 請參閱 [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql)。 
- `SYS.SERVERS` 傳回完整的 DNS 「 可連線 」 名稱，例如`myinstance.domain.database.windows.net`屬性"name"和"data_source。 」 請參閱 [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)。
- `@@SERVICENAME` 因為服務，因為它的概念存在，SQL Server 不會套用至受控執行個體，則傳回 NULL。 請參閱 [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql)。
- 支援 `SUSER_ID`。 如果 Azure AD 登入不是 sys.syslogins，則會傳回 NULL。 請參閱 [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql)。 
- 不支援 `SUSER_SID`。 會傳回錯誤的資料，也就是暫時的已知問題。 請參閱 [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql)。 

## <a name="Issues"></a>已知問題與限制

### <a name="tempdb-size"></a>TEMPDB 大小

檔案大小上限的`tempdb`不能大於 24 GB，每個一般用途層上的核心。 最大值`tempdb`業務關鍵層上的大小會限制執行個體儲存體大小。 `tempdb` 記錄檔大小會限制為 120 GB，同時在一般用途和業務關鍵層上。 `tempdb`資料庫一律會分割成 12 個資料檔案。 無法變更每個檔案最大，而且無法加入新檔案`tempdb`。 有些查詢可能會傳回錯誤，如果他們需要 24 GB 以上每個核心在`tempdb`則會產生 120 GB 以上的記錄檔。 `tempdb` 當空的資料庫執行個體啟動時或容錯移轉和任何變更時對進行中，會永遠重新建立`tempdb`將不予保留。 

### <a name="cant-restore-contained-database"></a>無法還原自主的資料庫

受控執行個體無法還原[自主資料庫](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)。 時間點還原現有的自主資料庫無法運作的受控執行個體。 很快就會解決此問題。 在此同時，我們建議您放在受控執行個體的資料庫移除內含項目選項。 請勿使用生產資料庫的內含項目選項。 

### <a name="exceeding-storage-space-with-small-database-files"></a>小型資料庫檔案造成儲存空間超出限制

`CREATE DATABASE``ALTER DATABASE ADD FILE`，和`RESTORE DATABASE`陳述式可能會失敗，因為執行個體可以連線到 Azure 儲存體限制。

每個一般目的受控執行個體具有最多 35 TB 的儲存體保留給 Azure 進階磁碟空間。 每個資料庫檔案會放在不同的實體磁碟。 磁碟大小可以是 128 GB、256 GB、512 GB、1 TB 或 4 TB。 未使用的磁碟空間不收費，但 Azure 進階磁碟大小總計的總和不得超過 35 TB。 在某些情況下，總計不需 8 TB 的 managed 執行個體可能會超過的 35 TB 的 Azure 限制因內部分散的儲存體大小。

比方說，一般目的受控執行個體可能會有 4 TB 磁碟上放置的大小為 1.2 TB 的一個檔案。 它也可能會有 248 是每 1 GB 大小的檔案會放置於不同的 128 GB 磁碟。 在此範例中：

- 配置的磁碟儲存體大小總計為 1 x 4 TB + 248 x 128 GB = 35 TB。
- 為執行個體上的資料庫保留的大小總計為 1 x 1.2 TB + 248 x 1 GB = 1.4 TB。

此範例說明在某些情況下，因為特定的分佈的檔案，而受管理的執行個體可能會達到您意料不到它時，會將附加的 Azure 進階磁碟的保留 35 TB 限制。

在此範例中，現有的資料庫會繼續運作，只要不加入新檔案，而不需要任何問題會成長。 無法建立新的資料庫，或還原，因為沒有足夠的空間供新磁碟機，即使所有資料庫的大小總計不會達到執行個體大小限制。 在此情況下會傳回錯誤不清楚。

您可以[找出其餘的檔案數目](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1)使用系統檢視表。 如果達到此限制，請試著[空白，然後使用 DBCC SHRINKFILE 陳述式就可以將它刪除一些較小的檔案](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file)，或切換到[業務關鍵層中，沒有這項限制](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。

### <a name="incorrect-configuration-of-the-sas-key-during-database-restore"></a>設定不正確的 SAS 金鑰在資料庫還原

`RESTORE DATABASE` 讀取.bak 檔案可能會不斷重試讀取.bak 檔案，並傳回錯誤很長一段時間之後，如果中的共用的存取簽章`CREDENTIAL`不正確。 將還原的資料庫，以確定 SAS 金鑰正確無誤，請執行 RESTORE HEADERONLY。
請確定您移除前置`?`從使用 Azure 入口網站所產生的 SAS 金鑰。

### <a name="tooling"></a>工具

SQL Server Management Studio 和 SQL Server Data Tools，而他們存取的受管理的執行個體可能會有一些問題。

- 使用 Azure AD 伺服器主體 （登入） 和使用者 （公開預覽） 搭配 SQL Server Data Tools 目前不支援。
- SQL Server Management Studio 中不支援 Azure AD 伺服器主體 （登入） 和使用者 （公開預覽） 的指令碼。

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>某些檢視、記錄與訊息中的不正確資料庫名稱

數個系統檢視、效能計數器、錯誤訊息、XEvents 與錯誤記錄檔項目都會顯示 GUID 資料庫識別碼，而非實際資料庫名稱。 不要依賴這些 GUID 識別項，因為它們在未來取代實際的資料庫名稱。

### <a name="database-mail"></a>Database Mail

`@query`中的參數[sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql)程序無法運作。

### <a name="database-mail-profile"></a>Database Mail 設定檔

SQL Server Agent 所使用的 Database Mail 設定檔，必須先呼叫`AzureManagedInstance_dbmail_profile`。 沒有任何限制的其他 Database Mail 設定檔名稱。

### <a name="error-logs-arent-persisted"></a>錯誤記錄檔不會保存

受控執行個體中所提供的錯誤記錄檔不會保存，和其大小不會包含於最大儲存體限制。 如果發生容錯移轉，就可能會自動清除錯誤記錄檔。

### <a name="error-logs-are-verbose"></a>錯誤記錄是詳細資訊記錄

受管理的執行個體將詳細的資訊放在錯誤記錄檔，和大部分的動作不相關。 錯誤記錄檔中的資訊數量會降低未來。

**因應措施：** 您可以使用自訂程序來讀取錯誤記錄檔，篩選出某些不相關的項目。 如需詳細資訊，請參閱 <<c0> [ 受管理的執行個體 – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/)。

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>不支援在相同的執行個體的兩個資料庫上交易範圍

`TransactionScope`類別在.NET 中的無法運作，如果兩個查詢傳送到相同的執行個體的相同交易範圍內的兩個資料庫：

```C#
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

雖然這段程式碼會使用相同的執行個體內的資料，您會需要 MSDTC。

**因應措施：** 使用  [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase)到另一個資料庫的內容使用的連接而不是使用兩個連線。

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR 模組和連結的伺服器有時無法參考本機的 IP 位址

CLR 模組放在受管理的執行個體和連結的伺服器或分散式的查詢，有時候參考目前的執行個體無法解析的本機執行個體的 IP。 此錯誤為暫時性問題。

**因應措施：** 盡可能在 CLR 模組中使用內容連接。

### <a name="tde-encrypted-databases-with-a-service-managed-key-dont-support-user-initiated-backups"></a>使用服務管理金鑰的 TDE 加密資料庫不支援使用者起始的備份

您無法執行`BACKUP DATABASE ... WITH COPY_ONLY`資料庫加密的受控服務的透明資料加密 (TDE)。 服務管理的 TDE 會強制加密與內部 TDE 金鑰備份。 無法匯出金鑰，所以您無法還原備份。

**因應措施：** 使用自動備份 」 和 「 時間點還原，或使用[客戶管理 (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key)改。 您也可以停用資料庫加密。

### <a name="point-in-time-restore-follows-time-by-the-time-zone-set-on-the-source-instance"></a>還原時間點會依循來源執行個體上設定的時區時間

目前時間點還原會解譯要還原至下列來源執行個體的時區而是由下列的 UTC 時間。
請檢查[已知問題的受控執行個體時區](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-timezone#known-issues)如需詳細資訊。

## <a name="next-steps"></a>後續步驟

- 如需有關受管理的執行個體的詳細資訊，請參閱[受管理的執行個體是什麼？](sql-database-managed-instance.md)
- 如需功能與比較清單，請參閱[Azure SQL Database 功能比較](sql-database-features.md)。
- 如需示範如何建立新的受控執行個體的快速入門，請參閱[建立受控執行個體](sql-database-managed-instance-get-started.md)。
