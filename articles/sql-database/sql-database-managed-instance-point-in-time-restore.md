---
title: SQL Database 受控實例-時間點還原 |Microsoft Docs
description: 如何將 SQL 受控實例中的資料庫還原至先前的時間點。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 67f13d16dcf6bbe4fa13fe3a6e78d3e4d61e1999
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862131"
---
# <a name="restore-a-sql-managed-instance-database-to-a-previous-point-in-time"></a>將 SQL 受控實例資料庫還原至先前的時間點

時間點還原（PITR）可讓您在過去某個時間點，將資料庫建立為另一個資料庫的複本。 本文說明如何在受控實例中執行資料庫的時間點還原。

時間點還原可用於復原案例，例如因錯誤而造成的事件、不正確載入的資料、刪除重要的資料，以及其他問題，以及僅供測試或審核之用。 根據您的資料庫設定，備份檔案會保留一段7到35天的時間。

時間點還原可用於：

- 從現有的資料庫還原資料庫。
- 從已刪除的資料庫還原資料庫。

此外，使用受控實例時，可以使用時間點還原來執行下列動作： 

- 將資料庫還原到相同的受控實例。
- 將資料庫還原到另一個受控實例。


> [!NOTE]
> 不可能進行整個受控實例的時間點還原。 這是可行的，而且在本文中說明的是裝載于受控實例上之資料庫的時間點還原。


## <a name="limitations"></a>限制

還原至另一個受控實例時，這兩個實例必須位於相同的訂用帳戶和區域中。 目前不支援跨區域和跨訂用帳戶的還原。

> [!WARNING]
> 請小心使用受控實例的儲存體大小–視還原資料的大小而定，您可能會用盡實例儲存體。 如果沒有足夠的空間可用於還原的資料，請使用替代的方法。

下表顯示受控實例的時間點復原案例：

|           |還原現有的資料庫| 還原現有的資料庫|還原已卸載的資料庫| 還原已卸載的資料庫|
|:----------|:----------|:----------|:----------|:----------|
|目的地| 相同 MI|另一個 MI |相同 MI|另一個 MI |
|Azure 入口網站| 是|否 |否|否|
|Azure CLI|是 |是 |否|否|
|PowerShell| 是|是 |是|是|


## <a name="restore-existing-database"></a>還原現有的資料庫

使用 Azure 入口網站、Powershell 或 Azure CLI，將現有的資料庫還原至相同的實例。 藉由指定目標受控實例和資源群組屬性，使用 Powershell 或 Azure CLI，將資料庫還原至另一個實例。 如果未指定這些參數，則預設會將資料庫還原至現有的實例。 目前不支援透過 Azure 入口網站還原至另一個實例。 

# <a name="portaltabazure-portal"></a>[入口網站](#tab/azure-portal)

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 流覽至您的受控實例，然後選取您想要還原的資料庫。 
1. 在 [資料庫] 頁面上選取 [**還原**]。 

    ![還原現有的資料庫](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

1. 在 [**還原**] 頁面上，選取您想要將資料庫還原至哪一個日期和時間點的歷程記錄。
1. 選取 [**確認**] 以還原您的資料庫。 這會啟動還原程式，這會建立新的資料庫，並在所需的時間點，填入來自原始資料庫的資料。 如需復原程式的詳細資訊，請參閱復原[時間](sql-database-recovery-using-backups.md#recovery-time)。 

1. 尋找受控實例
1. 選取您想要還原的資料庫
1. 在 [資料庫] 畫面上，按一下 [還原動作]
1. 在 [還原] 畫面上，選取您要還原資料庫之歷程記錄中的 [日期和時間]
1. 確認之後，將會啟動還原程式，而且視資料庫的大小而定，將會在所需的時間點，建立新的資料庫並填入來自原始資料庫的資料。 在還原程式期間，使用備份來檢查復原一文。


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

如果您尚未安裝 Azure PowerShell，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。

若要使用 PowerShell 還原資料庫，請將參數更新為您的值，然後執行下列命令：

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"
 
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId
 
Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                               -InstanceName $managedInstanceName `
                               -Name $databaseName `
                               -PointInTime $pointInTime `
                               -TargetInstanceDatabaseName $targetDatabase `
```

若要將資料庫還原到另一個受控實例，請設定目標資源組名和目標受控實例名稱。  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

如需詳細資訊，請參閱[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)。


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

如果您尚未安裝 Azure CLI，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

若要使用 Azure CLI 來還原資料庫，請將參數更新為您的值，然後執行下列命令：


```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```


若要將資料庫還原到另一個受控實例，請設定目標資源組名和目標受控實例名稱。  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

如需可用參數的詳細說明，請參閱[受控實例 CLI](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)。 

---

## <a name="restore-a-deleted-database"></a>還原已刪除的資料庫 
 
還原已刪除的資料庫只能使用 PowerShell 來完成。 資料庫可以還原至相同的實例或另一個實例。 

若要使用 PowerShell 還原已刪除的資料庫，請將參數更新為您的值，然後執行下列命令：

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"

$deleted_db = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
            -InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName 

$pointInTime = "2018-06-27T08:51:39.3882806Z"
$properties = New-Object System.Object
$properties | Add-Member -type NoteProperty -name CreateMode -Value "PointInTimeRestore"
$properties | Add-Member -type NoteProperty -name RestorePointInTime -Value $pointInTime
$properties | Add-Member -type NoteProperty -name RestorableDroppedDatabaseId -Value $deleted_db.Id
```

若要將已刪除的資料庫還原到另一個實例，請變更資源組名和受控實例名稱。

Location 參數應符合資源群組和受控實例的位置。

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-existing-database"></a>覆寫現有的資料庫 
 
若要覆寫現有的資料庫，您還必須：

1. 卸載您想要覆寫的現有資料庫。
1. 將時間點還原的資料庫重新命名為已卸載之資料庫的名稱。 


### <a name="drop-original-database"></a>卸載原始資料庫 
 
您可以使用 Azure 入口網站、PowerShell 或 Azure CLI 來卸載資料庫。 

您也可以藉由直接連接到受控實例、啟動 SQL Server Management Studio （SSMS）並執行下列 Transact-sql （T-sql）命令來卸載資料庫。

```sql
DROP DATABASE WorldWideImporters;
```

使用下列其中一種方法來連線到您的受控實例資料庫： 

- [SQL 虛擬機器](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [點對站](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [公用端點](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[入口網站](#tab/azure-portal)

在 Azure 入口網站中，選取受控實例中的資料庫，然後選取 **刪除**。

   ![還原現有的資料庫](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用下列 PowerShell 命令，從受控實例中卸載現有的資料庫： 

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

使用下列 Azure CLI 命令，從受控實例中卸載現有的資料庫： 

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---


### <a name="alter-new-database-name-to-original"></a>將新的資料庫名稱變更為原始

直接連接到受控實例，啟動 SQL Server Management Studio，然後執行下列 Transact-sql （T-sql）查詢，將還原的資料庫名稱變更為您想要覆寫之已卸載資料庫的名稱。 


```sql
ALTER WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```


使用下列其中一種方法來連線到您的受控實例資料庫： 

- [SQL 虛擬機器](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [點對站](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [公用端點](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>後續步驟

深入瞭解[長期保留](sql-database-long-term-retention.md)和[自動備份](sql-database-automated-backups.md)。 
