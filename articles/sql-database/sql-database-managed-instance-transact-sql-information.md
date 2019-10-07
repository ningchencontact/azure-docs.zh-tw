---
title: Azure SQL Database 受控實例的 T-sql 差異 |Microsoft Docs
description: 本文將討論 Azure SQL Database 中的受控執行個體與 SQL Server之間的 T-SQL 差異
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
ms.date: 08/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: 9a043d07004870c00c656b655d56a1526f8993d8
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2019
ms.locfileid: "72000505"
---
# <a name="managed-instance-t-sql-differences-limitations-and-known-issues"></a>受控實例的 T-sql 差異、限制和已知問題

本文摘要說明並解釋 Azure SQL Database 受控實例與內部部署 SQL Server 資料庫引擎之間的語法和行為差異。 受控執行個體的部署選項提供對內部部署 SQL Server 資料庫引擎的高度相容性。 受控執行個體支援大部分的 SQL Server 資料庫引擎功能。

![遷移](./media/sql-database-managed-instance/migration.png)

受控執行個體引進了一些 PaaS 限制，相較于 SQL Server，還有一些行為變更。 差異分成下列類別：<a name="Differences"></a>

- [可用性](#availability)包括[Always On](#always-on-availability)和[備份](#backup)的差異。
- [安全性](#security)包括[審核](#auditing)、[憑證](#certificates)、[認證](#credential)、[密碼編譯提供者](#cryptographic-providers)、[登入和使用者](#logins-and-users)以及[服務金鑰和服務主要金鑰](#service-key-and-service-master-key)的差異。
- [設定](#configuration)包括[緩衝集區延伸](#buffer-pool-extension)、定[序](#collation)、[相容性層級](#compatibility-levels)、[資料庫鏡像](#database-mirroring)、[資料庫選項](#database-options)、[SQL Server Agent](#sql-server-agent) 和[資料表選項](#tables)的差異。
- [功能](#functionalities)包括[BULK INSERT/OPENROWSET](#bulk-insert--openrowset)、[CLR](#clr)、[DBCC](#dbcc)、[分散式交易](#distributed-transactions)、[擴充事件](#extended-events)、[外部程式庫](#external-libraries)、[filestream 和 FileTable](#filestream-and-filetable)、[全文檢索語義搜尋](#full-text-semantic-search)、[連結的伺服器](#linked-servers)、[PolyBase](#polybase)、[複寫](#replication)、[還原](#restore-statement)、[Service Broker](#service-broker)、[預存程式、函數和觸發程式](#stored-procedures-functions-and-triggers)。
- [環境設定](#Environment)，例如 vnet 和子網設定。

大部分的功能都是架構條件約束，並代表服務功能。

此頁面也會說明在受控實例中探索到的[暫時性已知問題](#Issues)，未來將會解決此問題。

## <a name="availability"></a>可用性

### <a name="always-on-availability"></a>Always On

受控實例內建[高可用性](sql-database-high-availability.md)，而且無法由使用者控制。 不支援下列語句：

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql)語句的[SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr)子句

### <a name="backup"></a>備份

受控實例具有自動備份，因此使用者可以建立完整資料庫 `COPY_ONLY` 備份。 不支援差異、記錄和檔案快照集備份。

- 使用受控實例，您只能將實例資料庫備份至 Azure Blob 儲存體帳戶：
  - 只支援 `BACKUP TO URL`。
  - `FILE`、`TAPE` 和備份裝置不受支援。
- 支援大部分的一般 `WITH` 選項。
  - `COPY_ONLY` 是強制的。
  - 不支援 `FILE_SNAPSHOT`。
  - 磁帶選項：不支援 `REWIND`、`NOREWIND`、`UNLOAD` 和 `NOUNLOAD`。
  - 記錄檔特有的選項： `NORECOVERY`、`STANDBY` 和 `NO_TRUNCATE` 不受支援。

限制： 

- 使用受控實例，您可以將實例資料庫備份至最多32個等量的備份，如果使用備份壓縮，這就足以應付最多 4 TB 的資料庫。
- 您無法在使用服務管理的透明資料加密（TDE）加密的資料庫上執行 `BACKUP DATABASE ... WITH COPY_ONLY`。 服務管理的 TDE 會強制使用內部 TDE 金鑰來加密備份。 無法匯出金鑰，因此您無法還原備份。 使用自動備份和時間點還原，或改為使用[客戶管理的（BYOK） TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) 。 您也可以在資料庫上停用加密。
- 在受控實例中使用 @no__t 0 命令的備份等量大小上限為 195 GB，這是最大 blob 大小。 在備份命令中增加條帶 (Stripe) 數目，可減少個別條帶 (Stripe) 的大小並維持在此限制內。

    > [!TIP]
    > 若要解決這項限制，當您從內部部署環境或虛擬機器中的 SQL Server 備份資料庫時，您可以：
    >
    > - 備份至 `DISK`，而不是備份至 `URL`。
    > - 將備份檔案上傳至 Blob 儲存體。
    > - 還原至受控實例。
    >
    > 受控實例中的 `Restore` 命令在備份檔案中支援較大的 blob 大小，因為用來儲存已上傳備份檔案的不同 blob 類型。

如需使用 T-SQL 進行備份的相關資訊，請參閱[備份](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql)。

## <a name="security"></a>安全性

### <a name="auditing"></a>稽核

Azure SQL Database 中的資料庫和 SQL Server 中的資料庫兩者之間的主要稽核差異在於：

- 使用 Azure SQL Database 中的 [受控實例] 部署選項時，會在伺服器層級進行審核。 @No__t-0 記錄檔會儲存在 Azure Blob 儲存體中。
- 在使用 Azure SQL Database 中的單一資料庫和彈性集區部署選項時，稽核作業則會在資料庫層級運作。
- 在 SQL Server 內部部署或虛擬機器中，審核功能會在伺服器層級運作。 事件會儲存在檔案系統或 Windows 事件記錄檔上。
 
受控執行個體中的 XEvent 稽核支援 Azure Blob 儲存體目標。 不支援檔案和 Windows 記錄檔。

向 Azure Blob 儲存體進行稽核的 `CREATE AUDIT` 語法有一個主要差異：

- 系統會提供新的語法 `TO URL`，讓您可以用來指定放置 @no__t 1 檔案之 Azure Blob 儲存體容器的 URL。
- 不支援語法 `TO FILE`，因為受控實例無法存取 Windows 檔案共用。

如需詳細資訊，請參閱： 

- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [稽核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>憑證

受控實例無法存取檔案共用和 Windows 資料夾，因此適用下列條件約束：

- 憑證不支援 `CREATE FROM` @ no__t-1 @ no__t-2 檔案。
- 不支援來自 `FILE` @ no__t-4 @ no__t-5 的 `CREATE` @ no__t-1 @ no__t-2 憑證。 無法使用私密金鑰檔案。 

請參閱 [CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) 和 [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql)。 
 
**因應措施**：請改為[取得憑證二進位內容和私密金鑰、將它儲存為 .sql 檔案，並從二進位檔建立](https://docs.microsoft.com/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)，而不是建立憑證的備份及還原備份：

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>認證

僅支援Azure Key Vault 和 `SHARED ACCESS SIGNATURE` 身分識別。 不支援 Windows 使用者。

請參閱 [CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) 和 [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql)。

### <a name="cryptographic-providers"></a>密碼編譯提供者

受控實例無法存取檔案，所以無法建立密碼編譯提供者：

- 不支援 `CREATE CRYPTOGRAPHIC PROVIDER`。 請參閱 [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql)。
- 不支援 `ALTER CRYPTOGRAPHIC PROVIDER`。 請參閱 [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)。

### <a name="logins-and-users"></a>登入和使用者

- 支援使用 `FROM CERTIFICATE`、`FROM ASYMMETRIC KEY` 和 `FROM SID` 建立的 SQL 登入。 請參閱 [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql)。
- 支援使用[CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)語法建立的 Azure Active Directory （Azure AD）伺服器主體（登入），或[從 Login [Azure AD 登入]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current)語法建立使用者（公開預覽）。 這些登入是在伺服器層級建立。

    受控實例支援 Azure AD 的資料庫主體，語法 `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`。 這項功能也稱為 Azure AD 自主資料庫使用者。

- 不支援使用 `CREATE LOGIN ... FROM WINDOWS` 語法建立的 Windows 登入。 使用 Azure Active Directory 登入和使用者。
- 建立實例的 Azure AD 使用者具有不[受限制的系統管理員許可權](sql-database-manage-logins.md#unrestricted-administrative-accounts)。
- 您可以使用 `CREATE USER ... FROM EXTERNAL PROVIDER` 語法來建立非系統管理員 Azure AD 資料庫層級的使用者。 請參閱 [CREATE USER ...FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users)。
- Azure AD 伺服器主體（登入）僅支援一個受控實例中的 SQL 功能。 需要跨實例互動的功能，不論它們是否位於相同的 Azure AD 租使用者或不同的 tenant 中，Azure AD 的使用者都不支援。 這類功能的範例如下：

  - SQL 異動複寫。
  - 連結伺服器。

- 不支援將對應至 Azure AD 群組的 Azure AD 登入設定為資料庫擁有者。
- 支援使用其他 Azure AD 主體來模擬 Azure AD 伺服器層級主體，例如[EXECUTE as](/sql/t-sql/statements/execute-as-transact-sql)子句。 執行身分限制包括：

  - 當名稱不同于登入名稱時，不支援 Azure AD 使用者執行 AS 使用者。 例如，透過從登入 [john@contoso.com] 的 CREATE USER [myAadUser] 語法建立使用者，並透過 EXEC 以 USER = _myAadUser_嘗試模擬。 當您從 Azure AD 伺服器主體（登入）建立**使用者**時，請將登入指定為來自**login**的相同 login_name。
  - 只有屬於 `sysadmin` 角色之一部分的 SQL Server 層級主體（登入），才能夠執行以 Azure AD 主體為目標的下列作業：

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Azure AD 伺服器主體（登入）的公開預覽限制：

  - 受控實例的 Active Directory 系統管理員限制：

    - 用來設定受控實例的 Azure AD 系統管理員不能用來在受控實例內建立 Azure AD 伺服器主體（登入）。 您必須使用屬於 @no__t 0 角色的 SQL Server 帳戶來建立第一個 Azure AD 伺服器主體（登入）。 Azure AD 伺服器主體（登入）正式推出之後，將會移除此暫時限制。 如果您嘗試使用 Azure AD 的系統管理員帳戶來建立登入，您會看到下列錯誤： `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - 目前，在 master 資料庫中建立的第一個 Azure AD 登入必須由標準 SQL Server 帳戶（非 Azure AD）建立，這是使用從外部提供者[建立登](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)入的 `sysadmin` 角色。 正式運作之後，將會移除這項限制。 接著，您可以使用受控實例的 Active Directory 系統管理員，建立初始的 Azure AD 登入。
    - Azure AD 登入不支援與 SQL Server Management Studio 或 SqlPackage 搭配使用的 DacFx （匯出/匯入）。 Azure AD 伺服器主體（登入）正式推出後，將會移除此限制。
    - 搭配 SQL Server Management Studio 使用 Azure AD 伺服器主體（登入）：

      - 不支援使用任何已驗證登入的腳本 Azure AD 登入。
      - IntelliSense 無法辨識從外部提供者語句建立登入，並顯示紅色底線。

- 只有由受控實例布建程式所建立的伺服器層級主體登入、伺服器角色的成員（例如 `securityadmin` 或 `sysadmin`），或是伺服器層級具有 ALTER ANY LOGIN 許可權的其他登入，才能建立 Azure AD 伺服器主體。適用于受控實例之 master 資料庫中的（登入）。
- 如果登入是 SQL 主體，只有屬於 @no__t 0 角色之一部分的登入可以使用 create 命令來建立 Azure AD 帳戶的登入。
- Azure AD 登入必須是用於 Azure SQL Database 受控實例之相同目錄中 Azure AD 的成員。
- 從 SQL Server Management Studio 18.0 preview 5 開始，物件總管可以看到 Azure AD 伺服器主體（登入）。
- 可允許 Azure AD 伺服器主體 (登入) 與 Azure AD 系統管理員帳戶重疊。 當您解析主體並將許可權套用至受控實例時，Azure AD 伺服器主體（登入）會優先于 Azure AD 系統管理員。
- 在驗證期間，會套用下列順序來解析驗證主體：

    1. 如果 Azure AD 帳戶以直接對應至 Azure AD 伺服器主體（登入）的形式存在，這會在 server_principals 中顯示為類型 "E"，授與存取權，並套用 Azure AD 伺服器主體（登入）的許可權。
    2. 如果 Azure AD 帳戶是對應至 Azure AD 伺服器主體（登入）之 Azure AD 群組的成員，而這在 server_principals 中會顯示為類型 "X"，則授與存取權，並套用 Azure AD 群組登入的許可權。
    3. 如果 Azure AD 帳戶是特殊的入口網站設定 Azure AD 系統管理員針對受控實例（不存在於受管理的實例系統檢視），請針對受控實例（舊版模式）套用 Azure AD 系統管理員特殊的固定許可權。
    4. 如果 Azure AD 帳戶是直接對應到資料庫中的 Azure AD 使用者（在 database_principals 中會顯示為類型 "E"），則會授與存取權，並套用 Azure AD 資料庫使用者的許可權。
    5. 如果 Azure AD 帳戶是對應至資料庫中 Azure AD 使用者的 Azure AD 群組成員（database_principals 為類型 "X"），則授與存取權，並套用 Azure AD 群組登入的許可權。
    6. 如果有 Azure AD 登入對應到 Azure AD 使用者帳戶或 Azure AD 群組帳戶（解析為進行驗證的使用者），則會套用此 Azure AD 登入的擁有權限。

### <a name="service-key-and-service-master-key"></a>服務金鑰和服務主要金鑰

- 不支援[主要金鑰備份](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql)（由 SQL Database 服務管理）。
- 不支援[主要金鑰還原](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql)（由 SQL Database 服務管理）。
- 不支援[服務主要金鑰備份](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql)（由 SQL Database 服務管理）。
- 不支援[服務主要金鑰還原](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql)（由 SQL Database 服務管理）。

## <a name="configuration"></a>組態

### <a name="buffer-pool-extension"></a>緩衝集區延伸

- 不支援[緩衝集區延伸](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension)模組。
- 不支援 `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`。 請參閱 [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql)。

### <a name="collation"></a>定序

預設執行個體定序為 `SQL_Latin1_General_CP1_CI_AS`，而且可指定為建立參數。 請參閱[定序](https://docs.microsoft.com/sql/t-sql/statements/collations)。

### <a name="compatibility-levels"></a>相容性層級

- 支援的相容性層級為100、110、120、130、140和150。
- 不支援低於 100 的相容性層級。
- 新資料庫的預設相容性層級為 140。 若為已還原的資料庫，相容性層級會維持不變（如果它是100和更新版本）。

請參閱 [ALTER DATABASE 相容性層級](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。

### <a name="database-mirroring"></a>資料庫鏡像

不支援資料庫鏡像。

- 不支援 `ALTER DATABASE SET PARTNER` 和 `SET WITNESS` 選項。
- 不支援 `CREATE ENDPOINT … FOR DATABASE_MIRRORING`。

如需詳細資訊，請參閱 [ALTER DATABASE SET PARTNER and SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) 和 [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)。

### <a name="database-options"></a>資料庫選項

- 不支援多個記錄檔。
- 「一般用途」服務層級中不支援記憶體內部物件。 
- 每個一般用途實例都有280個檔案的限制，這表示每個資料庫最多可有280個檔案。 一般用途層中的資料和記錄檔都會計入此限制。 [業務關鍵層支援每個資料庫32767個](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)檔案。
- 資料庫不能包含包含 filestream 資料的檔案群組。 如果 .bak 包含 @no__t 0 資料，還原將會失敗。 
- 每個檔案都位於 Azure Blob 儲存體中。 每個檔案的 IO 和輸送量均取決於每個個別檔案的大小。

#### <a name="create-database-statement"></a>CREATE DATABASE 陳述式

下列限制適用于 `CREATE DATABASE`：

- 無法定義檔案和檔案群組。 
- 不支援 `CONTAINMENT` 選項。 
- `WITH` 選項不受支援。 
   > [!TIP]
   > 因應措施是在 `CREATE DATABASE` 之後，使用 `ALTER DATABASE` 來設定資料庫選項，以新增檔案或設定內含專案。 

- 不支援 `FOR ATTACH` 選項。
- 不支援 `AS SNAPSHOT OF` 選項。

如需詳細資訊，請參閱 [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql)。

#### <a name="alter-database-statement"></a>ALTER DATABASE 陳述式

有些檔案屬性無法設定或變更：

- 不能在 `ALTER DATABASE ADD FILE (FILENAME='path')` T-sql 語句中指定檔案路徑。 從指令碼中移除 `FILENAME`，因為受控執行個體會自動放置這些檔案。 
- 無法使用 `ALTER DATABASE` 語句來變更檔案名。

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

- 目前不支援在受控實例中啟用和停用 SQL Server Agent。 SQL Agent 一直在執行中。
- SQL Server Agent 設定是唯讀的。 受控實例中不支援 `sp_set_agent_properties` 的程式。 
- 工作 (Job)
  - 支援 T-SQL 作業步驟。
  - 支援下列複寫作業：
    - 交易記錄讀取器
    - 快照集
    - 散發者
  - 支援 SSIS 作業步驟。
  - 目前不支援其他類型的作業步驟：
    - 不支援合併式複寫作業步驟。 
    - 不支援佇列讀取器。 
    - 尚未支援命令 shell。
  - 受控實例無法存取外部資源，例如，透過 robocopy 的網路共用。 
  - 不支援 SQL Server Analysis Services。
- 部分支援通知。
- 雖然您需要設定 Database Mail 設定檔，但仍可支援電子郵件通知。 SQL Server Agent 只能使用一個 Database Mail 設定檔，而且必須在 `AzureManagedInstance_dbmail_profile` 時呼叫它。 
  - 不支援呼叫器。
  - 不支援 NetSend。
  - 尚不支援警示。
  - 不支援 proxy。
- 不支援 EventLog。

目前不支援下列 SQL 代理程式功能：

- Proxy
- 在閒置 CPU 上排程工作
- 啟用或停用代理程式
- 警示

如需有關 SQL Server Agent 的資訊，請參閱 [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)。

### <a name="tables"></a>資料表

不支援下列資料表類型：

- [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server)
- [外部資料表](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql)（Polybase）
- [MEMORY_OPTIMIZED](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) （只在一般用途層中不支援）

如需有關如何建立和改變數據表的詳細資訊，請參閱[CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql)和[alter TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)。

## <a name="functionalities"></a>功能

### <a name="bulk-insert--openrowset"></a>Bulk insert/OPENROWSET

受控實例無法存取檔案共用和 Windows 資料夾，因此必須從 Azure Blob 儲存體匯入檔案：

- 當您從 Azure Blob 儲存體匯入檔案時，@no__t 1 命令中需要 `DATASOURCE`。 請參閱[大量插入](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql)。
- 當您從 Azure Blob 儲存體讀取檔案的內容時，`OPENROWSET` 函數中需要 `DATASOURCE`。 請參閱 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)。
- `OPENROWSET` 可以用來讀取來自其他 Azure SQL 單一資料庫、受控實例或 SQL Server 實例的資料。 不支援 Oracle 資料庫或 Excel 檔案之類的其他來源。

### <a name="clr"></a>CLR

受控實例無法存取檔案共用和 Windows 資料夾，因此適用下列條件約束：

- 只支援 `CREATE ASSEMBLY FROM BINARY`。 請參閱[CREATE ASSEM 構件方法是 FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)。 
- 不支援 `CREATE ASSEMBLY FROM FILE`。 請參閱 [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)。
- `ALTER ASSEMBLY` 無法參考檔案。 請參閱 [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql)。

### <a name="database-mail-db_mail"></a>Database Mail （db_mail）
 - `sp_send_dbmail` 無法使用 @file_attachments 參數傳送附件。 本機檔案系統和外部共用或 Azure Blob 儲存體無法從這個程式存取。
 - 請參閱與 `@query` 參數和驗證相關的已知問題。
 
### <a name="dbcc"></a>DBCC

受控實例中不支援在 SQL Server 中啟用的未記載 DBCC 語句。

- 僅支援有限數目的全域追蹤旗標。 不支援工作階段層級 `Trace flags`。 請參閱[追蹤旗標](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)。
- [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql)和[dbcc TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql)會使用有限數目的全域追蹤旗標。
- 無法使用選項 REPAIR_ALLOW_DATA_LOSS、REPAIR_FAST 和 REPAIR_REBUILD 的[DBCC CHECKDB](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) ，因為無法在 `SINGLE_USER` 模式中設定資料庫，請參閱[ALTER database 差異](#alter-database-statement)。 Azure 支援小組會處理潛在的資料庫損毀。 如果您注意到應該修正的資料庫損毀，請洽詢 Azure 支援。

### <a name="distributed-transactions"></a>分散式交易

受控實例中目前不支援 MSDTC 和[彈性交易](sql-database-elastic-transactions-overview.md)。

### <a name="extended-events"></a>擴充事件

不支援擴充事件（XEvents）的某些 Windows 特定目標：

- 不支援 `etw_classic_sync` 目標。 將 `.xel` 檔案儲存在 Azure Blob 儲存體中。 請參閱 [etw_classic_sync 目標](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)。
- 不支援 `event_file` 目標。 將 `.xel` 檔案儲存在 Azure Blob 儲存體中。 請參閱 [event_file 目標](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)。

### <a name="external-libraries"></a>外部程式庫

資料庫內 R 和 Python 尚未支援外部程式庫。 請參閱 [SQL Server Machine Learning 服務](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services)。

### <a name="filestream-and-filetable"></a>Filestream 和 FileTable

- 不支援 Filestream 資料。
- 資料庫不能包含具有 @no__t 0 資料的檔案群組。
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

- 支援的目標為受控實例、單一資料庫和 SQL Server 實例。 
- 連結的伺服器不支援分散式可寫入交易（MS DTC）。
- 不支援的目標為檔案、Analysis Services 和其他 RDBMS。 嘗試使用原生 CSV 匯入 Azure Blob 儲存體使用 `BULK INSERT` 或 `OPENROWSET` 做為檔案匯入的替代方法。

作業

- 不支援跨執行個體寫入交易。
- 支援使用 `sp_dropserver` 卸除連結的伺服器。 請參閱 [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)。
- @No__t-0 函數只能用來在 SQL Server 實例上執行查詢。 它們可以是受控、內部部署或在虛擬機器中。 請參閱 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)。
- @No__t-0 函數只能用來在 SQL Server 實例上執行查詢。 它們可以是受控、內部部署或在虛擬機器中。 僅支援 `SQLNCLI`、`SQLNCLI11` 和 @no__t 2 值做為提供者。 例如 `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`。 請參閱 [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)。
- 連結的伺服器無法用來從網路共用讀取檔案（Excel、CSV）。 嘗試使用從 Azure Blob 儲存體讀取 CSV 檔案的[BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file)或[OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) 。 在[受管理的實例意見專案](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)上追蹤此要求 |

### <a name="polybase"></a>PolyBase

不支援參考 HDFS 或 Azure Blob 儲存體中檔案的外部資料表。 如需有關 PolyBase 的詳細資訊，請參閱[polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)。

### <a name="replication"></a>複寫

- 支援快照和雙向複寫類型。 不支援合併式複寫、點對點複寫和可更新的訂閱。
- [異動複寫](sql-database-managed-instance-transactional-replication.md)在受控實例上可供公開預覽，但有一些條件約束：
    - 所有類型的複寫參與者（發行者、散發者、提取訂閱者和發送訂閱者）都可以放在受控實例上，但發行者和散發者必須同時位於雲端或內部部署兩者。
    - 受控實例可以與最新版本的 SQL Server 進行通訊。 請參閱[這裡](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems)的支援版本。
    - 異動複寫有一些[額外的網路需求](sql-database-managed-instance-transactional-replication.md#requirements)。

如需有關設定複寫的詳細資訊，請參閱複寫[教學](replication-with-sql-database-managed-instance.md)課程。


如果已在[容錯移轉群組](sql-database-auto-failover-group.md)中的資料庫上啟用複寫，受管理的實例系統管理員必須清除舊主要複本上的所有發行集，並在容錯移轉之後，在新的主要上重新設定它們。 此案例中需要下列活動：

1. 停止在資料庫上執行的所有複寫作業（如果有的話）。
2. 在發行者資料庫上執行下列腳本，以卸載發行者的訂閱中繼資料：

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. 捨棄訂閱者的訂閱中繼資料。 在訂閱者實例的訂閱資料庫中執行下列腳本：

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. 藉由在已發行的資料庫中執行下列腳本，強制卸載「發行者」中的所有複寫物件：

   ```sql
   EXEC sp_removedbreplication
   ```

1. 從原始的主要實例強制卸載舊的散發者（如果容錯回復到用來擁有「散發者」的舊主伺服器）。 在舊的散發者受控實例的 master 資料庫上執行下列腳本：

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

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
- 來源: 
  - `FROM URL` （Azure Blob 儲存體）是唯一支援的選項。
  - 不支援 `FROM DISK`/`TAPE`/備份裝置。
  - 不支援備份組。
- 不支援 `WITH` 選項，例如 no `DIFFERENTIAL` 或 `STATS`。
- `ASYNC RESTORE`:即使用戶端連接中斷，仍會繼續還原。 如果您的連接已中斷，您可以查看 [`sys.dm_operation_status`] 視圖，以取得還原作業的狀態，以及建立和卸載資料庫的狀態。 請參閱 [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)。 

系統會設定或覆寫下列資料庫選項，而且之後無法變更： 

- 如果不是在 .bak 檔案中啟用訊息代理程式，`NEW_BROKER`。 
- 如果不是在 .bak 檔案中啟用訊息代理程式，`ENABLE_BROKER`。 
- 如果 .bak 檔案中的資料庫具有 `AUTO_CLOSE=ON`，則 `AUTO_CLOSE=OFF`。 
- 如果 .bak 檔案中的資料庫具有 `SIMPLE` 或 @no__t 2 復原模式，則 `RECOVERY FULL`。
- 系統會新增記憶體優化的檔案群組，如果它不在來源 .bak 檔案中，則會呼叫 XTP。 
- 任何現有的記憶體優化檔案群組都會重新命名為 XTP。 
- `SINGLE_USER` 和 @no__t 1 選項會轉換成 `MULTI_USER`。

限制： 

- 損毀資料庫的備份可能會根據損毀的類型還原，但在修正損毀之前，將不會採取自動備份。 請確定您在來源實例上執行 `DBCC CHECKDB`，並使用備份 `WITH CHECKSUM`，以避免發生此問題。
- 還原資料庫的 `.BAK` 檔案（其中包含本檔中所述的任何限制（例如，`FILESTREAM` 或 @no__t 2 物件），無法在受控執行個體上還原。
- 包含多個備份組的 @no__t 0 檔案無法還原。 
- 包含多個記錄檔的 @no__t 0 檔案無法還原。
- 包含大於 8 TB、作用中記憶體內部 OLTP 物件的資料庫，或每個實例超過280個檔案的備份，都無法在一般用途的實例上還原。 
- 包含大於 4 TB 的資料庫或記憶體內部 OLTP 物件的備份，其大小總計大於[資源限制](sql-database-managed-instance-resource-limits.md)中所述的大小，因此無法在業務關鍵實例上還原。
如需 restore 語句的詳細資訊，請參閱[restore 語句](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)。

 > [!IMPORTANT]
 > 相同的限制適用于內建的時間點還原作業。 例如，無法在業務關鍵實例上還原超過 4 TB 的一般目的資料庫。 具有記憶體內部 OLTP 檔案或超過280個檔案的商務關鍵資料庫，無法在一般用途實例上還原。

### <a name="service-broker"></a>Service broker

不支援跨執行個體的服務訊息代理程式：

- `sys.routes`:必要條件是，您必須從 sys.databases 選取位址。 位址在每個路由上都必須是 LOCAL。 請參閱 [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql)。
- `CREATE ROUTE`:除了 `LOCAL` 以外，您無法使用 `CREATE ROUTE` 搭配 `ADDRESS`。 請參閱 [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql)。
- `ALTER ROUTE`:除了 `LOCAL` 以外，您無法使用 `ALTER ROUTE` 搭配 `ADDRESS`。 請參閱 [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql)。 

### <a name="stored-procedures-functions-and-triggers"></a>預存程式、函數和觸發程式

- 一般用途層中不支援 `NATIVE_COMPILATION`。
- 不支援下列 [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 選項： 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- 不支援 `sp_execute_external_scripts`。 請參閱 [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)。
- 不支援 `xp_cmdshell`。 請參閱 [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)。
- 不支援 `Extended stored procedures`，其中包括 `sp_addextendedproc` @ no__t-2and `sp_dropextendedproc`。 請參閱[擴充預存程式](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)。
- 不支援 `sp_attach_db`、`sp_attach_single_file_db` 和 `sp_detach_db`。 請參閱 [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql)、[sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 和 [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)。

### <a name="system-functions-and-variables"></a>系統函數和變數

下列變數、函式和檢視會傳回不同的結果：

- `SERVERPROPERTY('EngineEdition')` 會傳回值8。 此屬性只會識別出受控執行個體。 請參閱 [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)。
- `SERVERPROPERTY('InstanceName')` 會傳回 Null，因為實例存在於 SQL Server 的概念不適用於受控實例。 請參閱 [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)。
- `@@SERVERNAME` 會傳回完整的 DNS 「可連線」名稱，例如 my-managed-instance.wcus17662feb9ce98.database.windows.net。 請參閱 [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql)。 
- `SYS.SERVERS` 會傳回完整的 DNS 「可連線」名稱，例如 "name" 和 "data_source" 屬性的 `myinstance.domain.database.windows.net`。 請參閱 [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)。
- `@@SERVICENAME` 會傳回 Null，因為存在於 SQL Server 的服務概念不適用於受控實例。 請參閱 [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql)。
- 支援 `SUSER_ID`。 如果 Azure AD 登入不在 syslogins 中，它會傳回 Null。 請參閱 [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql)。 
- 不支援 `SUSER_SID`。 傳回錯誤的資料，這是暫時性的已知問題。 請參閱 [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql)。 

## <a name="Environment"></a>環境條件約束

### <a name="subnet"></a>Subnet
-  您無法將任何其他資源（例如虛擬機器）放在已部署受控實例的子網中。 使用不同的子網來部署這些資源。
- 子網必須有足夠的可用[IP 位址](sql-database-managed-instance-connectivity-architecture.md#network-requirements)數目。 最小值為16，而建議在子網中至少要有32個 IP 位址。
- [服務端點無法與受控實例的子網建立關聯](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。 當您建立虛擬網路時，請確定已停用 [服務端點] 選項。
- 您可以在區域中部署的虛擬核心數目和實例類型有一些[條件約束和限制](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)。
- 有一些[必須在子網上套用的安全性規則](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。

### <a name="vnet"></a>VNET
- VNet 可以使用資源模型來部署-不支援 VNet 的傳統模型。
- 建立受控實例之後，不支援將受控實例或 VNet 移至另一個資源群組或訂用帳戶。
- 某些服務，例如 App Service 環境、邏輯應用程式和受控實例（用於異地複寫、異動複寫或透過連結的伺服器），如果其 Vnet 是使用 global 連線，則無法存取不同區域中的受控實例[對等互連](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)。 您可以透過 ExpressRoute 或透過 VNet 閘道的 VNet 對 VNet 來連接到這些資源。

### <a name="tempdb"></a>TEMPDB

在一般用途層上，`tempdb` 的檔案大小上限不能大於每個核心 24 GB。 業務關鍵層上的 `tempdb` 大小上限受到實例儲存體大小的限制。 `Tempdb` 記錄檔大小在一般用途和業務關鍵層上限制為 120 GB。 如果 `tempdb` 中的每個核心需要超過 24 GB，或如果產生超過 120 GB 的記錄資料，則某些查詢可能會傳回錯誤。

### <a name="error-logs"></a>錯誤記錄

受控實例會將詳細資訊放在錯誤記錄檔中。 錯誤記錄檔中有許多內部系統事件會記錄下來。 使用自訂程式來讀取篩選掉一些不相關專案的錯誤記錄檔。 如需詳細資訊，請參閱[受控實例– sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/)。

## <a name="Issues"></a>已知問題

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>嘗試移除不是空的檔案時傳回錯誤的錯誤

**日期**2019年10月

SQL Server/受控執行個體[不允許使用者捨棄不是空的](https://docs.microsoft.com/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites)檔案。 如果您嘗試使用 `ALTER DATABASE REMOVE FILE` 語句移除非空白的資料檔案，則不會立即傳回 `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` 的錯誤。 受控執行個體會繼續嘗試卸載檔案，而且在使用 `Internal server error` 30 分鐘之後，作業將會失敗。

**因應措施**：使用 `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` 命令移除檔案的內容。 如果這是檔案群組中唯一的檔案，您必須先從與這個檔案群組相關聯的資料表或分割區中刪除資料，然後再壓縮檔案，並選擇性地將此資料載入另一個資料表/資料分割。

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>進行中的資料庫還原會封鎖變更服務層級和建立實例作業

**日期**Sep 2019

進行中的 `RESTORE` 語句、資料移轉服務遷移程式和內建的還原時間點，將會封鎖更新服務層級或重新調整現有實例的大小，並建立新的實例，直到還原程式完成為止。 還原程式將會在還原程式執行所在的相同子網中，封鎖受控實例和實例集區上的這些作業。 實例集區中的實例不受影響。 建立或變更服務層作業不會失敗或超時，一旦還原程式完成或取消，就會繼續進行。

**因應措施**：等到還原程式完成，或如果建立或更新服務層級作業的優先順序較高，則取消還原程式。

### <a name="missing-validations-in-restore-process"></a>還原過程中缺少驗證

**日期**Sep 2019

`RESTORE` 語句和內建的還原時間點不會對還原的資料庫執行一些 nessecary 檢查：
- **DBCC CHECKDB** -  @ no__t-2 語句不會在還原的資料庫上執行 `DBCC CHECKDB`。 如果原始資料庫已損毀，或備份檔案在複製到 Azure blob 儲存體時損毀，將不會執行自動備份，Azure 支援將會與客戶聯繫。 
- 內建的時間點還原程式並不會檢查來自「商務關鍵性」實例的「自動備份」是否包含[記憶體內部 OLTP 物件](sql-database-in-memory.md#in-memory-oltp)。 

**因應措施**：請確定您在進行備份之前，在源資料庫上執行 `DBCC CHECKDB`，並在備份中使用 `WITH CHECKSUM` 選項，以避免可能會在受控實例上還原的損毀。 如果您要在一般用途層進行還原，請確定您的源資料庫未包含[記憶體內部 OLTP 物件](sql-database-in-memory.md#in-memory-oltp)。

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>業務關鍵服務層級上的 Resource Governor 可能需要在容錯移轉之後重新設定

**日期**Sep 2019

[Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)功能可讓您限制指派給使用者工作負載的資源，可能會在容錯移轉或使用者起始的服務層級變更之後，不正確地分類某些使用者工作負載（例如，max vCore 或 max 實例的變更）儲存體大小）。

**因應措施**：如果您使用[Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)，請在實例啟動時，定期或在執行 sql 工作的 Sql 代理程式作業中執行 `ALTER RESOURCE GOVERNOR RECONFIGURE`。

### <a name="cannot-authenticate-to-external-mail-servers-using-secure-connection-ssl"></a>無法使用安全連線（SSL）向外部郵件伺服器進行驗證

**日期**2019年8月

[使用安全連線（SSL）設定](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-database-mail)的 Database mail 無法在 Azure 外部的某些電子郵件伺服器上進行驗證。 這是即將解決的安全性設定問題。

**因應措施：** 暫時從 database mail 設定中移除安全連線（SSL），直到問題解決為止。 

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>服務層升級之後，必須重新初始化跨資料庫 Service Broker 對話方塊

**日期**2019年8月

跨資料庫 Service Broker 對話方塊會在變更服務層級作業之後，停止將訊息傳遞至其他資料庫中的服務。 訊息不會**遺失**，而且可以在寄件者佇列中找到。 受控執行個體中的任何虛擬核心或實例儲存體大小變更，會導致在所有資料庫中變更[sys.databases](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) view 中的 `service_broke_guid` 值。 任何在其他資料庫中參考服務代理人的[BEGIN DIALOG](https://docs.microsoft.com/en-us/sql/t-sql/statements/begin-dialog-conversation-transact-sql)語句所建立的 @no__t 0，將會停止將訊息傳遞至目標服務。

**因應措施：** 請先停止使用跨資料庫 Service Broker 對話交談的任何活動，再更新服務層級，然後在之後重新初始化。 如果有剩餘的訊息在服務層級變更後無法傳遞，請從來源佇列讀取訊息，然後將它們重新傳送至目標佇列。

### <a name="impersonification-of-aad-login-types-is-not-supported"></a>不支援 AAD 登入類型的 Impersonification

**日期**2019年7月

不支援使用 `EXECUTE AS USER` 或 `EXECUTE AS LOGIN` 個下列 AAD 主體的模擬：
-   AAD 使用者別名。 在此情況下，會傳回下列錯誤 `15517`。
- AAD 登入和以 AAD 應用程式或服務主體為基礎的使用者。 在此情況下，會傳回下列錯誤 `15517` 並 `15406`。

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>sp_send_db_mail 中不支援 @query 參數

**日期**2019 年 4 月

[Sp_send_db_mail ](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql)`@query`程式中的參數無法使用。

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>必須在異地容錯移轉之後重新設定異動複寫

**日期**Mar 2019

如果在自動容錯移轉群組中的資料庫上啟用異動複寫，則受管理的實例系統管理員必須清除舊主要複本上的所有發行集，並在容錯移轉至另一個區域之後，在新的主要上重新設定它們。 如[需詳細資訊，請](#replication)參閱複寫。

### <a name="aad-logins-and-users-are-not-supported-in-tools"></a>工具中不支援 AAD 登入和使用者

**日期**Jan 2019

SQL Server Management Studio 和 SQL Server Data Tools 不完全支援 Azure Active directory 登入和使用者。
- 目前不支援搭配 SQL Server Data Tools 使用 Azure AD 伺服器主體（登入）和使用者（公開預覽）。
- SQL Server Management Studio 不支援 Azure AD 伺服器主體（登入）和使用者的腳本（公開預覽）。

### <a name="temporary-database-is-used-during-restore-operation"></a>還原作業期間會使用暫存資料庫

當資料庫在受控執行個體上還原時，還原服務會先建立具有所需名稱的空資料庫，以在實例上配置名稱。 經過一段時間之後，就會卸載此資料庫，並啟動實際資料庫的還原。 處於*還原*狀態的資料庫將暫時具有隨機 GUID 值，而不是名稱。 一旦還原程式完成，暫存名稱就會變更為在 `RESTORE` 語句中指定的所需名稱。 在初始階段中，使用者可以存取空的資料庫，甚至在此資料庫中建立資料表或載入資料。 當還原服務啟動第二個階段時，將會卸載此暫存資料庫。

**因應措施**：除非您看到還原已完成，否則請不要存取您要還原的資料庫。

### <a name="tempdb-structure-and-content-is-re-created"></a>已重新建立 TEMPDB 結構和內容

@No__t 0 資料庫一律會分割成12個資料檔案，而且無法變更檔案結構。 無法變更每個檔案的大小上限，而且無法將新檔案新增至 `tempdb`。 當實例啟動或故障時，`Tempdb` 一律會重新建立為空的資料庫，而且不會保留在 `tempdb` 中所做的任何變更。

### <a name="exceeding-storage-space-with-small-database-files"></a>小型資料庫檔案造成儲存空間超出限制

`CREATE DATABASE`、`ALTER DATABASE ADD FILE` 和 @no__t 2 語句可能會失敗，因為實例可以達到 Azure 儲存體限制。

每個一般用途的受控實例最多可保留 35 TB 的儲存體，以供 Azure Premium 磁碟空間之用。 每個資料庫檔案都會放在個別的實體磁片上。 磁碟大小可以是 128 GB、256 GB、512 GB、1 TB 或 4 TB。 磁片上未使用的空間不會收費，但 Azure Premium 磁片大小的總計總和不能超過 35 TB。 在某些情況下，不需要 8 TB 的受控實例總計可能會超過 35 TB 的 Azure 儲存體大小限制，因為內部分散。

例如，一般用途的受控實例可能會有一個大小為 1.2 TB 的大型檔案，放在 4 TB 的磁片上。 它也可能有248個檔案，每個檔案都有 1 GB 的大小，每個檔案放在不同的 128 GB 磁片上。 在此範例中：

- 配置的磁碟儲存體大小總計為 1 x 4 TB + 248 x 128 GB = 35 TB。
- 為執行個體上的資料庫保留的大小總計為 1 x 1.2 TB + 248 x 1 GB = 1.4 TB。

這個範例說明在某些情況下，由於特定的檔案散發，受控實例可能會達到 35 TB 的限制，而當您可能不會預期時，它會保留給附加的 Azure Premium 磁片。

在此範例中，只要未新增新檔案，現有的資料庫就會繼續正常執行，而且不會有任何問題就可以成長。 無法建立或還原新的資料庫，因為新磁片磁碟機的空間不足，即使所有資料庫的大小總計未達到實例大小限制也一樣。 在此情況下傳回的錯誤並不清楚。

您可以使用系統檢視來[識別剩餘](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1)的檔案數目。 如果您達到此限制，請嘗試[使用 DBCC SHRINKFILE 語句來清空和刪除一些較小](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file)的檔案，或切換至[不具有此限制的業務關鍵層](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。

### <a name="guid-values-shown-instead-of-database-names"></a>顯示的 GUID 值，而不是資料庫名稱

數個系統檢視、效能計數器、錯誤訊息、XEvents 與錯誤記錄檔項目都會顯示 GUID 資料庫識別碼，而非實際資料庫名稱。 不要依賴這些 GUID 識別碼，因為未來會以實際的資料庫名稱來取代它們。

### <a name="error-logs-arent-persisted"></a>錯誤記錄檔不會保存

受控實例中可用的錯誤記錄檔不會保存下來，而且其大小不會包含在最大儲存體限制中。 如果發生容錯移轉，可能會自動清除錯誤記錄。 錯誤記錄檔歷程記錄中可能有間距，因為在數部虛擬機器上已將受控執行個體移動數次。

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>不支援相同實例內兩個資料庫的交易範圍

如果兩個查詢傳送至相同交易範圍下相同實例中的兩個資料庫，.NET 中的 `TransactionScope` 類別就無法使用：

```csharp
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

雖然此程式碼適用于相同實例內的資料，但它需要 MSDTC。

**因應措施：** 使用[sqlconnection.changedatabase （String）](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase)在連接內容中使用另一個資料庫，而不是使用兩個連接。

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR 模組與連結的伺服器有時候無法參考本機 IP 位址

放在受控實例中的 CLR 模組和連結的伺服器，或參考目前實例的分散式查詢有時無法解析本機實例的 IP。 此錯誤為暫時性問題。

**因應措施：** 如果可能的話，請在 CLR 模組中使用內容連接。

## <a name="next-steps"></a>後續步驟

- 如需受控實例的詳細資訊，請參閱[什麼是受控實例？](sql-database-managed-instance.md)
- 如需功能和比較清單，請參閱[Azure SQL Database 功能比較](sql-database-features.md)。
- 如需示範如何建立新受控實例的快速入門，請參閱[建立受控實例](sql-database-managed-instance-get-started.md)。
