---
title: 在 Azure VM 上管理和監視由 Azure 備份進行備份的 SQL Server 資料庫 | Microsoft Docs
description: 本文說明如何還原在 Azure VM 上執行且使用 Azure 備份進行備份的 SQL Server 資料庫
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 1c2ce0ba42f0bc3efd1dcc951113b05ab6941b98
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430952"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>管理和監視備份的 SQL Server 資料庫 


本文說明管理和監視 Azure VM 上執行的 SQL Server 資料庫的常見工作，透過 [Azure 備份](backup-overview.md)服務將其備份至 Azure 備份復原服務保存庫。 工作包括監視作業和警示、停止和繼續資料庫保護、執行備份作業，以及從備份取消註冊 VM。


> [!NOTE]
> 針對在 Azure VM 上執行的 SQl Server 資料庫使用 Azure 備份進行備份，目前處於公開預覽狀態。


如果您尚未針對 SQL Server 資料庫設定備份，請遵循[此文章](backup-azure-sql-database.md)的指示進行。

## <a name="monitor-backup-jobs"></a>監視備份作業

###  <a name="monitor-ad-hoc-jobs-in-the-portal"></a>監視入口網站中的臨機操作工作

Azure 備份會在**備份作業**入口網站中顯示所有的手動觸發工作，包括探索及註冊資料庫，以及備份和還原作業。

![備份作業入口網站](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> **備份作業**入口網站不會顯示已排程的備份工作。 請使用 SQL Server Management Studio 來監視已排程的備份作業，如下一節中所述。
>

### <a name="monitor-backup-jobs-with-sql-server-management-studio"></a>使用 SQL Server Management Studio 監視備份作業 

Azure 備份使用 SQL 原生 API 來執行所有備份作業。

使用原生 API 從 msdb 資料庫中的 [SQL backupset 資料表](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017)擷取所有作業資訊。

下列查詢範例會針對名稱為 **DB1** 的資料庫，擷取其所有備份作業。 自訂查詢以進行進階監視。

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  

```

## <a name="view-backup-alerts"></a>檢視備份警示

記錄備份會每隔 15 分鐘出現一次，因此監視備份作業可能會很繁瑣。 Azure 備份利用電子郵件警示以便於監視。

- 所有備份失敗都會觸發警示。
- 這些警示會在資料庫層級依錯誤碼合併。
- 資料庫第一次備份失敗時，才會傳送電子郵件警示。 

若要監視備份警示：

1. 在 [Azure 入口網站](https://portal.azure.com)中登入您的 Azure 訂用帳戶以監視資料庫警示。

2. 在保存庫儀表板上，選取 [警示與事件]。

   ![選取 [警示和事件]](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. 在 [警示和事件] 中，選取 [備份警示]。

   ![選取 [備份警示]](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>停止保護 SQL Server 資料庫

您可以透過數種方式停止備份 SQL Server 資料庫：

* 停止所有未來的備份作業並刪除所有復原點。
* 停止所有未來的備份作業但保留復原點不變。

請注意：

如果您保留復原點，會根據備份原則清除復原點。 清除所有復原點之前，您受保護的執行個體和已取用的儲存體會產生費用。 [深入了解](https://azure.microsoft.com/pricing/details/backup/)定價。
- 當您將復原點保留不變時，雖然復原點會根據保留原則到期，但是在您明確地刪除備份資料之前，Azure 備份一律會保留最後一個復原點。
- 如果您刪除資料來源，但沒有停止備份，新的備份則會開始失敗。 同樣地，舊的復原點會根據原則到期，但在您停止備份並刪除資料之前，最後一個復原點一律會保留。
- 除非您停用自動保護，否則無法停止已啟用自動保護的資料庫備份。

若要停止保護資料庫：

1. 在保存庫儀表板中的 [使用量] 下，選取 [備份項目]。

    ![開啟 [備份項目] 功能表](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png)上也提供本文中使用的原始碼。

2. 在 [備份管理類型] 中，選取 **Azure VM 中的 SQL**。

    ![選取 Azure VM 中的 SQL](./media/backup-azure-sql-database/sql-restore-backup-items.png)


3. 選取您要停止保護的資料庫。

    ![選取要停止保護的資料庫](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)


5. 在資料庫功能表上，選取 [停止備份]。

    ![選取 [停止備份]](./media/backup-azure-sql-database/stop-db-button.png)


6. 在 [停止備份] 功能表中，選取是否要保留或刪除資料。 選擇是否提供原因或註解。

    ![[停止備份] 功能表](./media/backup-azure-sql-database/stop-backup-button.png)

7. 按一下 [停止備份]。

  

### <a name="resume-protection-for-a-sql-database"></a>繼續保護 SQL 資料庫

如果在停止保護 SQL 資料庫時選取 [保留備份資料] 選項，則可以繼續保護。 如果未保留備份資料，則無法繼續保護。

1. 若要繼續保護 SQL 資料庫，請開啟備份項目並選取 [繼續備份]。

    ![選取 [繼續備份] 以繼續保護資料庫](./media/backup-azure-sql-database/resume-backup-button.png)

2. 在 [備份原則] 功能表上選取原則，然後選取 [儲存]。

## <a name="run-an-on-demand-backup"></a>執行隨選備份

您可以執行不同類型的隨選備份：

* 完整備份
* 僅複製完整備份
* 差異備份
* 記錄備份

[深入了解](backup-architecture.md#sql-server-backup-types) SQL Server 備份類型。

## <a name="unregister-a-sql-server-instance"></a>將 SQL Server 執行個體取消註冊

在停用保護之後但在刪除保存庫之前，將 SQL Server 執行個體取消註冊：

1. 在保存庫儀表板上，選取 [管理] 下的 [備份基礎結構]。  

   ![選取 [備份基礎結構]](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. 在 [管理伺服器] 底下，選取 [受保護的伺服器]。

   ![選取 [受保護的伺服器]](./media/backup-azure-sql-database/protected-servers.png)


3. 在 [受保護的伺服器] 中，選取要取消註冊的伺服器。 若要刪除保存庫，您必須將所有伺服器取消註冊。

4. 以滑鼠右鍵按一下 [受保護的伺服器] > [刪除]。

   ![選取 [刪除]](./media/backup-azure-sql-database/delete-protected-server.png)


## <a name="next-steps"></a>後續步驟

[檢閱](backup-sql-server-azure-troubleshoot.md)為 SQL Server 資料庫備份的疑難排解資訊。
