---
title: 使用 Azure Stack 驗證工具 | Microsoft Docs
description: 如何在 Azure Stack 中收集記錄檔以進行診斷。
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 12/03/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 82a691c0e0b6280a168605d56ee628d81f10823f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245645"
---
# <a name="validate-azure-stack-system-state"></a>驗證 Azure Stack 系統狀態

*適用範圍：Azure Stack 整合系統和 Azure Stack 開發套件*

Azure Stack 操作員能夠依需求了解系統的健康情況和狀態非常重要。 Azure Stack 驗證工具 (**Test-AzureStack**) 是一個 PowerShell Cmdlet，可讓您在系統上執行一系列的測試來找出故障 (如果有的話)。 當您連絡 Microsoft 客戶服務支援 (CSS) 解決問題時，通常會要求您透過[特殊權限端點 (PEP)](azure-stack-privileged-endpoint.md) 來執行此工具。 利用現有的全系統健康情況和狀態資訊，CSS 可以收集和分析詳細的記錄，專注於錯誤發生的區域，並與您一起解決問題。

## <a name="running-the-validation-tool-and-accessing-results"></a>執行驗證工具並存取結果

如先前所述，驗證工具是透過 PEP 執行。 每項測試會在 PowerShell 視窗中傳回 **PASS/FAIL** (通過/失敗) 狀態。 還會建立詳細的 HTML 報告，可於稍後在[記錄集合](azure-stack-diagnostics.md)期間存取該報告。 以下是端對端驗證測試流程的大綱： 

1. 存取具特殊權限端點 (PEP)。 執行下列命令來建立 PEP 工作階段：

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > 若要存取 ASDK 主機電腦上的 PEP，請使用 AzS-ERCS01 for -ComputerName。

2. 當您在 PEP 中，請執行： 

   ```powershell
   Test-AzureStack
   ```

   如需詳細資訊，請參閱[參數考量](azure-stack-diagnostic-test.md#parameter-considerations)和[使用案例範例](azure-stack-diagnostic-test.md#use-case-examples)區段。

3. 如果任何測試報告 **FAIL** (失敗)，請執行：

   ```powershell
   Get-AzureStackLog -FilterByRole SeedRing -OutputSharePath "<path>" -OutputShareCredential $cred
   ```

   此 Cmdlet 會收集 Test-AzureStack 所產生的記錄。 如需診斷記錄的詳細資訊，請參閱 [Azure Stack 診斷工具](azure-stack-diagnostics.md)。 如果測試回報 **WARN** (警告)，您便不應該收集記錄或連絡 CSS。

4. 如果您已透過 CSS 指示來執行驗證工具，CSS 代表會要求您收集的記錄，以繼續對問題進行疑難排解。

## <a name="tests-available"></a>測試可供使用

驗證工具可讓您執行一系列的系統層級的測試和基本雲端案例，使您能夠深入解析目前狀態，並確定系統中的問題。

### <a name="cloud-infrastructure-tests"></a>雲端基礎結構測試

這些影響不大的測試適用於基礎結構層級，並為您提供有關各種系統元件和函式的相關資訊。 目前，測試分組為以下類別：

| 測試分類                                        | -Include 和 -Ignore 的引數 |
| :--------------------------------------------------- | :-------------------------------- |
| Azure Stack 警示摘要                            | AzsAlertSummary                   |
| Azure Stack 備份共用的可及性摘要       | AzsBackupShareAccessibility       |
| Azure Stack 控制平面摘要                    | AzsControlPlane                   |
| Azure Stack Defender 摘要                         | AzsDefenderSummary                |
| Azure Stack 裝載基礎結構韌體摘要  | AzsHostingInfraFWSummary          |
| Azure Stack 雲端裝載基礎結構摘要     | AzsHostingInfraSummary            |
| Azure Stack 雲端裝載基礎結構使用率 | AzsHostingInfraUtilization        |
| Azure Stack 基礎結構容量                  | AzsInfraCapacity                  |
| Azure Stack 基礎結構效能               | AzsInfraPerformance               |
| Azure Stack 基礎結構角色摘要              | AzsInfraRoleSummary               |
| Azure Stack 更新摘要                           | AzsInfraUpdateSummary             |
| Azure Stack 入口網站和 API 摘要                   | AzsPortalAPISummary               |
| Azure Stack 縮放單位 VM 事件                     | AzsScaleUnitEvents                |
| Azure Stack 縮放單位 VM 資源                  | AzsScaleUnitResources             |
| Azure Stack SDN 驗證摘要                   | AzsSDNValidation                  |
| Azure Stack Service Fabric 角色摘要              | AzsSFRoleSummary                  |
| Azure Stack BMC 摘要                              | AzsStampBMCSummary                |
| Azure Stack 儲存體服務摘要                 | AzsStorageSvcsSummary             |
| Azure Stack SQL 存放區摘要                        | AzsStoreSummary                   |
| Azure Stack VM 放置摘要                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>雲端案例測試

除了上述基礎結構測試之外，您還可以執行雲端案例測試，以檢查基礎結構元件之間的功能。 由於這些測試涉及資源部署，所以需要雲端系統管理員認證才能執行這些測試。 
    > [!NOTE]
    >
    > Currently you cannot run cloud scenario tests using Active Directory Federated Services (AD FS) credentials. 

下列雲端案例都經過驗證工具測試：
- 資源群組建立   
- 方案建立              
- 供應項目建立            
- 儲存體帳戶建立   
- 虛擬機器建立 
- Blob 儲存體作業   
- 佇列儲存體作業  
- 資料表儲存體作業  

## <a name="parameter-considerations"></a>參數考量

- **List** 參數可用來顯示所有可用的測試類別。

- **Include** 和 **Ignore** 參數可用來包含或排除測試分類。 請參閱[可用的測試](azure-stack-diagnostic-test.md#tests-available)一節，以取得更多用於這些引數的速記資訊。

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- 租用戶 VM 會隨著雲端案例測試而部署。 您可以使用 **DoNotDeployTenantVm** 來停用此功能。 

- 您必須提供 **ServiceAdminCredential** 參數來執行雲端案例測試，如[使用案例範例](azure-stack-diagnostic-test.md#use-case-examples)一節中所述。

- 在測試基礎結構備份設定時，使用了 **BackupSharePath** 和 **BackupShareCredential**，如[使用案例範例](azure-stack-diagnostic-test.md#use-case-examples)一節中所示。

- 驗證工具也支援一般 PowerShell 參數：Verbose、Debug、ErrorAction、ErrorVariable、WarningAction、WarningVariable、OutBuffer、PipelineVariable 和 OutVariable。 如需詳細資訊，請參閱[關於一般參數](https://go.microsoft.com/fwlink/?LinkID=113216)。  

## <a name="use-case-examples"></a>使用案例範例

### <a name="run-validation-without-cloud-scenarios"></a>執行沒有雲端案例的驗證

執行沒有 **ServiceAdminCredential** 參數的驗證工具，以略過執行雲端案例測試： 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>執行具有雲端案例的驗證

依預設提供具有 **ServiceAdminCredentials** 參數的驗證工具，以執行雲端案例測試： 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

如果您希望僅執行雲端案例而不執行其餘測試，則可以使用 **Include** 參數來執行這項操作： 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

以 UPN 格式輸入雲端系統管理員的使用者名稱：serviceadmin@contoso.onmicrosoft.com (Azure AD)。 出現提示時，輸入雲端管理員帳戶的密碼。

### <a name="run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix"></a>執行驗證工具，以在安裝更新或 Hotfix 之前先測試系統整備

在開始安裝更新或 Hotfix 之前，應該執行驗證工具以檢查您的 Azure Stack 狀態：

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>執行驗證工具以測試基礎結構備份設定

設定基礎結構備份*之前*，您可以使用 **AzsBackupShareAccessibility** 測試來測試備份共用路徑和認證： 

  ```powershell
  New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
  ```

設定好備份*之後*，您可以執行 **AzsBackupShareAccessibility** 以驗證是否可以從 ERCS 存取共用：

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

若要對設定的備份共用測試新認證，請執行： 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```



## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Stack 診斷工具和問題記錄的詳細資訊，請參閱 [Azure Stack 診斷工具](azure-stack-diagnostics.md)。

若要深入了解疑難排解，請參閱 [Microsoft Azure Stack 疑難排解](azure-stack-troubleshooting.md)。