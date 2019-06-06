---
title: Azure 備份：備份和還原 SQL Database 中使用 Azure 備份和 PowerShell 的 Azure Vm
description: 備份和還原 SQL 資料庫，在使用 Azure 備份和 PowerShell 的 Azure Vm 中。
services: backup
author: pvrk
manager: vijayts
keywords: Azure 備份;SQL;
ms.service: backup
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pullabhk
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 6a2e065466ab4426a6472b64fae19d264ff8dd81
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734231"
---
# <a name="back-up-and-restore-sql-databases-in-azure--vms-with-powershell"></a>備份和還原 SQL Database 中使用 PowerShell 的 Azure Vm

這篇文章說明如何使用 Azure PowerShell 來備份和復原 Azure VM 使用的 SQL DB [Azure 備份](backup-overview.md)復原服務保存庫。

本教學課程說明如何：

> [!div class="checklist"]
> * 設定 PowerShell，並註冊 Azure 復原服務提供者。
> * 建立復原服務保存庫。
> * 在 Azure VM 中的 SQL 資料庫的設定備份。
> * 執行備份作業。
> * 還原備份 SQL DB。
> * 監視備份與還原作業。

## <a name="before-you-start"></a>開始之前

* [了解更多](backup-azure-recovery-services-vault-overview.md)有關復原服務保存庫。
* 了解的功能[備份 Azure Vm 中的 SQL Db](backup-azure-sql-database.md#before-you-start)。
* 檢閱復原服務的 PowerShell 物件階層。

### <a name="recovery-services-object-hierarchy"></a>復原服務物件階層

下圖摘要說明物件階層架構。

![復原服務物件階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

檢閱**Az.RecoveryServices** [指令程式參考](/powershell/module/az.recoveryservices)Azure 文件庫中的參考。

### <a name="set-up-and-install"></a>設定和安裝

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

設定 PowerShell，如下所示：

1. [下載最新版的 Az PowerShell](/powershell/azure/install-az-ps)。 所需的最低版本是 1.5.0。

2. 尋找 Azure 備份 PowerShell cmdlet，使用下列命令：

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. 檢閱 Azure 備份和復原服務保存庫的別名和 cmdlet。 以下是您可能會看到的範例。 它不是 cmdlet 的完整清單。

    ![復原服務 Cmdlet 清單](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. 登入 Azure 帳戶**Connect AzAccount**。
5. 在網頁上出現，系統會提示您輸入您的帳戶認證。

    * 或者，您可以做為參數中包含您的帳戶認證**Connect AzAccount** cmdlet 搭配 **-認證**。
    * 如果您是租用戶的 CSP 合作夥伴，指定客戶的租用戶中，然後再使用其 tenantID 或租用戶的主要網域名稱。 例如 **Connect-AzAccount -Tenant** fabrikam.com。

6. 建立您想要使用的帳戶，因為一個帳戶可以有多個訂用的訂用帳戶的關聯。

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. 如果您是第一次使用 Azure 備份，請使用 **Register-AzResourceProvider** Cmdlet 利用您的訂用帳戶來註冊 Azure 復原服務提供者。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. 確認提供者已成功註冊：

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. 在命令輸出中，確認**RegistrationState**變更為**已註冊**。 如果沒有，請執行**註冊 AzResourceProvider** cmdlet 一次。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

依照下列步驟建立復原服務保存庫。

復原服務保存庫是一項 Resource Manager 資源，因此您必須將它放在資源群組內。 您可以使用現有的資源群組，或使用 **New-AzResourceGroup** Cmdlet 建立資源群組。 建立資源群組時，請指定資源群組的名稱與位置。

1. 保存庫位於資源群組。 如果您沒有現有的資源群組中，建立新的[新增 AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0)。 在此範例中，我們會建立新的資源群組，在美國西部區域中。

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. 使用[新增 AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) cmdlet 來建立保存庫。 為保存庫指定與用於資源群組相同的位置。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. 指定要用於保存庫儲存體備援類型。

    * 您可以使用[本機備援儲存體](../storage/common/storage-redundancy-lrs.md)或[異地備援儲存體](../storage/common/storage-redundancy-grs.md)。
    * 下列範例會設定 **-BackupStorageRedundancy**選項[組 AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty)的 cmd **testvault**設為**異地備援**。

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>在訂用帳戶中檢視保存庫

若要檢視訂用帳戶中的所有保存庫，請使用 [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)。

```powershell
Get-AzRecoveryServicesVault
```

輸出會如下所示。 提供相關聯的資源群組和位置。

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>設定保存庫內容

將保存庫物件儲存在變數中，並設定保存庫內容。

* 許多 Azure 備份 cmdlet 作為輸入，需要在復原服務保存庫物件，因此很方便地將保存庫物件儲存在變數中。
* 保存庫內容是保存庫中受保護的資料類型。 使用設定[組 AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0)。 在設定的內容之後，它適用於所有後續的 cmdlet。

下列範例會設定 **testvault** 的保存庫內容。

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>擷取保存庫識別碼

我們計劃淘汰依據 Azure PowerShell 指導方針設定保存庫內容。 相反地，您可以儲存或擷取保存庫識別碼，並傳遞至相關的命令，如下所示：

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>設定備份原則

備份原則會指定備份排程，以及應該保留備份的復原點的時間長度：

* 備份原則至少與一個保留原則相關聯。 保護原則會定義復原點在被刪除之前要保留多久。
* 預設備份原則保留使用的檢視[Get AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0)。
* 預設備份原則排程使用的檢視[Get AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)。
* 您使用[新增 AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) cmdlet 來建立新的備份原則。 您輸入的排程和保留原則物件。

下列範例會將排程原則和保留原則儲存在變數中。 它接著會使用這些變數做為參數新原則的 (**NewSQLPolicy**)。 **NewSQLPolicy**接受每日的 「 完整 」 備份，將其保留 180 天會每隔 2 小時的記錄備份

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

輸出會如下所示。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 9:00:00 PM      Daily                                    False                True
```

## <a name="enable-backup"></a>啟用備份

### <a name="registering-the-sql-vm"></a>註冊 SQL VM

如需 Azure VM 備份和 Azure 檔案共用，備份服務可以連線到這些 Azure Resource Manager 資源，並擷取相關的詳細資料。 由於 SQL 應用程式在 Azure VM 中的，備份服務會需要存取應用程式，並擷取必要的詳細資料的權限。 若要這樣做，您必須 *'註冊'* 所在的復原服務保存庫 SQL 應用程式的 Azure VM。 一旦您將 SQL VM 向保存庫時，您可以保護 SQL Db，只有在該保存庫。 使用[註冊 AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS cmdlet，來註冊 VM。

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

命令會傳回這項資源的 '備份容器' 和狀態會 「 註冊 」

> [!NOTE]
> 如果未指定 force 參數，會要求使用者確認文字 '要停用保護，此容器'。 請忽略這段文字，然後說出"Y"，以確認。 這是已知的問題，我們正努力移除文字和 force 參數的需求

### <a name="fetching-sql-dbs"></a>正在擷取 SQL Db

完成註冊之後，備份服務將能夠列出所有在 VM 內的可用 SQL 元件。 若要檢視所有 SQL 元件，但備份到此保存庫，請使用[Get AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

輸出會顯示所有未受保護的 SQL 元件註冊至此保存庫項目類型和伺服器名稱的所有 SQL vm。 您可以進一步篩選至特定的 SQL VM，藉由傳遞 '-容器 ' 參數或使用的 'Name' 和 'ServerName' 以及 ItemType 組合旗標設為唯一的 SQL 項目會抵達。

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>設定備份

既然我們已經擁有必要的 SQL DB 和與原則的 it 需要備份，我們可以使用[啟用 AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) cmdlet 來設定此 SQL 資料庫的備份。

````powershell
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
````

此命令會等到完成設定備份，並傳回下列輸出。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>正在擷取新的 SQL Db

一旦註冊電腦，備份服務會擷取然後可用資料庫的詳細資料。 如果使用者已註冊的機器加入 SQL Db/SQL 執行個體的更新版本，您需要手動觸發備份的服務，以執行 '查詢' 以取得所有受保護的資料庫 （包括新加入的項目） 的全新一次。 使用[Initialize AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet 上的 SQL VM 來執行全新的查詢。 此命令會等到作業完成。 在稍後使用[Get AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS cmdlet 來取得最新的未受保護 SQL 元件的清單

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

一旦擷取相關的可保護項目，讓備份中的指示[ 區段上方](#configuring-backup)。
如果其中一個不想要以手動方式偵測新的資料庫使用，他們可以選擇 autoprotection 述[以下](#enable-autoprotection)。

## <a name="enable-autoprotection"></a>啟用 AutoProtection

使用者可以設定備份，使得在未來新增的所有資料庫會自動施以都保護，與特定原則。 若要啟用 autoprotection，請使用[啟用 AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS 指令程式。

既然是要備份所有未來的資料庫，此作業在 SQLInstance 所完成的指令層級。

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $targetPolicy -VaultId $targetvault.ID
```

一旦給予 autoprotection 意圖，於機器，以擷取新查詢加入 Db 發生於為排程的背景工作每隔 8 小時。

## <a name="restore-sql-dbs"></a>還原 SQL 資料庫

Azure 備份可以還原，如下所示執行 Azure Vm 的 SQL Server 資料庫：

1. 使用交易記錄備份還原到特定的日期或時間 （秒）。 Azure 備份會自動決定適當的完整差異備份和還原所需的記錄備份鏈結會根據選取的時間。
2. 將特定的完整或差異備份還原至特定復原點還原。

檢查前文所述的必要條件[此處](restore-sql-database-azure-vm.md#prerequisites)再還原 SQL 資料庫使用。

先使用 SQL DB 備份擷取的相關[Get AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS 指令程式。

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>擷取相關的還原時間

如上面所述，使用者可以還原至完整/差異複本備份 SQL DB**或**要記錄的時間點。

#### <a name="fetch-distinct-recovery-points"></a>擷取不同的復原點

使用[Get AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0)備份 SQL 資料庫的擷取相異 （完整/差異） 的復原點。

````powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
````

輸出會類似下列的範例

````powershell
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
````

使用 'RecoveryPointId' 篩選或陣列篩選器來擷取相關的復原點。

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>擷取時間點復原點

如果使用者想要將資料庫還原到特定的時間點，使用[Get AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS 指令程式。 Cmdlet 會傳回代表該 SQL 備份項目持續不中斷記錄鏈結的開始和結束時間日期的清單。 所需的時間點應該在此範圍內。

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

輸出會類似下列的範例。

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

上述的輸出，表示該使用者可以還原到任何點時間顯示的開始時間和結束時間之間。 時間是 utc 格式。 建構任何點時間在如上所示的範圍內的 PS 中。

> [!NOTE]
> 當某個記錄點-時間選取要還原的時也就起點，完整備份還原資料庫時，將不需要指定使用者。 Azure 備份服務會負責整個復原計劃也就是它的完整備份選擇的項目記錄備份，以套用等。

### <a name="determine-recovery-configuration"></a>判斷復原組態

發生 SQL DB 還原時支援下列還原案例。

1. 覆寫備份的 SQL DB，使用來自另一個復原點-OriginalWorkloadRestore 資料
2. 還原為新的資料庫位於相同的 SQL 執行個體-AlternateWorkloadRestore 的 SQL DB
3. 還原為新的資料庫在另一個 SQL VM-AlternateWorkloadRestore 的另一個 SQL 執行個體的 SQL DB

之後擷取相關的復原點 (distinct 或記錄的時間點)，使用[Get AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet 來擷取復原設定物件，根據所需的復原計劃。

#### <a name="original-workload-restore"></a>原始的工作負載還原

若要覆寫備份資料庫的復原點的資料，請只指定正確的旗標和相關的復原點下個範例所示。

##### <a name="original-restore-with-distinct-recovery-point"></a>使用不同的復原點的原始還原

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>原始記錄中時間點還原

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>替代的工作負載還原

> [!IMPORTANT]
> 備份 SQL DB 可以還原為新的資料庫到另一個 SQLInstance，'' 此保存庫登錄的 Azure VM 中。

如上所述，如果目標 SQLInstance 蘊含另一個 Azure VM，請確定它是[註冊至此保存庫](#registering-the-sql-vm)和相關的 SQLInstance 會顯示為可保護的項目。

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

只將相關的復原點，以正確的旗標的目標 SQL 執行個體如下所示。

##### <a name="alternate-restore-with-distinct-recovery-point"></a>使用不同的復原點的替代還原

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>替代的還原與記錄時間點

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

最終的復原點的組態物件取自[Get AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS cmdlet 已還原的所有相關資訊，並如下所示。

````powershell
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

您可以編輯已還原的資料庫名稱、 OverwriteWLIfpresent、 NoRecoveryMode 和 targetPhysicalPath 欄位。 取得目標檔案路徑，如下所示的更多詳細資料。

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

設定相關的 PS 屬性做為字串值，如下所示。

````powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl

TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
````

> [!IMPORTANT]
> 請確定最終的復原設定物件會具有所有必要和適當值，因為還原作業將會根據設定物件。

### <a name="restore-with-relevant-configuration"></a>相關的設定使用還原

相關的復原設定物件會取得並驗證之後，使用[還原 AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS cmdlet 來啟動還原程序。

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

還原作業會傳回要追蹤工作。

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>管理 SQL 備份

### <a name="on-demand-backup"></a>在隨選備份

一旦啟用備份的資料庫，使用者也可以觸發資料庫使用的隨選備份[備份 AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS 指令程式。 下列範例會觸發 SQL DB 上的完整備份已啟用壓縮，以及應該保留 60 天的完整備份。

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

臨機操作備份命令會傳回要追蹤的工作。

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

如果輸出已遺失，或如果您想要取得相關的作業識別碼[取得作業清單](#track-azure-backup-jobs)從 Azure 備份服務，然後追蹤 以及其詳細資料。

### <a name="change-policy-for-backup-items"></a>變更備份項目原則

使用者可以修改現有的原則，或從 [policy1] 中變更的原則備份的項目，為 Policy2。 若要切換原則備份的項目，只要擷取相關的原則和備份項目，並使用[啟用 AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0)命令與備份的項目，做為參數。

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

此命令會等到完成設定備份，並傳回下列輸出。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>重新註冊 SQL Vm

> [!WARNING]
> 請務必先閱讀這[文件](backup-sql-server-azure-troubleshoot.md#re-registration-failures)以了解失敗的徵狀和原因，然後再嘗試重新註冊

若要觸發的 SQL VM 的重新註冊，擷取相關的備份容器，並將它傳遞到註冊 cmdlet。

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
````

### <a name="stop-protection"></a>停止保護

#### <a name="retain-data"></a>保留資料

如果使用者想要停止保護，則可以使用[停用 AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS 指令程式。 這將會停止排定的備份，但直到備份的資料現在會永遠保留。

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>刪除備份資料

若要完全移除備份的資料儲存在保存庫，只要新增 '-RemoveRecoveryPoints' 旗標/切換到['disable' 保護命令](#retain-data)。

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

#### <a name="disable-auto-protection"></a>停用自動保護

如果 autoprotection SQLInstance 所設定，使用者可以使用停用它[停用 AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS 指令程式。

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>取消註冊 SQL VM

如果 SQL server 的所有資料庫使用[已不再受保護且沒有備份的資料存在](#delete-backup-data)，使用者可以取消註冊此保存庫中的 SQL VM。 只有使用者可以保護資料庫到另一個保存庫。 使用[取消註冊 AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS cmdlet 來取消註冊 SQL VM。

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>追蹤 Azure 的備份作業

請務必請注意，Azure 備份只會追蹤 SQL 備份中的使用者觸發作業。 在入口網站 /powershell 中看不到 排定的備份 （包括記錄備份）。 不過，如果有任何排定的工作失敗，[備份警示](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)會產生並顯示在入口網站中。 [使用 Azure 監視器](backup-azure-monitoring-use-azuremonitor.md)來追蹤所有排程的工作和其他相關資訊。

使用者可以追蹤觸發臨機操作/使用者作業中傳回的 JobID[輸出](#on-demand-backup)的非同步作業，例如備份。 使用[Get AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS cmdlet，來追蹤工作和其詳細資料。

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

若要從 Azure 備份服務中取得的臨機操作工作和其狀態的清單，請使用[Get AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS 指令程式。 下列範例會傳回所有進行中 SQL 作業。

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

若要取消進行中作業，請使用[停止 AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS 指令程式。

## <a name="managing-sql-always-on-availability-groups"></a>管理 SQL Always On 可用性群組

SQL Always On 可用性群組，請務必[註冊的所有節點](#registering-the-sql-vm)的可用性群組 (AG)。 完成註冊之後的所有節點，SQL 可用性群組物件邏輯上會建立可保護的項目 下。 SQL AG 下的資料庫會列為 'Sql Database'。 節點會顯示為獨立執行個體，並在其下的預設 SQL 資料庫會被列為以及 SQL database。

例如，假設 SQL AG 會有兩個節點: 'sql server-0' 和' sql server-1' 和 1 SQL AG DB。 這些節點會註冊之後，如果使用者[列出可保護的項目](#fetching-sql-dbs)，它會列出下列元件

1. SQL AG 物件-可保護項目類型為 SQLAvailabilityGroup
2. 為 sql Database 的 SQL AG 資料庫-可保護的項目類型
3. sql server 0-可保護的項目類型為 SQLInstance
4. sql server 1-為 SQLInstance 的可保護的項目類型
5. 在 sql server 0-任何預設 SQL Db （master、 model、 msdb） 可保護的項目類型為 sql Database
6. 為 sql Database 的 sql server 1-在任何預設值 （master、 model、 msdb） 的 SQL 資料庫使用可保護的項目類型

sql server 0，sql server 1 將也會列為 「 AzureVMAppContainer"時[備份的容器會列出](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0)。

只提取到相關的 SQL 資料庫[啟用備份](#configuring-backup)而[臨機操作備份](#on-demand-backup)並[還原 PS cmdlet](#restore-sql-dbs)完全相同。
