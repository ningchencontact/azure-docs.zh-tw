---
title: 對使用 Azure 備份進行備份的 SQL Server 資料庫進行疑難排解 | Microsoft Docs
description: 適用於在 Azure VM 上執行並使用 Azure 備份進行備份之 SQL Server 資料庫的疑難排解資訊。
services: backup
author: anuragm
manager: shivamg
ms.service: backup
ms.topic: article
ms.date: 06/18/2019
ms.author: anuragm
ms.openlocfilehash: 9ed30a35f30d1b6b9fdcd43110ed93618a10dbc3
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204183"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>針對 Azure 上的 SQL Server 備份進行疑難排解

這篇文章提供疑難排解的資訊，來保護 Azure 上的 SQL Server Vm。

## <a name="feature-consideration-and-limitations"></a>功能考量和限制

若要檢視的功能考量，請參閱本文中，[關於 SQL Server 中備份 Azure Vm 中的](backup-azure-sql-database.md#feature-consideration-and-limitations)。

## <a name="sql-server-permissions"></a>SQL Server 權限

若要針對虛擬機器上的 SQL Server 資料庫設定保護功能，該虛擬機器上就必須安裝 **AzureBackupWindowsWorkload** 擴充功能。 如果您收到 **UserErrorSQLNoSysadminMembership** 錯誤，這表示 SQL 執行個體沒有所需的備份權限。 若要修正這個錯誤，請遵循[設定非 Marketplace SQL VM 的權限](backup-azure-sql-database.md#set-vm-permissions)中的步驟。

## <a name="backup-type-unsupported"></a>不支援的備份類型

| Severity | 描述 | 可能的原因 | 建議的動作 |
|---|---|---|---|
| 警告 | 目前的設定，此資料庫不支援特定類型的備份類型出現在相關聯的原則。 | <li>**Master DB**：只有完整資料庫備份作業可對 master 資料庫中;既不**差異**備份或交易**記錄**的備份。 </li> <li>**簡單復原模式**中的任何資料庫不允許交易**記錄**進行備份。</li> | 修改資料庫設定，使其支援原則中的所有備份類型。 或者，變更目前的原則，僅納入支援的備份類型。 否則，排定的備份期間將略過不支援的備份類型或臨機操作備份的備份作業將會失敗。


## <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 此 SQL 資料庫不支援所要求的備份類型。 | 當資料庫復原模式不允許所要求的備份類型時便會發生。 此錯誤會於下列情況時發生： <br/><ul><li>使用簡單復原模式的資料庫不允許記錄備份。</li><li>master 資料庫不允許差異備份和記錄備份。</li></ul>如需詳細資訊，請參閱 [SQL 復原模式](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server)文件。 | 如果在簡單復原模式下的資料庫記錄備份失敗，請嘗試下列其中一個選項：<ul><li>如果資料庫處於簡單復原模式，請停用記錄備份。</li><li>使用 [SQL 文件](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server)將資料庫復原模式變更為「完整」或「大量記錄」。 </li><li> 如果您不想變更復原模式，而且所用來備份多個資料庫的標準原則無法變更，則請忽略此錯誤。 完整和差異備份會依排程運作。 記錄備份則會略過，這符合此案例的預期。</li></ul>如果是 master 資料庫，而且您已設定差異或記錄備份，請使用下列任何步驟：<ul><li>使用入口網站將 master 資料庫的備份原則排程變更為「完整」。</li><li>如果所用來備份多個資料庫的標準原則無法變更，則請忽略此錯誤。 完整備份會依排程運作。 差異或記錄備份則不會進行，這符合此案例的預期。</li></ul> |
| 作業遭到取消，原因是同一個資料庫上已在執行衝突的作業。 | 請參閱關於同時執行的[備份和還原限制部落格文章](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database)。| [使用 SQL Server Management Studio (SSMS) 來監視備份作業。](manage-monitor-sql-database-backup.md) 衝突的作業失敗後，請重新啟動作業。|

## <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| SQL 資料庫不存在。 | 資料庫已遭到刪除或重新命名。 | 檢查資料庫是否已意外遭到刪除或重新命名。<br/><br/> 如果資料庫意外遭到刪除，卻要繼續備份，請將資料庫還原到原始位置。<br/><br/> 如果您已刪除資料庫，而且之後不再需要備份，則請在復原服務保存庫中，按一下[使用「刪除/保留資料」停止備份](manage-monitor-sql-database-backup.md)。

## <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 記錄鏈結中斷。 | 資料庫或 VM 使用其他備份解決方案來備份，而讓記錄鏈結截斷。|<ul><li>檢查是否有其他備份解決方案或指令碼正在使用中。 如果有，請停止其他備份解決方案。 </li><li>如果備份是臨機操作的記錄備份，請觸發完整備份，才能開始新的記錄鏈結。 若為已排程的記錄備份，因為 Azure 備份服務會自動觸發完整備份進而修正此問題，所以不需要採取任何動作。</li>|

## <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| Azure 備份無法連線至 SQL 執行個體。 | Azure 備份無法連線至 SQL 執行個體。 | 使用 Azure 入口網站錯誤功能表中的其他詳細資料，來縮小根本原因。 請參閱 [SQL 備份疑難排解](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine)以修正錯誤。<br/><ul><li>如果預設的 SQL 設定不允許遠端連線，請變更設定。 請參閱下列文章來變更設定的相關資訊。<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>如果有登入問題，請參閱下列連結來修正問題：<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

## <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 此資料來源的第一個完整備份遺失。 | 資料庫的完整備份遺失。 完整備份是記錄和差異備份的母體，因此必須先擷取完整備份才能觸發差異或記錄備份。 | 觸發臨機操作的完整備份。   |

## <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 資料來源的交易記錄已滿，所以無法擷取備份。 | 資料庫的交易記錄空間已滿。 | 若要修正這個問題，請參閱 [SQL 文件](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)。 |

## <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 目標位置上已經有同名的資料庫存在。 | 目標還原目的地已經有同名資料庫。  | <ul><li>變更目標資料庫名稱</li><li>或者，使用還原頁面中的強制覆寫選項</li> |

## <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 還原失敗，原因是無法讓資料庫離線。 | 執行還原時，必須讓目標資料庫離線。 Azure 備份無法讓這項資料離線。 | 使用 Azure 入口網站錯誤功能表中的其他詳細資料，來縮小根本原因。 如需詳細資訊，請參閱 [SQL 文件](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms)。 |

##  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 在目標上找不到具有指紋的伺服器憑證。 | 目的地執行個體上的 master 資料庫沒有有效的加密指紋。 | 將來源執行個體上所使用的有效憑證指紋，匯入到目標執行個體。 |

## <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 用於復原的記錄備份包含大量記錄的變更。 根據 SQL 指導方針，其無法用來在任意時間點停止。 | 當資料庫在大量記錄的復原模式下時，就無法復原大量記錄交易與下一個記錄交易之間的資料。 | 選擇不同的點進行復原的時間。 [深入了解](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105))


## <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 無法符合 SQL Always On 可用性群組的備份喜好設定，原因是可用性群組有某些節點並未註冊。 | 執行備份所需的節點未註冊或無法連線。 | <ul><li>確定對此資料庫執行備份所需的所有節點均已註冊且狀況良好，然後重試該作業。</li><li>變更 SQL Always On 可用性群組的備份喜好設定。</li></ul> |

## <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| SQL 伺服器 VM 已關閉，無法供 Azure 備份服務存取。 | VM 已關閉 | 確定 SQL 伺服器正在執行。 |

## <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| Azure 備份服務使用 Azure VM 客體代理程式來執行備份，但目標伺服器上無法使用客體代理程式。 | 客體代理程式未啟用，或其狀況不良 | 手動[安裝 VM 客體代理程式](../virtual-machines/extensions/agent-windows.md)。 |

## <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 自動保護用途可能已移除或不再有效。 | 您對於 SQL 執行個體啟用自動保護時，**設定備份**作業便會執行該執行個體中的所有資料庫。 如果您停用自動保護，而工作正在執行，則**進行中**工作會取消，並出現此錯誤碼。 | 再次啟用自動保護可保護其他所有的資料庫。 |

## <a name="re-registration-failures"></a>重新註冊失敗

檢查是否有一或多個[徵狀](#symptoms)觸發重新註冊作業之前。

### <a name="symptoms"></a>徵兆

* 所有的作業，例如備份、 還原及設定備份的 VM 使用的其中一個下列的錯誤代碼上失敗：**WorkloadExtensionNotReachable**， **UserErrorWorkloadExtensionNotInstalled**， **WorkloadExtensionNotPresent**， **WorkloadExtensionDidntDequeueMsg**
* **備份狀態**項目會顯示備份**無法連線到**。 雖然您必須排除所有其他原因，可能也會導致相同的狀態：

  * 因為缺少權限，才能執行備份的相關的作業，在 VM 上  
  * VM 已關閉導致無法進行備份
  * 網路問題  

    ![重新註冊 VM](./media/backup-azure-sql-database/re-register-vm.png)

* 如果 alwayson 可用性群組，備份會開始失敗之後變更備份喜好設定，或發生容錯移轉

### <a name="causes"></a>原因
這些徵兆可能會因為一或多個原因如下：

  * 延伸模組已被刪除，或從入口網站解除安裝 
  * 延伸模組已從解除安裝**控制台中**下方的 vm**解除安裝或變更程式**UI
  * VM 已在使用就地磁碟還原的時間還原
  * 長導致過期延伸模組組態，它已關閉 VM
  * 已刪除 VM 和具有相同名稱和相同的資源群組，為已刪除的 VM 中建立另一個 VM
  * 其中一個 AG 節點未收到完整的備份設定，這可能是在可用性群組註冊至保存庫時，或新增新的節點取得  <br>
    在上述案例中，建議您使用觸發重新註冊在 VM 上的作業。 這個選項才可透過 PowerShell，並即將推出在 Azure 入口網站中。

## <a name="files-size-limit-beyond-which-restore-happens-to-default-path"></a>預設路徑剛好超過的還原檔案的大小限制

上的檔案數目還會依據其名稱和路徑，不只取決於檔案的字串總大小。 針對每個資料庫檔案，取得的邏輯檔案名稱和實體路徑。
您可以使用下面所列的 SQL 查詢：

  ```
  SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
                 INNER JOIN sys.databases db ON db.database_id = mf.database_id
                 WHERE db.name = N'<Database Name>'"
 ```

現在將它們排列成下面所列的格式：

  ```[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
  ```

範例：

  ```[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
  ```

如果上述內容的字串大小超過 20,000 個位元組，則資料庫檔案會以不同的方式儲存，並在復原期間，您將無法將還原的目標檔案路徑。 檔案將會還原至 SQL Server 所提供的預設 SQL 路徑。

### <a name="override-the-default-target-restore-file-path"></a>覆寫預設目標還原檔案路徑

您可以將 JSON 檔案，其中包含要還原的目標路徑的資料庫檔案的對應覆寫目標還原檔案路徑在還原作業期間。 這個建立的檔案`database_name.json`並將它放在位置*C:\Program Files\Azure 工作負載 Backup\bin\plugins\SQL*。

下面所列應該是格式的檔案的內容：
  ```[
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

範例：

  ```[
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

 
在上述的內容中，您可以取得使用下面所列的 SQL 查詢的資料庫檔案的邏輯名稱：

```
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```


應放置這個檔案，再觸發還原作業。

## <a name="next-steps"></a>後續步驟

如需 SQL Server VM 的 Azure 備份 (公開預覽) 詳細資訊，請參閱 [SQL VM 的 Azure 備份 (公開預覽)](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup)。
