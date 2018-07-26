---
title: 備份 Azure Stack | Microsoft Docs
description: 在 Azure Stack 上以就地備份來執行隨選備份。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/08/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: c9e7ffae1b988d0940d10acdb1b387a25e0466ec
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242881"
---
# <a name="back-up-azure-stack"></a>備份 Azure Stack

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

在 Azure Stack 上以就地備份來執行隨選備份。 如需設定 PowerShell 環境的指示，請參閱[安裝 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。 若要登入 Azure Stack，請參閱[設定 Azure Stack PowerShell 環境](azure-stack-powershell-configure-admin.md)。

## <a name="start-azure-stack-backup"></a>啟動 Azure Stack 備份

使用 Start-AzSBackup 啟動具有 -AsJob 變數的新備份，以追蹤進度。 

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output
```

## <a name="confirm-backup-completed-via-powershell"></a>透過 PowerShell 確認備份已完成

```powershell
    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

- 成果應會類似於以下輸出結果：

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

## <a name="confirm-backup-completed-in-the-administration-portal"></a>在系統管理入口網站中確認已完成的備份

1. 開啟位於 [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external) 的 Azure Stack 系統管理入口網站。
2. 選取 [更多服務] > [基礎結構的備份]。 在 [基礎結構備份] 刀鋒視窗中選擇 [設定]。
3. 在 [可用備份] 清單中尋找備份的 [名稱] 和 [完成日期]。
4. 確認**狀態**是**已成功**。

## <a name="next-steps"></a>後續步驟

- 深入了解從資料遺失事件從復原的工作流程。 請參閱[從重大資料遺失的情況下復原](azure-stack-backup-recover-data.md)。