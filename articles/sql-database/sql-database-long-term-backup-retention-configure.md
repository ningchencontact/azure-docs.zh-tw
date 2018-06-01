---
title: 管理 Azure SQL Database 長期備份保留 | Microsoft Docs
description: 了解如何在 SQL Azure 儲存體中儲存自動備份，然後將它們還原
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: df3d843516bce30253c23080716e606dfb56f25e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211556"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>管理 Azure SQL Database 長期備份保留

您可以使用[長期備份保留](sql-database-long-term-retention.md) 原則 (LTR) 來設定 Azure SQL Database，以在 Azure blob 儲存體中自動保留最多 10 年的備份。 然後，您可以使用 Azure 入口網站或 PowerShell 來復原資料庫。

> [!NOTE]
> 在 2016 年 10 月這項功能的初始預覽版本中，備份已儲存在 Azure 復原服務保存庫中。 此更新會移除此相依性，但是為了回溯相容性，原始 API 會支援到 2018 年 5 月 31 日。 如果您需要與 Azure 復原服務保存庫中的備份互動，請參閱[使用 Azure 復原服務保存庫的長期備份保留](sql-database-long-term-backup-retention-configure-vault.md)。 

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>使用 Azure 入口網站來設定長期保留原則和還原備份

下列各節說明如何使用 Azure 入口網站來設定長期保留、檢視長期保留的備份，以及從長期保留還原備份。

### <a name="configure-long-term-retention-policies"></a>設定長期保留原則

您可以將 Azure SQL Database 設定為[保留自動備份](sql-database-long-term-retention.md)的期間比您服務層的保留期限還要長。 

1. 在 Azure 入口網站中，選取您的 SQL Server，然後按一下 [長期備份保留]。

   ![長期備份保留連結](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. 在 [設定原則] 索引標籤上，選取您要設定或修改長期備份保留原則的資料庫。

   ![選取資料庫](./media/sql-database-long-term-retention/ltr-configure-select-database.png)

3. 在 [設定原則] 窗格中，選取您想要保留每週、每月或每年備份並指定各項的保留期限。 

   ![設定原則](./media/sql-database-long-term-retention/ltr-configure-policies.png)

4. 完成時，按一下 [套用]。

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>使用 Azure 入口網站檢視備份及從備份還原

檢視使用 LTR 原則針對特定資料庫保留的備份，然後從這些備份還原。 

1. 在 Azure 入口網站中，選取您的 SQL Server，然後按一下 [長期備份保留]。

   ![長期備份保留連結](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. 在 [可用備份] 索引標籤上，選取您要查看可用備份的資料庫。

   ![選取資料庫](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. 在 [可用備份] 窗格中，檢閱可用的備份。 

   ![檢視備份](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. 選取您要還原的備份，然後指定新的資料庫名稱。

   ![還原](./media/sql-database-long-term-retention/ltr-restore.png)

5. 按一下 [確定]，將您的資料庫從 Azure SQL 儲存體中的備份還原到新的資料庫。

6. 在工具列上，按一下 [通知] 圖示以檢視還原作業的狀態。

   ![從保存庫還原作業進度](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. 完成還原作業之後，開啟 [SQL Database] 頁面，以檢視剛還原的資料庫。

> [!NOTE]
> 從這裡開始，您可以使用 SQL Server Management Studio 連接到已還原的資料庫來執行所需的工作，例如[從還原的資料庫擷取一堆資料來複製到現有的資料庫，或刪除現有的資料庫，並將還原的資料庫重新命名為現有的資料庫名稱](sql-database-recovery-using-backups.md#point-in-time-restore)。
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>使用 PowerShell 來設定長期保留原則和還原備份

下列各節說明如何使用 PowerShell 來設定長期備份保留、檢視 Azure SQL 儲存體中的備份，以及從 Azure SQL 儲存體中的備份還原。

> [!IMPORTANT]
> 您必須使用最新的 AzureRM PowerShell 來設定 LTR V2 原則。 目前的版本是 [AzureRM 4.5.0-preview](https://www.powershellgallery.com/packages/AzureRM.Sql/4.5.0-preview)，這是預覽版本，因此請使用下列命令來安裝它：`Install-Module -Name AzureRM.Sql -AllowPrerelease -Force`。
> 如需安裝發行前版本的指導方針，請參閱[取得 PowerShellGet 模組](https://docs.microsoft.com/en-us/powershell/gallery/installing-psget)。 AzureRM PowerShell 2018 年 5 月版本將在幾天後發行 (預計是 2018/5/18)，在安裝已可供使用的發行版本時，您可以忽略 -AllowPrelease 參數，並使用下列命令 `Install-Module -Name AzureRM.Sql -Force`。

### <a name="create-an-ltr-policy"></a>建立 LTR 原則

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $subId

# get the server
$server = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetetion = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>檢視 LTR 原則
此範例示範如何列出伺服器內的 LTR 原則

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzureRmSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzureRmSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>清除 LTR 原則
此範例示範如何清除資料庫中的 LTR 原則

```powershell
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>檢視 LTR 備份

此範例示範如何列出伺服器內的 LTR 備份。 

```powershell
# Get the list of all LTR backups in a specific Azure region 
# The backups are grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest
# backup first.  
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location 

# Get the list of LTR backups from the Azure region under 
# the named server. 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName

# Get the LTR backups for a specific database from the Azure region under the named server 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>刪除 LTR 備份

此範例示範如何刪除備份清單中的 LTR 備份。

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzureRmSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

### <a name="restore-from-ltr-backups"></a>從 LTR 備份還原
此範例示範如何從 LTR 備份還原。 請注意，這個介面並未變更，但是資源識別碼參數現在需要 LTR 備份資源識別碼。 

```powershell
# Restore LTR backup as an S3 database
Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName S3
```

> [!NOTE]
> 從這裡開始，您可以使用 SQL Server Management Studio 連線到已還原的資料庫來執行所需的工作，例如從還原的資料庫擷取一堆資料來複製到現有的資料庫，或刪除現有的資料庫，並將還原的資料庫重新命名為現有的資料庫名稱。 請參閱[還原時間點](sql-database-recovery-using-backups.md#point-in-time-restore)。

## <a name="next-steps"></a>後續步驟

- 若要深入了解服務產生的自動備份，請參閱[自動備份](sql-database-automated-backups.md)
- 若要深入了解長期備份保留，請參閱[長期備份保留](sql-database-long-term-retention.md)
