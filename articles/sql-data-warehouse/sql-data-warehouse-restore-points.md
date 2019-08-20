---
title: 使用者定義的還原點 |Microsoft Docs
description: 如何 Azure SQL 資料倉儲建立還原點。
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 9dcb8b2e9c1b75aac3c195f89777ac9c6eb030d7
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575258"
---
# <a name="user-defined-restore-points"></a>使用者定義的還原點

在本文中, 您將瞭解如何使用 PowerShell 和 Azure 入口網站為 Azure SQL 資料倉儲建立新的使用者定義還原點。

## <a name="create-user-defined-restore-points-through-powershell"></a>透過 PowerShell 建立使用者定義的還原點

若要建立使用者定義的還原點, 請使用[AzSqlDatabaseRestorePoint][New-AzSqlDatabaseRestorePoint] PowerShell Cmdlet。

1. 開始之前, 請務必[安裝 Azure PowerShell][Install Azure PowerShell]。
2. 開啟 PowerShell。
3. 連接到您的 Azure 帳戶，然後列出與您帳戶關聯的所有訂用帳戶。
4. 選取包含要還原之資料庫的訂用帳戶。
5. 建立資料倉儲立即複本的還原點。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. 查看所有現有還原點的清單。

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>透過 Azure 入口網站建立使用者定義的還原點

使用者定義的還原點也可以透過 Azure 入口網站建立。

1. 登入您的[Azure 入口網站][Azure portal]帳戶。

2. 流覽至您想要為其建立還原點的 SQL 資料倉儲。

3. 從左窗格中選取 **[總覽**], 然後選取 [ **+ 新增還原點**]。 如果 [新增還原點] 按鈕未啟用, 請確定資料倉儲並未暫停。

    ![新增還原點](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. 指定使用者定義還原點的名稱, 然後按一下 [套用]。 使用者定義的還原點預設保留期間為七天。

    ![還原點名稱](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>後續步驟

- [還原現有的資料倉儲][Restore an existing data warehouse]
- [還原已刪除的資料倉儲][Restore a deleted data warehouse]
- [從異地備份資料倉儲還原][Restore from a geo-backup data warehouse]

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[PowerShelldoc]:./sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-powershell
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md
<!--MSDN references-->
[New-AzSqlDatabaseRestorePoint]: https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
