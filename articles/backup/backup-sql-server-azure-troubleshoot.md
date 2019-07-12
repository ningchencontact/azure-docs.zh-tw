---
title: 疑難排解 SQL Server 資料庫備份使用 Azure 備份 |Microsoft Docs
description: 適用於在 Azure VM 上執行並使用 Azure 備份進行備份之 SQL Server 資料庫的疑難排解資訊。
services: backup
author: anuragm
manager: sivan
ms.service: backup
ms.topic: article
ms.date: 06/18/2019
ms.author: anuragm
ms.openlocfilehash: b2822a3c7dfa23065f2cbd35ef4e506efae026f2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704856"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>使用 Azure 備份來進行疑難排解 SQL Server 資料庫備份

本文章提供在 Azure 虛擬機器上執行的 SQL Server 資料庫的疑難排解資訊。

如需有關備份程序和限制的詳細資訊，請參閱 <<c0> [ 關於 SQL Server 中備份 Azure Vm 中的](backup-azure-sql-database.md#feature-consideration-and-limitations)。

## <a name="sql-server-permissions"></a>SQL Server 權限

若要設定的虛擬機器上的 SQL Server 資料庫的保護，您必須安裝**AzureBackupWindowsWorkload**該虛擬機器上的延伸模組。 如果您收到錯誤**UserErrorSQLNoSysadminMembership**，這表示您的 SQL Server 執行個體沒有必要的備份權限。 若要修正這個錯誤，請遵循[設定 VM 的權限](backup-azure-sql-database.md#set-vm-permissions)。

## <a name="error-messages"></a>錯誤訊息

### <a name="backup-type-unsupported"></a>不支援的備份類型

| Severity | 描述 | 可能的原因 | 建議的動作 |
|---|---|---|---|
| 警告 | 目前的設定，此資料庫不支援特定的備份類型出現在相關聯的原則。 | <li>只有完整資料庫備份作業可以在 master 資料庫上執行。 差異備份和交易記錄備份都不是可行的。 </li> <li>在簡單復原模式中的任何資料庫不允許的交易記錄備份。</li> | 修改資料庫設定，使其支援原則中的所有備份類型。 或者，您也可以變更目前的原則，以納入僅支援的備份類型。 否則，排定的備份期間將略過不支援的備份類型或臨機操作備份的備份作業將會失敗。


### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 此 SQL 資料庫不支援所要求的備份類型。 | 當資料庫復原模式不允許所要求的備份類型時便會發生。 此錯誤會於下列情況時發生： <br/><ul><li>使用簡單復原模式的資料庫不允許記錄備份。</li><li>Master 資料庫不允許差異及記錄備份。</li></ul>如需詳細資訊，請參閱 < [SQL Server 復原模式](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server)文件。 | 如果在簡單復原模式資料庫的記錄檔備份失敗，請嘗試其中一個選項：<ul><li>如果資料庫處於簡單復原模式，請停用記錄備份。</li><li>使用[SQL Server 文件](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server)變更為完整的資料庫復原模式或大量記錄。 </li><li> 如果您不想變更復原模式，而且所用來備份多個資料庫的標準原則無法變更，則請忽略此錯誤。 完整和差異備份會依排程運作。 記錄備份則會略過，這符合此案例的預期。</li></ul>如果它是主要資料庫，並已設定的差異或記錄備份，請使用下列其中一種方式：<ul><li>您可以使用入口網站變更為完整的 master 資料庫中，備份原則排程。</li><li>如果所用來備份多個資料庫的標準原則無法變更，則請忽略此錯誤。 完整備份會依排程運作。 差異或記錄備份則不會進行，這符合此案例的預期。</li></ul> |
| 作業遭到取消，原因是同一個資料庫上已在執行衝突的作業。 | 請參閱[備份和還原的限制相關的部落格項目](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database)同時執行。| [使用 SQL Server Management Studio (SSMS) 來監視備份作業](manage-monitor-sql-database-backup.md)。 衝突的操作失敗之後，重新啟動作業。|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| SQL 資料庫不存在。 | 資料庫已遭到刪除或重新命名。 | 檢查資料庫是否已意外遭到刪除或重新命名。<br/><br/> 如果資料庫意外遭到刪除，卻要繼續備份，請將資料庫還原到原始位置。<br/><br/> 如果您刪除了該資料庫，且不需要未來的備份，然後在 復原服務保存庫中，選取**停止備份**具有**保留備份資料**或是**刪除備份資料**。 如需詳細資訊，請參閱 <<c0> [ 管理並監視備份 SQL Server 資料庫](manage-monitor-sql-database-backup.md)。

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 記錄鏈結中斷。 | 資料庫或 VM 備份到另一個的備份解決方案，會截斷記錄檔鏈結。|<ul><li>檢查是否有其他備份解決方案或指令碼正在使用中。 如果有，請停止其他備份解決方案。 </li><li>如果備份是臨機操作的記錄備份，請觸發完整備份，才能開始新的記錄鏈結。 為排程的記錄備份，因為 Azure 備份服務會自動觸發完整備份以修正此問題不需要採取任何動作。</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| Azure 備份無法連線至 SQL 執行個體。 | Azure 備份無法連線到 SQL Server 執行個體。 | 使用 Azure 入口網站錯誤功能表上的其他詳細資料，以縮小的根本原因。 請參閱 [SQL 備份疑難排解](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine)以修正錯誤。<br/><ul><li>如果預設的 SQL 設定不允許遠端連線，請變更設定。 請參閱下列文章來變更設定的相關資訊：<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>如果有登入問題，請使用下列連結加以修正：<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 此資料來源的第一個完整備份遺失。 | 資料庫的完整備份遺失。 記錄檔和差異備份是完整備份的父代，因此請務必進行完整備份之前觸發差異或記錄備份。 | 觸發臨機操作的完整備份。   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 資料來源的交易記錄已滿，所以無法擷取備份。 | 資料庫的交易記錄空間已滿。 | 若要修正此問題，請參閱[SQL Server 文件](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)。 |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 目標位置上已經有同名的資料庫存在。 | 目標還原目的地已經有相同名稱的資料庫。  | <ul><li>變更目標資料庫名稱。</li><li>或者，您也可以使用 [還原] 頁面上的 [強制] 覆寫選項。</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 還原失敗，原因是無法讓資料庫離線。 | 當您要進行還原時，目標資料庫必須離線。 Azure 備份無法使這項資料離線。 | 使用 Azure 入口網站錯誤功能表上的其他詳細資料，以縮小的根本原因。 如需詳細資訊，請參閱 < [SQL Server 文件](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms)。 |

###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 在目標上找不到具有指紋的伺服器憑證。 | 在目的地執行個體上的 master 資料庫沒有有效的加密憑證指紋。 | 匯入的來源執行個體，目標執行個體上使用有效的憑證指紋。 |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 用於復原的記錄備份包含大量記錄的變更。 根據 SQL 指導方針，其無法用來在任意時間點停止。 | 在大量記錄復原模式資料庫時，就無法復原大量記錄交易與下一個記錄交易之間的資料。 | 選擇不同的點進行復原的時間。 [深入了解](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105))。


### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 無法符合 SQL Always On 可用性群組的備份喜好設定，原因是可用性群組有某些節點並未註冊。 | 執行備份所需的節點未註冊或無法連線。 | <ul><li>確定要執行此資料庫的備份所需的所有節點已註冊且狀況良好，然後重試此作業。</li><li>變更 SQL Server Always On 可用性群組的備份喜好設定。</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| SQL 伺服器 VM 已關閉，無法供 Azure 備份服務存取。 | 關閉 VM。 | 確定 SQL Server 執行個體正在執行。 |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| Azure 備份服務使用 Azure VM 客體代理程式來執行備份，但目標伺服器上無法使用客體代理程式。 | 客體代理程式未啟用，或狀況不良。 | 手動[安裝 VM 客體代理程式](../virtual-machines/extensions/agent-windows.md)。 |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 自動保護用途可能已移除或不再有效。 | 當您啟用自動保護 SQL Server 執行個體上**設定備份**作業會執行該執行個體中的所有資料庫。 如果您停用自動保護，而工作正在執行，則**進行中**工作會取消，並出現此錯誤碼。 | 啟用自動保護一次一次，以協助保護所有剩餘的資料庫。 |

## <a name="re-registration-failures"></a>重新註冊失敗

觸發重新註冊作業之前，先檢查一或多個下列症狀：

* 所有作業 （例如備份、 還原及設定備份） 上具有下列錯誤碼的其中一個 VM 失敗：**WorkloadExtensionNotReachable**， **UserErrorWorkloadExtensionNotInstalled**， **WorkloadExtensionNotPresent**， **WorkloadExtensionDidntDequeueMsg**.
* **備份狀態**區域中備份的項目會顯示**無法連線到**。 排除所有的其他原因，可能會導致相同的狀態：

  * 缺少的權限執行的 VM 上的備份相關作業  
  * 關閉 VM，因此無法進行備份
  * 網路問題  

  !["Not reachable"狀態中重新註冊 VM](./media/backup-azure-sql-database/re-register-vm.png)

* 在 Always On 可用性群組，備份會開始失敗之後變更備份喜好設定，或在容錯移轉之後。

這些徵兆可能會發生一個或多個原因如下：

* 延伸模組已刪除，或從入口網站解除安裝。 
* 延伸模組已從解除安裝**控制台中**底下的 VM 上**解除安裝或變更程式**。
* 在透過就地磁碟還原的時間，已還原 VM。
* VM 已關閉長，因此在其上的擴充功能組態已過期。
* 已刪除 VM，並具有相同名稱和相同的資源群組，為已刪除的 VM 中建立另一個 VM。
* 其中一個可用性群組節點未收到完整的備份設定。 這種情形時的可用性群組已註冊至保存庫，或加入新的節點。

在上述案例中，我們建議您觸發 VM 上的重新註冊作業。 現在，此選項則是只能透過 PowerShell 項目。

## <a name="size-limit-for-files"></a>檔案大小限制

檔案的字串總大小不只上的檔案數目取決也取決於它們的名稱和路徑。 每個資料庫檔案，取得的邏輯檔案名稱和實體路徑。 您可以使用這個 SQL 查詢：

```
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

現在將它們排列成下列格式：

```
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

以下為範例：

```
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

如果內容的字串大小超過 20,000 個位元組，則資料庫檔案，將儲存在以不同的方式。 在復原期間，您無法將還原的目標檔案路徑。 SQL Server 所提供的預設 SQL 路徑，將會還原檔案。

### <a name="override-the-default-target-restore-file-path"></a>覆寫預設目標還原檔案路徑

您可以將 JSON 檔案，其中包含對應到目標還原目錄資料庫檔案的覆寫目標還原檔案路徑在還原作業期間。 建立`database_name.json`檔案，並將它放在位置*C:\Program Files\Azure 工作負載 Backup\bin\plugins\SQL*。

此格式應該是檔案的內容：
```
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

以下為範例：

```
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

在上述的內容中，您可以使用下列 SQL 查詢來取得資料庫檔案的邏輯名稱：

```
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```


應放置這個檔案，再觸發還原作業。

## <a name="next-steps"></a>後續步驟

SQL Server Vm （公開預覽） 的 Azure 備份的相關資訊，請參閱[適用於 SQL Vm 的 Azure 備份](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup)。
