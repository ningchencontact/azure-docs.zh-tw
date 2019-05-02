---
title: 備份及還原使用 Azure 備份和 PowerShell 的 Azure 檔案
description: 備份及還原使用 Azure 備份和 PowerShell 的 Azure 檔案。
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: pullabhk
ms.openlocfilehash: 46719d3f72c57a44fb48489891e2348bb418da1e
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918960"
---
# <a name="back-up-and-restore-azure-files-with-powershell"></a>備份及還原 Azure 檔案和 PowerShell

這篇文章說明如何使用 Azure PowerShell 來備份和復原 Azure 檔案的檔案共用使用[Azure 備份](backup-overview.md)復原服務保存庫。 

本教學課程說明如何：

> [!div class="checklist"]
> * 設定 PowerShell，並註冊 Azure 復原服務提供者。
> * 建立復原服務保存庫。
> * 設定 Azure 檔案共用的備份。
> * 執行備份作業。
> * 還原備份 Azure 檔案共用或從共用個別檔案。
> * 監視備份與還原作業。


## <a name="before-you-start"></a>開始之前

- [详细了解](backup-azure-recovery-services-vault-overview.md)恢复服务保管库。
- 閱讀 預覽功能，如[備份 Azure 檔案共用](backup-azure-files.md)。
- 查看恢复服务的 PowerShell 对象层次结构。


## <a name="recovery-services-object-hierarchy"></a>復原服務物件階層

下图汇总了对象层次结构。

![復原服務物件階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

查看 Azure 库中的 **Az.RecoveryServices** [cmdlet 参考](/powershell/module/az.recoveryservices)。


## <a name="set-up-and-install"></a>設定和安裝

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

設定 PowerShell，如下所示：

1. [下載最新版的 Az PowerShell](/powershell/azure/install-az-ps)。 最低版本需求為 1.0.0。

2. 尋找 Azure 備份 PowerShell cmdlet，使用下列命令：

    ```powershell
    Get-Command *azrecoveryservices*
    ```
3. 檢閱的別名和 cmdlet 的 Azure 備份，Azure Site Recovery 和復原服務保存庫會出現。 以下是您可能會看到的範例。 它不是 cmdlet 的完整清單。

    ![復原服務 Cmdlet 清單](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. 登入 Azure 帳戶**Connect AzAccount**。
4. 在網頁上出現，系統會提示您輸入您的帳戶認證。

    - 或者，您可以做為參數中包含您的帳戶認證**Connect AzAccount** cmdlet 搭配 **-認證**。
    - 如果您是代表租用戶的 CSP 合作夥伴，指定客戶的租用戶中，然後再使用其 tenantID 或租用戶的主要網域名稱。 例如 **Connect-AzAccount -Tenant** fabrikam.com。

4. 建立您想要使用的帳戶，因為一個帳戶可以有多個訂用的訂用帳戶的關聯。

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. 如果您是第一次使用 Azure 備份，請使用 **Register-AzResourceProvider** Cmdlet 利用您的訂用帳戶來註冊 Azure 復原服務提供者。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. 確認提供者已成功註冊：

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
7. 在命令輸出中，確認**RegistrationState**變更為**已註冊**。 如果沒有，請執行**註冊 AzResourceProvider** cmdlet 一次。



## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

依照下列步驟建立復原服務保存庫。

- 復原服務保存庫是一項 Resource Manager 資源，因此您必須將它放在資源群組內。 您可以使用現有的資源群組，或使用 **New-AzResourceGroup** Cmdlet 建立資源群組。 建立資源群組時，請指定資源群組的名稱與位置。 

1. 保存庫位於資源群組。 如果您沒有現有的資源群組中，建立新的[新增 AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0)。 在此範例中，我們會建立新的資源群組，在美國西部區域中。

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```
2. 使用[新增 AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) cmdlet 來建立保存庫。 為保存庫指定與用於資源群組相同的位置。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. 指定要用於保存庫儲存體備援類型。

   - 您可以使用[本機備援儲存體](../storage/common/storage-redundancy-lrs.md)或[異地備援儲存體](../storage/common/storage-redundancy-grs.md)。
   - 下列範例會設定 **-BackupStorageRedundancy**選項[組 AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperties?view=azps-1.4.0)的 cmd **testvault**設為**異地備援**。

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>在訂用帳戶中檢視保存庫

若要檢視訂用帳戶中的所有保存庫，請使用 [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)。

```powershell
Get-AzRecoveryServicesVault
```

輸出會如下所示。 請注意，提供相關聯的資源群組和位置。

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

- 許多 Azure 備份 cmdlet 作為輸入，需要在復原服務保存庫物件，因此很方便地將保存庫物件儲存在變數中。
- 保存庫內容是保存庫中受保護的資料類型。 使用設定[組 AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0)。 在設定的內容之後，它適用於所有後續的 cmdlet。


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

- 備份原則至少與一個保留原則相關聯。 保護原則會定義復原點在被刪除之前要保留多久。
- 預設備份原則保留使用的檢視[Get AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0)。
- 預設備份原則排程使用的檢視[Get AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)。
-  您使用[新增 AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) cmdlet 來建立新的備份原則。 您輸入的排程和保留原則物件。

下列範例會將排程原則和保留原則儲存在變數中。 然後會使用這些變數做為參數的新原則 (**NewAFSPolicy**)。 **NewAFSPolicy** 會每日進行備份，並保留 30 天。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

輸出會如下所示。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```



## <a name="enable-backup"></a>啟用備份

定義備份原則之後，您可以啟用 Azure 檔案共用使用原則的保護。

### <a name="retrieve-a-backup-policy"></a>擷取的備份原則

擷取相關的原則物件[Get AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0)。 若要取得特定的原則，或檢視與工作負載類型相關聯的原則，請使用這個指令程式。

#### <a name="retrieve-a-policy-for-a-workload-type"></a>擷取原則，以供工作負載類型

下列範例會擷取工作負載類型的原則**azure 檔案**。

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

輸出會如下所示。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```
> [!NOTE]
> PowerShell 中 **BackupTime** 欄位的時區是國際標準時間 (UTC)。 當備份時間顯示在 Azure 入口網站中時，系統會根據您的當地時區調整時間。

### <a name="retrieve-a-specific-policy"></a>擷取特定的原則

下列原則會擷取名為 **dailyafs** 的備份原則。

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>啟用備份，並套用原則

使用啟用保護[啟用 AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0)。 與保存庫相關聯的原則之後，就會依照原則排程觸發備份。

下列範例會啟用保護 Azure 檔案共用**testAzureFileShare**儲存體帳戶中**testStorageAcct**，將原則**dailyafs**。

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

此命令會一直等到設定保護作業完成，並提供如下所示的輸出。

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="trigger-an-on-demand-backup"></a>觸發隨選備份

使用[備份 AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0)來執行隨選備份受保護的 Azure 檔案共用。

1. 擷取儲存體帳戶和檔案共用從容器中保留備份資料與保存庫[Get AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)。
2. 若要開始備份作業，您需使用 [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem) 來取得 VM 的相關資訊。
3. 執行隨選備份與[備份 AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem)。

執行隨選備份，如下所示：
    
```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

此命令會傳回具識別碼可供追蹤的作業，如下列範例所示。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Azure 檔案共用快照用於進行備份時，因此通常在命令傳回此輸出時作業便已完成。

### <a name="modify-the-protection-policy"></a>修改保護原則

若要變更用來備份 Azure 檔案共用的原則，請使用[啟用 AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0)。 指定相關的備份項目和新的備份原則。

下列範例會將 **testAzureFS** 保護原則從 **dailyafs** 變更為 **monthlyafs**。

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-files"></a>還原 Azure 檔案共用和檔案

您可以還原整個檔案共用上的特定檔案。 您可以還原到原始位置，或到替代位置。 

### <a name="fetch-recovery-points"></a>擷取復原點

使用[Get AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0)來列出備份項目的所有復原點。

下列指令碼：

- 變數 **$rp**是選取備份的項目，從過去七天的復原點的陣列。
- 陣列是以相反時間順序排序，最新復原點位於索引 **0**。
- 使用標準 PowerShell 陣列索引來挑選復原點。
- 在範例中，**$rp[0]** 會選取最新的復原點。

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

輸出會如下所示。

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```
選取相關的復原點之後，您還原的檔案共用或檔案的原始位置，或替代位置。

### <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Azure 檔案共用還原到替代位置

使用[還原 AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0)還原至選取的復原點。 指定這些參數來識別替代的位置： 

- **TargetStorageAccountName**：要還原備份內容的目標儲存體帳戶。 目標儲存體帳戶必須與保存庫位於相同位置。
- **TargetFileShareName**：要還原備份內容之目標儲存體帳戶內的檔案共用。
- **TargetFolder**：要還原資料之檔案共用下的資料夾。 如果備份的內容還原至根資料夾，則將目標資料夾值設為空字串。
- **ResolveConflict**：當與還原的資料有衝突時的指示。 可接受的值為 **Overwrite** 或 **Skip**。

執行此 cmdlet 使用的參數如下所示：

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

此命令會傳回具識別碼可供追蹤的作業，如下列範例所示。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

### <a name="restore-an-azure-file-to-an-alternate-location"></a>Azure 檔案還原到替代位置

使用[還原 AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0)還原至選取的復原點。 指定這些參數來識別替代位置，以及唯一識別您想要還原的檔案。

* **TargetStorageAccountName**：要還原備份內容的目標儲存體帳戶。 目標儲存體帳戶必須與保存庫位於相同位置。
* **TargetFileShareName**：要還原備份內容之目標儲存體帳戶內的檔案共用。
* **TargetFolder**：要還原資料之檔案共用下的資料夾。 如果備份的內容還原至根資料夾，則將目標資料夾值設為空字串。
* **SourceFilePath**：檔案共用中要還原之檔案的絕對路徑字串。 此路徑與 **Get-AzStorageFile** PowerShell Cmdlet 中使用的路徑相同。
* **SourceFileType**：選取目錄或檔案。 可接受的值為 **Directory** 或 **File**。
* **ResolveConflict**：當與還原的資料有衝突時的指示。 可接受的值為 **Overwrite** 或 **Skip**。

其他參數 （來源檔案路徑和 SourceFileType） 只與您想要還原個別檔案。

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

上一節中所示，此命令會傳回識別碼為要追蹤作業。

### <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>將 Azure 檔案共用和檔案還原到原始位置

當您還原到原始位置時，您不需要指定目的與目標相關的參數。 僅必須提供 **ResolveConflict**。

#### <a name="overwrite-an-azure-file-share"></a>覆寫 Azure 檔案共用

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>覆寫 Azure 檔案

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>追蹤備份和還原作業

視備份和還原作業會傳回識別碼，以及作業時所顯示您[執行隨選備份](#trigger-an-on-demand-backup)。 使用[Get AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) cmdlet 來追蹤工作進度和詳細資料。

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```
## <a name="next-steps"></a>後續步驟
[深入了解](backup-azure-files.md)備份 Azure 檔案，在 Azure 入口網站中。
