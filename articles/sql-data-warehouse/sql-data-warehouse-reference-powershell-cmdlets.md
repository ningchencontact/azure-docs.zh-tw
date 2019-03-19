---
title: Azure SQL 資料倉儲的 PowerShell Cmdlet
description: 尋找 Azure SQL 資料倉儲的前幾個 PowerShell Cmdlet，包括如何暫停和繼續資料庫。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 0310a74986f86e072e3ee0fa81df36a03d2e5117
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2019
ms.locfileid: "57409936"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>適用於 SQL 資料倉儲的 PowerShell Cmdlet 和 REST API
您可使用 Azure PowerShell Cmdlet 或 REST API 管理許多 SQL 資料倉儲系統管理工作。  下面是如何使用 PowerShell 命令自动执行 SQL 数据仓库中的常见任务的一些示例。  如需一些良好的 REST 範例，請參閱[使用 REST 管理延展性][Manage scalability with REST]一文。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>開始使用 Azure PowerShell Cmdlet
1. 開啟 Windows PowerShell。
2. 在 PowerShell 提示中，執行下列命令來登入 Azure Resource Manager，並選取您的訂用帳戶。
   
    ```PowerShell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>暫停 SQL 資料倉儲範例
暫停 "Server01" 伺服器上託管的 "Database02" 資料庫。  此伺服器位於「ResourceGroup1」這個 Azure 資源群組。

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
一種變化中，此範例中擷取的物件輸送到[暫止 AzSqlDatabase][Suspend-AzSqlDatabase]。  結果就是暫停資料庫。 最終的命令會顯示結果。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>啟動 SQL 資料倉儲範例
繼續 "Server01" 伺服器上託管之 "Database02" 資料庫的作業。 此伺服器包含在「ResourceGroup1」這個資源群組中。

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

一種變化，此範例會從 "ResourceGroup1" 資源群組包含的 "Server01" 伺服器中，擷取 "Database02" 資料庫。 它使用管線傳送要擷取的物件[Resume AzSqlDatabase][Resume-AzSqlDatabase]。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> 請注意，如果您的伺服器是 foo.database.windows.net，請使用 "foo" 作為 PowerShell Cmdlet 中的 -ServerName。
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>其他支援的 PowerShell Cmdlet
這些 PowerShell Cmdlet 皆由 Azure SQL 資料倉儲所支援。

* [Get-AzSqlDatabase][Get-AzSqlDatabase]
* [Get-AzSqlDeletedDatabaseBackup][Get-AzSqlDeletedDatabaseBackup]
* [Get-AzSqlDatabaseRestorePoints][Get-AzSqlDatabaseRestorePoints]
* [New-AzSqlDatabase][New-AzSqlDatabase]
* [Remove-AzSqlDatabase][Remove-AzSqlDatabase]
* [Restore-AzSqlDatabase][Restore-AzSqlDatabase]
* [Resume-AzSqlDatabase][Resume-AzSqlDatabase]
* [Select-AzSubscription][Select-AzSubscription]
* [Set-AzSqlDatabase][Set-AzSqlDatabase]
* [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase]

## <a name="next-steps"></a>後續步驟
有关更多的 PowerShell 示例，请参阅：

* [使用 PowerShell 建立 SQL 資料倉儲][Create a SQL Data Warehouse using PowerShell]
* [資料庫還原][Database restore]

如需可以使用 PowerShell 進行自動化的其他工作，請參閱 [Azure SQL Database Cmdlet][Azure SQL Database Cmdlets]。 請注意，Azure SQL 資料倉儲並沒有支援所有的 Azure SQL Database Cmdlet。  如需可以使用 REST 來自動化的工作清單，請參閱 [Azure SQL Database 的作業][Operations for Azure SQL Database]。

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./create-data-warehouse-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.sql
[Operations for Azure SQL Database]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase
[Get-AzSqlDeletedDatabaseBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup
[Get-AzSqlDatabaseRestorePoints]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoints
[New-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase
[Remove-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Resume-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase
<!-- It appears that Select-AzSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase
[Suspend-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
