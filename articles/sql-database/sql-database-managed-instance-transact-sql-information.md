---
title: Azure SQL Database 受控執行個體的 T-SQL 差異 | Microsoft Docs
description: 本文將討論 Azure SQL Database 中的受控執行個體與 SQL Server 之間的 T-SQL 差異
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, bonova
manager: craigg
ms.date: 03/13/2019
ms.openlocfilehash: b044a7c2b3122fcbce44ae2e45198f57f6a87260
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541276"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Azure SQL Database 受控執行個體的 T-SQL 差異

受控執行個體的部署選項提供對內部部署 SQL Server 資料庫引擎的高度相容性。 受控執行個體支援大部分的 SQL Server 資料庫引擎功能。

![移轉](./media/sql-database-managed-instance/migration.png)

由於仍有一些語法和行為上的差異，因此本文將摘要說明這些差異。 <a name="Differences"></a>

- [可用性](#availability)上的差異包括 [Always-On](#always-on-availability) 和[備份](#backup)，
- [安全性](#security)上的差異包括[稽核](#auditing)、[憑證](#certificates)、[認證](#credential)、[密碼編譯提供者](#cryptographic-providers)、[登入 / 使用者](#logins--users)、[服務金鑰和服務主要金鑰](#service-key-and-service-master-key)，
- [設定](#configuration)上的差異包括[緩衝集區延伸](#buffer-pool-extension)、[定序](#collation)、[相容性層級](#compatibility-levels)、[資料庫鏡像](#database-mirroring)、[資料庫選項](#database-options)、[SQL Server Agent](#sql-server-agent)、[資料表選項](#tables)，
- [功能](#functionalities)包括 [/OPENROWSET](#bulk-insert--openrowset)、[CLR](#clr)、[DBCC](#dbcc)、[分散式交易](#distributed-transactions)、[擴充事件](#extended-events)、[外部程式庫](#external-libraries)、[Filestream 和 Filetable](#filestream-and-filetable)、[全文檢索語意搜尋](#full-text-semantic-search)、[連結伺服器](#linked-servers)、[Polybase](#polybase)、[複寫](#replication)、[RESTORE](#restore-statement)、[Service Broker](#service-broker)、[預存程序、函式和觸發程序](#stored-procedures-functions-triggers)，
- [在受控執行個體中會有不同行為的功能](#Changes)
- [暫時性限制與已知問題](#Issues)

## <a name="availability"></a>可用性

### <a name="always-on-availability"></a>Always-On

受控執行個體內建[高可用性](sql-database-high-availability.md)，並且無法由使用者控制。 不支援下列陳述式︰

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql)陳述式的 [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) 子句

### <a name="backup"></a>Backup 

受控執行個體有自動備份，且可讓使用者建立完整資料庫的 `COPY_ONLY` 備份。 不支援差異、記錄及檔案快照集備份。

- 使用受控執行個體，您可以將執行個體資料庫只備份到 Azure Blob 儲存體帳戶：
  - 只支援 `BACKUP TO URL`
  - 不支援 `FILE`、`TAPE`及備份裝置  
- 支援大部分一般的 `WITH` 選項
  - `COPY_ONLY` 是必要的
  - 不支援 `FILE_SNAPSHOT`
  - 磁帶選項：不支援 `REWIND`、`NOREWIND`、`UNLOAD` 和 `NOUNLOAD`
  - 記錄專用選項：不支援 `NORECOVERY`、`STANDBY` 和 `NO_TRUNCATE`

限制：  

- 使用受控執行個體，您可以將執行個體資料庫備份至最多具有 32 個等量磁碟區的備份，這足夠最多 4 TB 的資料庫使用 (如果使用備份壓縮)。
- 使用的最大備份等量磁碟區大小`BACKUP`受管理的執行個體中的命令為 195 GB （最大 blob 大小）。 在備份命令中增加條帶 (Stripe) 數目，可減少個別條帶 (Stripe) 的大小並維持在此限制內。

    > [!TIP]
    > 若要解決這項限制，從 SQL Server 中的內部部署環境或虛擬機器中備份資料庫時，您可以執行下列作業：
    >
    > - 備份至`DISK`而不要備份至 `URL`
    > - 備份的檔案上傳至 Blob 儲存體
    > - 還原至受控執行個體
    >
    > `Restore`受管理的執行個體中的命令會支援備份檔案中較大 blob 大小因為不同的 blob 類型用來上傳備份檔案的儲存體。

如需使用 T-SQL 進行備份的相關資訊，請參閱[備份](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql)。

## <a name="security"></a>安全性

### <a name="auditing"></a>稽核

Azure SQL Database 中的資料庫和 SQL Server 中的資料庫兩者之間的主要稽核差異在於：

- 使用 Azure SQL Database 中的受控執行個體部署選項，稽核作業會在伺服器層級運作，並將 `.xel` 記錄檔儲存於 Azure Blob 儲存體。
- 在使用 Azure SQL Database 中的單一資料庫和彈性集區部署選項時，稽核作業則會在資料庫層級運作。
- 在 SQL Server 內部部署 / 虛擬機器中，稽核會在伺服器層級運作，但會將事件儲存在檔案系統/windows 事件記錄。
  
受控執行個體中的 XEvent 稽核支援 Azure Blob 儲存體目標。 不支援檔案與 windows 記錄。

向 Azure Blob 儲存體進行稽核的 `CREATE AUDIT` 語法有一個主要差異：

- 已提供新的 `TO URL` 語法，可讓您指定放置 `.xel` 檔案的 Azure Blob 儲存體容器 URL
- `TO FILE` 語法不受支援，因為受控執行個體無法存取 Windows 檔案共用。

如需詳細資訊，請參閱  

- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [稽核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>憑證

受控執行個體無法存取檔案共用及 Windows 資料夾，因此會有下列限制：

- 憑證不支援 `CREATE FROM`/`BACKUP TO` 檔案
- 不支援 `FILE`/`ASSEMBLY` 中的 `CREATE`/`BACKUP` 憑證。 無法使用私密金鑰檔案。  

請參閱 [CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) 和 [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql)。  
  
**因應措施**：指令碼憑證/私密金鑰，儲存為 .sql 檔案，並從二進位檔建立：

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>認證

僅支援Azure Key Vault 和 `SHARED ACCESS SIGNATURE` 身分識別。 不支援 Windows 使用者。

請參閱 [CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) 和 [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql)。

### <a name="cryptographic-providers"></a>密碼編譯提供者

受控執行個體無法存取檔案，所以無法建立密碼編譯提供者：

- 不支援 `CREATE CRYPTOGRAPHIC PROVIDER`。 請參閱 [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql)。
- 不支援 `ALTER CRYPTOGRAPHIC PROVIDER`。 請參閱 [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)。

### <a name="logins--users"></a>登入 / 使用者

- 不支援 `FROM CERTIFICATE`、`FROM ASYMMETRIC KEY` 和 `FROM SID` 建立的 SQL 登入。 請參閱 [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql)。
- 支援使用 [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) 語法或 [CREATE USER FROM LOGIN [Azure AD Login]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) 語法建立的 Azure Active Directory (Azure AD) 伺服器主體 (登入) (**公開預覽**)。 這些是建立在伺服器層級的登入。

    受控執行個體支援使用 `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` 語法的 Azure AD 資料庫主體。 這也稱為 Azure AD 自主資料庫使用者。

- 不支援使用 `CREATE LOGIN ... FROM WINDOWS` 語法建立的 Windows 登入。 使用 Azure Active Directory 登入和使用者。
- 建立執行個體的 Azure AD 使用者具有[不受限制的系統管理員權限](sql-database-manage-logins.md#unrestricted-administrative-accounts)。
- 您可以使用 `CREATE USER ... FROM EXTERNAL PROVIDER` 語法建立非系統管理員的 Azure Active Directory (Azure AD) 資料庫層級使用者。 請參閱 [CREATE USER ...FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users)。
- Azure AD 伺服器主體 (登入) 僅支援一個 MI 執行個體中的 SQL 功能。 Azure AD 使用者無法使用需跨越執行個體進行互動的功能，無論在相同 Azure AD 租用戶或不同租用戶中皆是如此。 這類功能的範例如下：

  - SQL 異動複寫和
  - 連結伺服器

- 不支援將對應至 Azure AD 群組的 Azure AD 登入設定為資料庫擁有者。
- 支援使用其他 Azure AD 主體模擬 Azure AD 伺服器層級的主體，例如 [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) 子句。 EXECUTE AS 限制：

  - 名稱與登入名稱不同時，不支援對 Azure AD 使用者使用 EXECUTE AS USER。 例如，如果透過 CREATE USER [myAadUser] FROM LOGIN [john@contoso.com] 語法建立使用者，並嘗試透過 EXEC AS USER = _myAadUser_ 進行模擬。 從 Azure AD 伺服器主體 (登入) 建立 **USER** 時，請指定與 **LOGIN** 中的 login_name 相同的 user_name。
  - 只有屬於 `sysadmin` 角色一部分的 SQL 伺服器層級主體 (登入) 可以執行下列鎖定 Azure AD 主體的作業：

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- **公開預覽** Azure AD 伺服器主體 (登入) 的限制：

  - 受控執行個體的 Active Directory 系統管理員限制：

    - 用來設定受控執行個體的 Azure AD 系統管理員不可用來在受控執行個體內建立 Azure AD 伺服器主體 (登入)。 您必須使用屬於 `sysadmin` 的 SQL Server 帳戶來建立第一個 Azure AD 伺服器主體 (登入)。 此一限制是暫時性的，在 Azure AD 伺服器主體 (登入) 正式運作後便會消除。 如果您嘗試使用 Azure AD 系統管理員帳戶來建立登入，則會看到下列錯誤：`Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - 目前，在 Master DB 中建立的第一個 Azure AD 登入必須使用標準 SQL Server 帳戶 (非 Azure AD) 來建立，也就是使用 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) FROM EXTERNAL PROVIDER 的 `sysadmin`。 正式運作後，這項限制將會移除，而初始 Azure AD 登入就能夠由受控執行個體的 Active Directory 系統管理員來建立。
    - 與 SQL Server Management Studio (SSMS) 或 SqlPackage 搭配使用的 DacFx (匯出/匯入) 不可用於 Azure AD 登入。 此一限制會在 Azure AD 伺服器主體 (登入) 正式運作後移除。
    - 搭配 SSMS 使用 Azure AD 伺服器層級主體 (登入)

      - 不支援以指令碼執行 Azure AD 登入 (使用任何已驗證的登入)。
      - IntelliSense 無法辨識 **CREATE LOGIN FROM EXTERNAL PROVIDER**陳述式，而且會顯示紅色底線。

- 只有伺服器層級主體登入 (由受控執行個體佈建程序所建立)、伺服器角色的成員 (`securityadmin` 或 `sysadmin`)，或在伺服器層級上有 ALTER ANY LOGIN 權限的其他登入，可以在 Master 資料庫中建立受控執行個體的 Azure AD 伺服器主體 (登入)。
- 如果登入是 SQL 主體，只有屬於 `sysadmin` 角色一部分的登入可以使用 create 命令來為 Azure AD 帳戶建立登入。
- Azure AD 登入必須是 Azure SQL 受控執行個體所用目錄內的 Azure AD 成員。
- 從 SSMS 18.0 preview 5 開始，Azure AD 伺服器主體 (登入) 會顯示在物件總管中。
- 可允許 Azure AD 伺服器主體 (登入) 與 Azure AD 系統管理員帳戶重疊。 解析主體和將權限套用至受控執行個體時，Azure AD 伺服器主體 (登入) 會優先於 Azure AD 系統管理員。
- 在驗證期間，解析驗證主體時會套用下列順序：

    1. 如果 Azure AD 帳戶以直接對應至 Azure AD 伺服器主體 (登入) 的形式存在 (在 sys.server_principals 中顯示為類型 ‘E’)，則授與存取權，並套用 Azure AD 伺服器主體 (登入) 的權限。
    2. 如果 Azure AD 帳戶是對應至 Azure AD 伺服器主體 (登入) 的 Azure AD 群組成員 (在 sys.server_principals 中顯示為類型 ‘X’)，則授與存取權，並套用 Azure AD 群組登入的權限。
    3. 如果 Azure AD 帳戶是由入口網站所設定且適用於受控執行個體的特殊 Azure AD 系統管理員 (不存在於受控執行個體的系統檢視)，則套用固定且適用於受控執行個體的 Azure AD 系統管理員權限 (傳統模式)。
    4. 如果 Azure AD 帳戶以直接對應至資料庫中 Azure AD 使用者的形式存在 (在 sys.database_principals 中為類型 ‘E’)，則授與存取權，並套用 Azure AD 資料庫使用者的權限。
    5. 如果 Azure AD 帳戶是對應至資料庫中 Azure AD 使用者的 Azure AD 群組成員 (在 sys.database_principals 中為類型 ‘X’)，則授與存取權，並套用 Azure AD 群組登入的權限。
    6. 如果有對應至 Azure AD 使用者帳戶或 Azure AD 群組帳戶的 Azure AD 登入要針對使用者驗證進行解析，則會套用此 Azure AD 登入的所有權限。

### <a name="service-key-and-service-master-key"></a>服務金鑰和服務主要金鑰

- 不支援[主要金鑰備份](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) (由 SQL Database 服務管理)
- 不支援[主要金鑰還原](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) (由 SQL Database 服務管理)
- 不支援[服務主要金鑰備份](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) (由 SQL Database 服務管理)
- 不支援[服務主要金鑰還原](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) (由 SQL Database 服務管理)

## <a name="configuration"></a>組態

### <a name="buffer-pool-extension"></a>緩衝集區延伸

- [不支援緩衝集區延伸](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension)。
- 不支援 `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`。 請參閱 [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql)。

### <a name="collation"></a>Collation

預設執行個體定序為 `SQL_Latin1_General_CP1_CI_AS`，而且可指定為建立參數。 請參閱[定序](https://docs.microsoft.com/sql/t-sql/statements/collations)。

### <a name="compatibility-levels"></a>相容性層級

- 支援的相容性層級：100、110、120、130、140  
- 不支援低於 100 的相容性層級。
- 新資料庫的預設相容性層級為 140。 針對已還原的資料庫，如果相容性層級為 100 或更高，則維持不變。

請參閱 [ALTER DATABASE 相容性層級](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。

### <a name="database-mirroring"></a>資料庫鏡像

不支援資料庫鏡像。

- 不支援 `ALTER DATABASE SET PARTNER` 和 `SET WITNESS` 選項。
- 不支援 `CREATE ENDPOINT … FOR DATABASE_MIRRORING`。

如需詳細資訊，請參閱 [ALTER DATABASE SET PARTNER and SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) 和 [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)。

### <a name="database-options"></a>資料庫選項

- 不支援多個記錄檔。
- 「一般用途」服務層中不支援記憶體內部物件。  
- 沒有 280 個檔案，每個一般用途執行個體最多 280 個檔案，每個資料庫的限制。 資料和記錄檔案一般用途層都會計入這項限制。 [業務關鍵層支援每個資料庫的 32,767 檔案](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。
- 資料庫不能包含具有 Filestream 資料的檔案群組。  如果 .bak 包含 `FILESTREAM` 資料，還原將會失敗。  
- 每個檔案都位於 Azure Blob 儲存體中。 每個檔案的 IO 和輸送量均取決於每個個別檔案的大小。  

#### <a name="create-database-statement"></a>CREATE DATABASE 陳述式

以下是 `CREATE DATABASE` 的限制：

- 無法定義檔案和檔案群組。  
- 不支援 `CONTAINMENT` 選項。  
- 不支援 `WITH` 選項。  
   > [!TIP]
   > 因應措施是在 `CREATE DATABASE` 之後使用 `ALTER DATABASE` 來設定資料庫選項以新增檔案，或設定內含項目。  

- 不支援 `FOR ATTACH` 選項
- 不支援 `AS SNAPSHOT OF` 選項

如需詳細資訊，請參閱 [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql)。

#### <a name="alter-database-statement"></a>ALTER DATABASE 陳述式

有些檔案屬性無法設定或變更：

- `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL 陳述式中無法指定檔案路徑。 從指令碼中移除 `FILENAME`，因為受控執行個體會自動放置這些檔案。  
- 無法使用 `ALTER DATABASE` 陳述式變更檔案名稱。

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

- SQL 代理程式設定是唯讀狀態。 受控執行個體中不支援 `sp_set_agent_properties` 程序。  
- 工作
  - 支援 T-SQL 作業步驟。
  - 支援下列複寫作業：
    - 交易記錄讀取器
    - 快照
    - 散發者
  - 支援 SSIS 作業步驟
  - 目前不支援其他類型的作業步驟，包括：
    - 不支援合併複寫作業步驟。  
    - 不支援佇列讀取器。  
    - 尚不支援命令殼層。
  - 受控執行個體無法存取外部資源 (例如，透過 robocopy 的網路共用)。  
  - 尚不支援 PowerShell。
  - 不支援 Analysis Services。
- 部分支援通知。
- 支援電子郵件通知，但必須設定「資料庫郵件」設定檔。 可能只有一個資料庫郵件設定檔，而且在公開預覽中一定名為 `AzureManagedInstance_dbmail_profile` (暫時性限制)。  
  - 不支援呼叫器。  
  - 不支援 NetSend。
  - 尚不支援警示。
  - 不支援 Proxy。  
- 不支援 Eventlog。

目前不支援下列功能，但未來將會啟用：

- Proxy
- 在閒置的 CPU 上排程工作
- 啟用/停用代理程式
- 警示

如需有關 SQL Server Agent 的資訊，請參閱 [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)。

### <a name="tables"></a>資料表

不支援下列項目：

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED`  

如需有關建立和更改資料表的資訊，請參閱 [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) 和 [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)。

## <a name="functionalities"></a>功能

### <a name="bulk-insert--openrowset"></a>大量插入 / openrowset

受控執行個體無法存取檔案共用及 Windows 資料夾，因此必須從 Azure Blob 儲存體匯入這些檔案：

- 從 Azure Blob 儲存體匯入檔案時，`BULK INSERT` 命令中需要 `DATASOURCE`。 請參閱[大量插入](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql)。
- 當您從 Azure Blob 儲存體讀取檔案的內容時，`OPENROWSET` 函數需要 `DATASOURCE`。 請參閱 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)。

### <a name="clr"></a>CLR

受控執行個體無法存取檔案共用及 Windows 資料夾，因此會有下列限制：

- 只支援 `CREATE ASSEMBLY FROM BINARY`。 請參閱 [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)。  
- 不支援 `CREATE ASSEMBLY FROM FILE`。 請參閱 [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)。
- `ALTER ASSEMBLY` 無法參考檔案。 請參閱 [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql)。

### <a name="dbcc"></a>DBCC

受控執行個體中不支援已在 SQL Server 中啟用但無文件說明的 DBCC 陳述式。

- 不支援 `Trace Flags`。 請參閱[追蹤旗標](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)。
- 不支援 `DBCC TRACEOFF`。 請參閱 [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql)。
- 不支援 `DBCC TRACEON`。 請參閱 [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql)。

### <a name="distributed-transactions"></a>分散式交易

受控執行個體中目前不支援 MSDTC 和[彈性交易](sql-database-elastic-transactions-overview.md)。

### <a name="extended-events"></a>擴充事件

不支援 XEvent 的某些 Windows 特定目標：

- 不支援 `etw_classic_sync target`。 將 `.xel` 檔案儲存在 Azure Blob 儲存體中。 請參閱 [etw_classic_sync 目標](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)。
- 不支援 `event_file target`。 將 `.xel` 檔案儲存在 Azure Blob 儲存體中。 請參閱 [event_file 目標](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)。

### <a name="external-libraries"></a>外部程式庫

尚不支援資料庫內部 R 與 Python 的外部程式庫。 請參閱 [SQL Server Machine Learning 服務](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services)。

### <a name="filestream-and-filetable"></a>Filestream 和 Filetable

- 不支援 Filestream 資料。
- 資料庫不能包含具有 `FILESTREAM` 資料的檔案群組。
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

- 支援的目標：SQL Server 和 SQL Database
- 不支援目標：檔案、Analysis Services 和其他 RDBMS。

作業

- 不支援跨執行個體寫入交易。
- 支援使用 `sp_dropserver` 卸除連結的伺服器。 請參閱 [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)。
- `OPENROWSET` 函式只可用來在 SQL Server 執行個體 (受控、內部部署或在虛擬機器中) 上執行查詢。 請參閱 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)。
- `OPENDATASOURCE` 函式只可用來在 SQL Server 執行個體 (受控、內部部署或在虛擬機器中) 上執行查詢。 只支援使用 `SQLNCLI`、`SQLNCLI11` 和 `SQLOLEDB` 值作為提供者。 例如： `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee` 。 請參閱 [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)。

### <a name="polybase"></a>Polybase

不支援參考 HDFS 或 Azure Blob 儲存體內部檔案的外部資料表。 如需有關 Polybase 的資訊，請參閱 [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)。

### <a name="replication"></a>複寫

複寫可用於受控執行個體的公開預覽版。 如需有關複寫的資訊，請參閱 [SQL Server 複寫](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance)。

### <a name="restore-statement"></a>RESTORE 陳述式

- 支援的語法
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- 不支援的語法
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- 來源  
  - `FROM URL` (Azure Blob 儲存體) 是唯一支援的選項。
  - 不支援 `FROM DISK`/`TAPE`/備份裝置。
  - 不支援備份組。
- 不支援 `WITH` 選項 (沒有 `DIFFERENTIAL` 和 `STATS` 等等)。
- `ASYNC RESTORE` - 即使用戶端連線中斷，還原作業仍會繼續。 如果您的連線中斷，您可以檢查 `sys.dm_operation_status` 檢視，了解還原作業的狀態 (也適用於 CREATE (建立) 和 DROP (捨棄) 資料庫作業)。 請參閱 [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)。  

系統會設定/覆寫下列資料庫選項，而且之後無法變更：  

- `NEW_BROKER` (如果 .bak 檔案中未啟用訊息代理程式)  
- `ENABLE_BROKER` (如果 .bak 檔案中未啟用訊息代理程式)  
- `AUTO_CLOSE=OFF` (如果.bak 檔案中的資料庫具有 `AUTO_CLOSE=ON`)  
- `RECOVERY FULL` (如果.bak 檔案中的資料庫具有 `SIMPLE` 或 `BULK_LOGGED` 鎖定模式)
- 新增記憶體最佳化檔案群組並呼叫 XTP (如果來源 .bak 檔案中沒有的話)  
- 任何現有的記憶體最佳化檔案群組已重新命名為 XTP  
- `SINGLE_USER` 和 `RESTRICTED_USER` 選項會轉換成 `MULTI_USER`

限制：  

- 無法還原包含多個備份組的 `.BAK` 檔案。
- 無法還原包含多個記錄檔的 `.BAK` 檔案。
- 如果 .bak 包含 `FILESTREAM` 資料，還原將會失敗。
- 若備份中包含的資料庫具有作用中的記憶體內部物件，則目前無法還原該備份。  
- 若備份中包含的資料庫在某時間點上有記憶體內部物件，則目前無法還原該備份。
- 若備份中包含的資料庫處於唯讀模式中，則目前無法還原該備份。 這項限制很快將會移除。

如需有關 Restore 陳述式的資訊，請參閱 [陳述式](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)。

### <a name="service-broker"></a>Service broker

不支援跨執行個體的服務訊息代理程式：

- `sys.routes`必要條件：從 sys.routes 選取位址。 每個路由上的位址必須是 LOCAL。 請參閱 [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql)。
- `CREATE ROUTE` - 您只能使用 `LOCAL` 的 `ADDRESS` 來執行 `CREATE ROUTE`。 請參閱 [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql)。
- `ALTER ROUTE` - 您只能使用 `LOCAL` 的 `ADDRESS` 來執行 `ALTER ROUTE`。 請參閱 [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql)。  

### <a name="stored-procedures-functions-triggers"></a>預存程序、函式、觸發程序

- 常规用途层中不支持 `NATIVE_COMPILATION`。
- 不支援下列 [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 選項：
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- 不支援 `sp_execute_external_scripts`。 請參閱 [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)。
- 不支援 `xp_cmdshell`。 請參閱 [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)。
- 不支援 `Extended stored procedures`，包括 `sp_addextendedproc`  和 `sp_dropextendedproc`。 請參閱[擴充預存程序](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)
- 不支援 `sp_attach_db`、`sp_attach_single_file_db` 和 `sp_detach_db`。 請參閱 [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql)、[sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 和 [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)。

## <a name="Changes"></a> 行為變更

下列變數、函式和檢視會傳回不同的結果：

- `SERVERPROPERTY('EngineEdition')` 傳回值「8」。 此屬性只會識別出受控執行個體。 請參閱 [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)。
- `SERVERPROPERTY('InstanceName')` 會傳回 NULL，因為執行個體基於 SQL Server 而存在的概念不適用於受控執行個體。 請參閱 [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)。
- `@@SERVERNAME` 會傳回完整的 DNS「可連線」名稱，例如 my-managed-instance.wcus17662feb9ce98.database.windows.net。 請參閱 [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql)。  
- `SYS.SERVERS` - 傳回完整的 DNS「可連線」名稱，例如 'name' 和 'data_source' 屬性的 `myinstance.domain.database.windows.net`。 請參閱 [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)。
- `@@SERVICENAME` 會傳回 NULL，因為服務基於 SQL Server 而存在的概念不適用於受控執行個體。 請參閱 [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql)。
- 支援 `SUSER_ID`。 如果 Azure AD 登入不在 sys.syslogins 中，則傳回 NULL。 請參閱 [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql)。  
- 不支援 `SUSER_SID`。 傳回錯誤資料 (暫時的已知問題)。 請參閱 [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql)。
- `GETDATE()` 和其他內建的日期/時間函式一律會傳回時間 (UTC 時區)。 請參閱 [GETDATE](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql)。

## <a name="Issues"></a>已知問題與限制

### <a name="tempdb-size"></a>TEMPDB 大小

最大檔案大小的`tempdb`不能大於 24 GB/core 一般用途層上。 最大`tempdb`業務關鍵層上的大小會限制執行個體儲存體大小。 `tempdb` 一律將分成 12 個資料檔案。 無法變更每個檔案大小的上限，但可將新檔案新增至 `tempdb`。 有些查詢可能會傳回錯誤，如果他們需要超過 24 GB / 核心中`tempdb`。

### <a name="cannot-restore-contained-database"></a>無法還原自主的資料庫

受控執行個體無法還原[自主資料庫](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)。 時間點還原現有的自主資料庫無法運作的受控執行個體。 即將移除此問題，我們建議您在此同時移除內含項目選項，從您的資料庫放在受控執行個體上，請勿使用生產資料庫的內含項目選項。

### <a name="exceeding-storage-space-with-small-database-files"></a>小型資料庫檔案造成儲存空間超出限制

每個一般目的受控執行個體 」 最多 35 TB 的儲存體保留給 Azure 進階磁碟空間，以及每個資料庫檔案會放在不同的實體磁碟上。 磁碟大小可以是 128 GB、256 GB、512 GB、1 TB 或 4 TB。 針對磁碟上未使用的空間，並不收費，但「Azure 進階磁碟」大小的總和不可超過 35 TB。 在某些情況下，總計不需 8 TB 的「受控執行個體」可能會因內部分散的緣故而超過 35 TB 的 Azure 儲存體大小限制。

例如，一般目的受控執行個體可以有一個檔案置於 4 TB 磁碟的大小和放在不同的 128GB 磁碟的 248 檔案 (大小每 1 GB) 1.2 TB。 在此範例中：

- 配置的磁碟儲存體大小總計為 1 x 4 TB + 248 x 128 GB = 35 TB。
- 為執行個體上的資料庫保留的大小總計為 1 x 1.2 TB + 248 x 1 GB = 1.4 TB。

這說明了在特性情況下，由於特定的檔案散發方式，「受控執行個體」可能在您未預期的情形下，達到為所連結「Azure 進階磁碟」保留的 35 TB。

在此範例中，現有資料庫會繼續運作，只要不新增檔案，就可正常成長而不會有任何問題。 不過，因為沒有足夠空間可供新的磁碟機使用，所以無法建立或還原新的資料庫，即使所有資料庫的大小總計未達到執行個體大小限制也是如此。 在該情況下所傳回的錯誤將不清楚。

您可以[找出其餘的檔案數目](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1)使用系統檢視表。 如果您已達到此限制會試著[空白，然後刪除一些較小的檔案，使用 DBCC SHRINKFILE 陳述式](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file)或以 shitch[業務關鍵層中，不會有此限制](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>還原資料庫期間 SAS 金鑰設定不正確

如果 `CREDENTIAL` 中的共用存取簽章不正確，讀取 .bak 檔案的 `RESTORE DATABASE` 可能會不斷重試讀取 .bak 檔案，並在很長一段時間之後傳回錯誤。 請在還原資料庫前執行 RESTORE HEADERONLY，以確定 SAS 金鑰正確無誤。
請確定您已從使用 Azure 入口網站所產生的 SAS 金鑰中移除 `?` 前置符號。

### <a name="tooling"></a>工具

SQL Server Management Studio (SSMS) 和 SQL Server Data Tools (SSDT) 在存取受控執行個體時可能會有一些問題。

- 目前不支援使用 Azure AD 伺服器主體 (登入) 和使用者 (**公開預覽**) 搭配 SSDT。
- SSMS 不支援 Azure AD 伺服器主體 (登入) 和使用者的指令碼 (**公開預覽**)。

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>某些檢視、記錄與訊息中的不正確資料庫名稱

數個系統檢視、效能計數器、錯誤訊息、XEvents 與錯誤記錄檔項目都會顯示 GUID 資料庫識別碼，而非實際資料庫名稱。 不要依賴這些 GUID 識別碼，因為它們未來會被實際資料庫名稱取代。

### <a name="database-mail-profile"></a>資料庫郵件 XP

SQL 代理程式所使用的 database mail 設定檔，必須先呼叫`AzureManagedInstance_dbmail_profile`。

### <a name="error-logs-are-not-persisted"></a>錯誤記錄檔不會在工作階段之間保存下來

受控執行個體中可用的錯誤記錄檔不會保留下來，而且其大小不包括在儲存體大小上限中。 若發生容錯移轉，可能會自動清除錯誤記錄檔。

### <a name="error-logs-are-verbose"></a>錯誤記錄檔是詳細資訊記錄

受控執行個體會放置詳細資訊在錯誤記錄中，而且許多都是不相關的。 錯誤記錄檔中的資訊量未來將會減少。

**因應措施**：使用自訂程序來讀取篩選調某些不相關項目的錯誤記錄檔。 如需詳細資訊，請參閱[受控執行個體：sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/)。

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>不支援相同執行個體內兩個資料庫上的異動範圍

`TransactionScope` 如果兩個查詢傳送到相同的執行個體的相同交易範圍內的兩個資料庫，在.NET 中的類別無法運作︰

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

雖然此程式碼在相同執行個體內的資料上可正常運作，但是它需要 MSDTC。

**因應措施**：使用 [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) 在連線內容中使用其他資料庫，而非使用兩個連線。

### <a name="clr-modules-and-linked-servers-sometime-cant-reference-local-ip-address"></a>CLR 模組與連結的伺服器有時候無法參考本機 IP 位址

放置在受控執行個體中的 CLR 模組與參考目前執行個體的連結伺服器/分散式查詢有時無法解析本機執行個體的 IP。 此錯誤為暫時性問題。

**因應措施**：可能的話，在 CLR 模組中使用內容連線。

### <a name="tde-encrypted-databases-dont-support-user-initiated-backups"></a>TDE 加密資料庫不支援使用者啟動備份

您無法在使用透明資料加密 (TDE) 的資料庫上執行 `BACKUP DATABASE ... WITH COPY_ONLY`。 TDE 會強制使用內部 TDE 金鑰對備份進行加密，並且無法匯出金鑰，因此您將無法還原備份。

**因應措施**：使用自動備份和時間點還原，或停用資料庫加密。

## <a name="next-steps"></a>後續步驟

- 如需受控執行個體的詳細資訊，請參閱[受控執行個體是什麼？](sql-database-managed-instance.md)
- 如需功能與比較清單，請參閱 [SQL 的一般功能](sql-database-features.md)。
- 如需示範如何建立新受控執行個體的快速入門，請參閱[建立受控執行個體](sql-database-managed-instance-get-started.md)。
