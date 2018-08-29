---
title: 備份 Azure Stack | Microsoft Docs
description: 在 Azure Stack 上以就地備份來執行隨選備份。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 578bb864f56b788db77d1201533e73d3b9616669
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2018
ms.locfileid: "41946528"
---
# <a name="back-up-azure-stack"></a>備份 Azure Stack

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

在 Azure Stack 上以就地備份來執行隨選備份。 如需有關設定 PowerShell 環境的指示，請參閱[安裝適用於 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。 若要登入 Azure Stack，請參閱[使用 Azure Stack 中的系統管理員入口網站](azure-stack-manage-portals.md)。

## <a name="start-azure-stack-backup"></a>啟動 Azure Stack 備份

### <a name="start-a-new-backup-without-job-progress-tracking"></a>啟動不含作業進度追蹤的新備份
使用 Start-AzSBackup 來立即啟動不含作業進度追蹤的新備份。

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-backup-with-job-progress-tracking"></a>啟動含作業進度追蹤的 Azure Stack 備份
使用 Start-AzSBackup 來啟動含有 -AsJob 變數的新備份，以追蹤備份作業進度。

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob 
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " `
    + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) `
    -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output

    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

## <a name="confirm-backup-has-completed"></a>確認備份已完成

### <a name="confirm-backup-has-completed-using-powershell"></a>使用 PowerShell 來確認備份已完成
使用下列 PowerShell 命令來確定備份已順利完成：

```powershell
   Get-AzsBackup
```

成果應會類似於以下輸出結果：

```powershell
    BackupDataVersion : 1.0.1
    BackupId          : <backup ID>
    RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
    Status            : Succeeded
    CreatedDateTime   : 7/6/2018 6:46:24 AM
    TimeTakenToCreate : PT20M32.364138S
    DeploymentID      : <deployment ID>
    StampVersion      : 1.1807.0.41
    OemVersion        : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
    Name              : local/<local name>
    Type              : Microsoft.Backup.Admin/backupLocations/backups
    Location          : local
    Tags              : {}
```

### <a name="confirm-backup-has-completed-in-the-administration-portal"></a>在系統管理入口網站中確認備份已完成
依照下列步驟，使用 Azure Stack 系統管理入口網站來確認備份已順利完成：

1. 開啟 [Azure Stack 系統管理入口網站](azure-stack-manage-portals.md)。
2. 選取 [更多服務] > [基礎結構的備份]。 在 [基礎結構備份] 刀鋒視窗中選擇 [設定]。
3. 在 [可用備份] 清單中尋找備份的 [名稱] 和 [完成日期]。
4. 確認**狀態**是**已成功**。

## <a name="next-steps"></a>後續步驟

深入了解從資料遺失事件從復原的工作流程。 請參閱[從重大資料遺失的情況下復原](azure-stack-backup-recover-data.md)。
