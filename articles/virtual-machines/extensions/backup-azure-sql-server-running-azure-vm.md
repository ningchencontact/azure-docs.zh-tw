---
title: 在 Azure VM 中執行的 SQL Server 的 azure 備份
description: 如何註冊 Azure 備份的 SQL Server Azure VM 中執行
services: backup
author: swatisachdeva
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: swatisachdeva
ms.openlocfilehash: 8710242e04156c8af6e5882a3cb4d42cc31e3677
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607607"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>在 Azure VM 中執行的 SQL Server 的 azure 備份

在其他供應項目之間的 azure 備份提供支援的備份工作負載，例如 SQL Server Azure Vm 中執行。 因為 SQL 應用程式在 Azure VM 中執行，備份服務會需要存取應用程式，並擷取必要的詳細資料的權限。
若要這樣做，請安裝 Azure 備份**AzureBackupWindowsWorkload**的 VM 上，SQL Server 執行所在，由使用者觸發註冊程序期間的延伸模組。

## <a name="prerequisites"></a>必要條件

如需支援的案例清單，請參閱[可支援性對照表](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support)Azure 備份支援。

## <a name="network-connectivity"></a>網路連線

Azure 備份支援 NSG 標記，部署 proxy 伺服器，或列出的 IP 範圍;如需每個方法的詳細資訊，請參閱這[文章](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity)。

## <a name="extension-schema"></a>擴充功能結構描述

擴充功能結構描述和屬性值會傳遞至 CRP API 服務的組態值 （執行階段設定）。 註冊和升級期間，會使用這些組態值。 **AzureBackupWindowsWorkload**延伸模組也會使用此結構描述。 預先設定結構描述;objectStr 欄位可以加入新的參數

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

下列 JSON 顯示 WorkloadBackup 延伸模組的結構描述。  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>屬性值

名稱 | 值/範例 | 資料類型
 --- | --- | ---
地區設定 | zh-TW  |  string
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | string
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = =" | string
commandStartTimeUTCTicks | "636967192566036845"  | string
vmType  | "microsoft.compute/virtualmachines"  | string
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = =" | string
logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string
statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | string


## <a name="template-deployment"></a>範本部署

我們建議您新增至虛擬機器的 AzureBackupWindowsWorkload 延伸模組藉由啟用虛擬機器上的 SQL Server 備份。 這可以透過來達成[Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup)專為自動化 SQL Server VM 上的備份。


## <a name="powershell-deployment"></a>PowerShell 部署

您要註冊所在的復原服務保存庫 SQL 應用程式的 Azure VM。 在註冊期間，AzureBackupWindowsWorkload 延伸模組會安裝在 VM 上。 使用 [註冊 AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) cmdlet 來註冊 VM。
 
```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```
 
命令會傳回**備份容器**這會是資源和狀態**註冊**。


## <a name="next-steps"></a>後續步驟

- [了解更多](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot)有關 Azure SQL Server VM 備份的疑難排解指導方針
- [常見的問題](https://docs.microsoft.com/azure/backup/faq-backup-sql-server)有關備份 Azure 虛擬機器 (Vm) 上執行，並使用 Azure 備份服務的 SQL Server 資料庫。
