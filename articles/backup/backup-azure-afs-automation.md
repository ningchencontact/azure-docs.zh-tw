---
title: 使用 PowerShell 部署和管理 Azure 檔案共用
description: 在 Azure 中使用 PowerShell 針對 Azure 檔案共用部署和管理備份
services: backup
author: pvrk
manager: shivamg
keywords: PowersShell; Azure files backup; Azure files restore; Azure 檔案備份; Azure 2檔案還原;
ms.service: backup
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: pullabhk
ms.assetid: 80da8ece-2cce-40dd-8dce-79960b6ae073
ms.openlocfilehash: 90623981f67bbed15ade743192525676e58a0a83
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318379"
---
# <a name="use-powershell-to-back-up-and-restore-azure-file-shares"></a>使用 PowerShell 備份和還原 Azure 檔案共用

本文會說明如何使用 Azure PowerShell Cmdlet 從復原服務保存庫備份及復原 Azure 檔案共用。 復原服務保存庫是一個 Azure Resource Manager 資源，可用來保護 Azure 備份與 Azure Site Recovery 服務中的資料與資產。

## <a name="concepts"></a>概念

如果您不熟悉 Azure 備份服務，如需服務的概觀，請參閱文章[何謂 Azure 備份？](backup-introduction-to-azure-backup.md)。 在開始之前，請務必記下[這裡](backup-azure-files.md)記載的備份 Azure 檔案共用預覽功能。

若要有效地使用 PowerShell，就必須了解物件的階層及從何處開始。

![復原服務物件階層](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

若要檢視 AzureRm.RecoveryServices.Backup PowerShell Cmdlet 參考文件，請參閱 Azure 文件庫中的 [Azure 備份 - 復原服務 Cmdlet](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) 。

## <a name="setup-and-registration"></a>設定和註冊

> [!NOTE]
> 如[這裡](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.13.0)所述，AzureRM 模組中新功能的支援於 2018 年 11 月結束。 因此，我們使用新的 'Az' PS 模組，為 Azure 檔案共用的備份提供支援。 我們也正在計畫將 Az 模組上架成為正式上市版本。

開始：

1. [下載最新版本的'Az' PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-6.13.0) (所需的最低版本為：0.7.0)

2. 輸入下列命令，以找到可用的 Azure 備份 PowerShell Cmdlet：

    ```powershell
    Get-Command *azrecoveryservices*
    ```
    將顯示 Azure備份、Azure Site Recovery 與復原服務保存庫的別名與 Cmdlet。 下圖是您將看到的範例。 它不是 Cmdlet 的完整清單。

    ![復原服務清單](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. 使用 **Connect-AzAccount** 登入您的 Azure 帳戶。 此 Cmdlet 會開啟網頁，提示您輸入帳戶認證：

    * 或者，您可以使用 **-Credential** 參數，以參數形式將您的帳戶認證加入 **Connect-AzAccount** Cmdlet。
    * 如果您是代表租用戶工作的 CSP 合作夥伴，請使用客戶的 tenantID 或租用戶主要網域名稱將客戶指定為租用戶。 例如︰**Connect-AzAccount -Tenant "fabrikam.com"**

4. 由於一個帳戶可以有多個訂用帳戶，因此您必須將要使用的訂用帳戶與帳戶建立關聯：

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. 如果您是第一次使用 Azure 備份，您必須使用 **Register-AzResourceProvider** Cmdlet 利用您的訂用帳戶來註冊 Azure 復原服務提供者。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. 您可以使用下列命令，確認提供者已成功註冊：
    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    在命令輸出中，**RegistrationState** 應該變更為 **Registered**。 如果不是，只要再次執行 **Register-AzResourceProvider** Cmdlet 即可。

PowerShell 可以自動化下列工作：

* 建立復原服務保存庫
* 設定 Azure 檔案共用備份
* 觸發備份作業
* 監視備份作業
* 還原 Azure 檔案共用
* 從 Azure 檔案共用還原個別的 Azure 檔案

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

下列步驟將引導您完成建立復原服務保存庫。

1. 復原服務保存庫是一項 Resource Manager 資源，因此您必須將它放在資源群組內。 您可以使用現有的資源群組，或使用 **New-AzResourceGroup** Cmdlet 建立資源群組。 建立資源群組時，請指定資源群組的名稱與位置。  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. 使用 **New-AzRecoveryServicesVault** Cmdlet 來建立復原服務保存庫。 請務必為保存庫指定與用於資源群組相同的位置。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. 指定要使用的儲存體備援類型；您可以使用[本地備援儲存體 (LRS)](../storage/common/storage-redundancy-lrs.md) 或[異地備援儲存體 (GRS)](../storage/common/storage-redundancy-grs.md)。 下列範例顯示 testvault 的 -BackupStorageRedundancy 選項設為 GeoRedundant。

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>在訂用帳戶中檢視保存庫

若要檢視訂用帳戶中的所有保存庫，請使用 **Get-AzRecoveryServicesVault**：

```powershell
Get-AzRecoveryServicesVault
```

輸出會類似下列的範例，請注意，會提供相關聯的 ResourceGroupName 與位置。

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

許多 Azure 備份 Cmdlet 都需要將復原服務保存庫物件當做輸入。

使用 **Set-AzRecoveryServicesVaultContext** 設定保存庫內容。 保存庫內容設定之後就會套用至所有後續的 Cmdlet。 下列範例會設定保存庫 *testvault* 的保存庫內容。

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

> [!NOTE]
> 根據 Azure PowerShell 指導方針，我們計畫要淘汰保存庫內容的設定。 相對地，我們建議使用者傳遞如下所述的保存庫識別碼

或者，您可以儲存/擷取要執行 PowerShell 作業的保存庫識別碼，並將它傳遞至相關的命令。

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-backup-for-an-azure-file-share"></a>設定 Azure 檔案共用備份

### <a name="create-protection-policy"></a>建立保護原則

備份保護原則至少與一個保留原則相關聯。 保留原則會定義復原點在被刪除之前要保留多久。 使用 **Get-AzRecoveryServicesBackupRetentionPolicyObject** 檢視預設保留原則。  同樣地，您可以使用 **Get-AzRecoveryServicesBackupSchedulePolicyObject** 取得預設排程原則。 **New-AzRecoveryServicesBackupProtectionPolicy** Cmdlet 會建立 PowerShell 物件來保存備份原則資訊。 排程和保留原則物件可當作 **New-AzRecoveryServicesBackupProtectionPolicy** Cmdlet 的輸入。 下列範例會將排程原則和保留原則儲存在變數中。 這個範例在建立保護原則 *NewPolicy* 時會使用這些變數來定義參數。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

輸出類似於下列範例：

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```

### <a name="enable-protection"></a>啟用保護。

一旦您已定義保護原則，您就可以使用此原則為 Azure 檔案共用啟用保護。

首先，使用 **Get-AzRecoveryServicesBackupProtectionPolicy** Cmdlet 擷取相關的原則物件。 您可以使用此 Cmdlet 來取得特定的原則，或檢視與工作負載類型相關聯的原則。

下列範例會取得工作負載類型 AzureFiles 的原則。

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

輸出類似於下列範例：

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> PowerShell 中 BackupTime 欄位的時區是 UTC。 不過，當備份時間顯示在 Azure 入口網站中時，系統會根據您的當地時區調整時間。
>
>

下列原則會擷取名為 "dailyafs" 的備份原則

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

使用 **Enable-AzRecoveryServicesBackupProtection** 搭配指定的原則啟用項目保護。 一旦原則與保存庫相關聯，備份工作流程將依照原則排程定義的時間觸發。

下列範例會使用原則 dailyafs，針對儲存體帳戶 "testStorageAcct" 底下的 Azure 檔案共用 "testAzureFileShare" 啟用保護。

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

此命令會等到設定保護作業完成，並提供如下所示的輸出。

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

### <a name="trigger-an-on-demand-backup"></a>觸發隨選備份

使用 **Backup-AzRecoveryServicesBackupItem** 以觸發受保護之 Azure 檔案共用的備份作業。 使用下列命令擷取儲存體帳戶與其中的檔案共用，然後觸發隨選備份。

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

此命令會傳回可使用識別碼追蹤的作業，如下列範例所示。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

在進行備份時我們正在利用 Azure 檔案共用快照，因此通常在命令傳回此輸出時作業便已完成

### <a name="modify-protection-policy"></a>修改保護原則

如果您想要變更用來保護 Azure 檔案共用的原則，請使用 **Enable-AzRecoveryServicesBackupProtection** 搭配相關備份項目與新的保護原則。

下列範例會將 "testAzureFS" 的保護原則從 "dailyafs" 變更為 "monthlyafs"

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-sharesazure-files"></a>還原 Azure 檔案共用/Azure 檔案

您可以將整個檔案共用還原至原始位置或替代位置。 同樣地，您也可以還原檔案共用中的個別檔案。

### <a name="fetching-recovery-points"></a>擷取復原點

使用 **Get-AzRecoveryServicesBackupRecoveryPoint** Cmdlet 來列出備份項目的所有復原點。 在下列指令碼中，變數 **$rp** 是選取的備份項目在過去七天的復原點陣列。 陣列是以相反時間順序排序，最新復原點位於索引 0。 使用標準 PowerShell 陣列索引來挑選復原點。 在範例中，$rp[0] 會選取最新的復原點。

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

輸出類似於下列範例：

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

一旦選取相關的復原點之後，就可繼續將檔案共用/檔案還原至替代/原始位置，如下所述。

### <a name="restore-azure-file-shares-to-an-alternate-location"></a>將 Azure 檔案共用還原至替代位置

#### <a name="restoring-an-azure-file-share"></a>還原 Azure 檔案共用

提供下列資訊來識別替代位置：

* ***TargetStorageAccountName***：要還原備份內容的目標儲存體帳戶。 目標儲存體帳戶應該與保存庫位在相同位置。
* ***TargetFileShareName***：要還原備份內容之目標儲存體帳戶中的檔案共用
* ***TargetFolder***：要還原資料之檔案共用下的資料夾。 如果備份的內容應還原至根資料夾，目標資料夾的值為空字串
* ***ResolveConflict***：如果還原之資料有衝突的指示。 可接受的值為 "Overwrite" 或 "skip"

提供這些參數給還原命令，以將備份的檔案共用還原至替代位置。

````powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
````

此命令會傳回具識別碼可供追蹤的作業，如下列範例所示。

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
````

#### <a name="restoring-an-azure-file"></a>還原 Azure 檔案

如果您想要還原個別檔案，而不是整個檔案共用，您應提供下列參數以唯一方式識別個別檔案。

* ***TargetStorageAccountName***：要還原備份內容的目標儲存體帳戶。 目標儲存體帳戶應該與保存庫位在相同位置。
* ***TargetFileShareName***：要還原備份內容之目標儲存體帳戶中的檔案共用
* ***TargetFolder***：要還原資料之檔案共用下的資料夾。 如果備份的內容應還原至根資料夾，目標資料夾的值為空字串
* ***SourceFilePath***：檔案共用中要還原之檔案的絕對路徑字串。 這是與 ```Get-AzStorageFile``` PS Cmdlet 中所使用的相同路徑。
* ***SourceFileType***：選取目錄或檔案。 可接受的值為 "Directory" 或 "File"
* ***ResolveConflict***：如果還原之資料有衝突的指示。 可接受的值為 "Overwrite" 或 "skip"

如您所見，額外的參數僅與要還原的個別檔案相關聯。

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

這也會傳回可使用識別碼追蹤的作業，如上所示。

### <a name="restore-azure-file-shares-to-original-location"></a>將 Azure 檔案共用還原至原始位置

在還原至原始位置的情況下，無需指定與參數相關聯的所有目的地/目標。 只需要提供 ```ResolveConflict```

#### <a name="overwrite-an-azure-file-share"></a>覆寫 Azure 檔案共用

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>覆寫 Azure 檔案

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>追蹤備份和還原作業

隨選備份和還原作業會傳回隨附識別碼的作業，如[上面](#trigger-an-on-demand-backup)所示。 使用 ```Get-AzRecoveryServicesBackupJobDetails``` Commandlet 來追蹤作業進度和擷取更多詳細資料。

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
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support
```
