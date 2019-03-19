---
title: 還原 Azure SQL 資料倉儲 (PowerShell) | Microsoft Docs
description: 還原 Azure SQL 資料倉儲的 PowerShell 工作。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6324eb11b334fd6a00e30d6f2fc6d1bec3f7a82c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57870818"
---
# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>還原 Azure SQL 資料倉儲 (PowerShell)
> [!div class="op_single_selector"]
> * [概觀][Overview]
> * [入口網站][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

在本文中，您將了解如何使用 PowerShell 來還原 Azure SQL 資料倉儲。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**請驗證您的 DTU 容量。**  每個 SQL 資料倉儲均由具有預設 DTU 配額的 SQL 伺服器裝載 (例如 myserver.database.windows.net)。  在您還原 SQL 資料倉儲之前，請確認您的 SQL 伺服器有足夠的剩餘 DTU 配額供要還原的資料庫使用。 若要了解如何計算所需 DTU 或要求更多 DTU，請參閱[要求 DTU 配額變更][Request a DTU quota change]。

### <a name="install-powershell"></a>安裝 PowerShell
若要使用 Azure PowerShell 與 SQL 資料倉儲，您必須安裝 Azure PowerShell。  您可以執行，以檢查您的版本**Get-module-ListAvailable-名稱 Az**。  如需安裝最新版本的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell][How to install and configure Azure PowerShell]。

## <a name="restore-an-active-or-paused-database"></a>還原作用中或已暫停的資料庫
若要從快照集使用還原資料庫[還原 AzSqlDatabase] [ Restore-AzSqlDatabase] PowerShell cmdlet。

1. 開啟 Windows PowerShell。
2. 連接到您的 Azure 帳戶，然後列出與您帳戶關聯的所有訂用帳戶。
3. 选择包含要还原的数据库的订阅。
4. 列出資料庫的還原點。
5. 使用 RestorePointCreationDate 选取所需的还原点。
6. 將資料庫還原到想要的還原點。
7. 確認還原的資料庫在線上。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> 完成还原后，即可按[在恢复后配置数据库][Configure your database after recovery]中的说明配置恢复的数据库。
> 
> 

## <a name="restore-a-deleted-database"></a>還原已刪除的資料庫
若要還原已刪除的資料庫，請使用[還原 AzSqlDatabase] [ Restore-AzSqlDatabase] cmdlet。

1. 開啟 Windows PowerShell。
2. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。
3. 選取包含要還原之已刪除資料庫的訂用帳戶。
4. 取得已刪除的特定資料庫。
5. 還原已刪除的資料庫。
6. 確認還原的資料庫在線上。

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> 還原完成後，您可以遵循[在復原之後設定資料庫][Configure your database after recovery]來設定復原的資料庫。
> 
> 

## <a name="restore-from-an-azure-geographical-region"></a>從 Azure 地理區域還原
若要復原的資料庫，使用[還原 AzSqlDatabase] [ Restore-AzSqlDatabase] cmdlet。

> [!NOTE]
> 您可以執行異地還原來還原至「針對計算最佳化」效能層級！ 若要這麼做，請指定一個「針對計算最佳化」ServiceObjectiveName 作為選擇性參數。 
>
> 

1. 開啟 Windows PowerShell。
2. 連接到您的 Azure 帳戶，然後列出與您帳戶關聯的所有訂用帳戶。
3. 選取包含要還原之資料庫的訂用帳戶。
4. 取得您想要復原的資料庫。
5. 创建对数据库的恢复请求。
6. 验证异地还原的数据库的状态。

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> 若要在完成还原后配置数据库，请参阅[在恢复后配置数据库][Configure your database after recovery]。
> 
> 

如果來源資料庫是啟用 TDE，則復原的資料庫將是啟用 TDE。

## <a name="next-steps"></a>後續步驟
若要深入了解 Azure SQL Database 版本的商務持續性功能，請閱讀 [Azure SQL Database 商務持續性概觀][Azure SQL Database business continuity overview]。

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
