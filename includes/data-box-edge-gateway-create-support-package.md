---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174438"
---
如果您遇到任何裝置問題，您可以從系統記錄檔來建立支援封裝。 Microsoft 支援服務會使用此套件，針對問題進行疑難排解。 請遵循下列步驟來建立支援封裝：

1. [連接到您的裝置的 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用`Get-HcsNodeSupportPackage`命令來建立支援封裝。 此 cmdlet 的使用方式如下所示：

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    此 cmdlet 會從您的裝置收集記錄檔，並將這些記錄檔複製到指定的網路或本機共用。

    所使用的參數如下所示：

    - `-Path` -指定的網路或本機路徑複製到支援封裝。 (必要)
    - `-Credential` -指定的認證來存取受保護的路徑。
    - `-Zip` -指定產生的 zip 檔案。
    - `-Include` -指定要包含的支援封裝中包含的元件。 如果未指定，`Default`假設。
    - `-IncludeArchived` -指定要支援封裝中包含封存的記錄檔。
    - `-IncludePeriodicStats` -指定要支援封裝中包含定期統計資料的記錄檔。

    
