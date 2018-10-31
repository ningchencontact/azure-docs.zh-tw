---
title: 了解 Azure 更新管理中的代理程式檢查結果
description: 了解如何針對「更新管理」代理程式的問題進行疑難排解。
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/10/2018
ms.topic: conceptual
ms.service: automation
ms.component: update-management
manager: carmonm
ms.openlocfilehash: 1b258d115e7d9962ecab4b93dbcd98d13f5977c7
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956675"
---
# <a name="understand-the-agent-check-results-in-update-management"></a>了解更新管理中的代理程式檢查結果

您的非 Azure 機器在「更新管理」中未顯示為 [就緒] 的原因有很多。 在「更新管理」中，您可以檢查「混合式背景工作角色」代理程式的健康情況，以判斷根本問題。 本文探討如何從 Azure 入口網站及在離線情況下執行疑難排解員。

## <a name="start-the-troubleshooter"></a>啟動疑難排解員

在入口網站中按一下 [更新代理程式整備程度] 資料行底下的 [疑難排解] 連結，即可啟動 [對更新代理程式進行疑難排解] 頁面。 此頁面會顯示代理程式的問題及本文的連結，以協助您針對問題進行疑難排解。

![VM 清單頁面](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> VM 必須處於執行中狀態，才能進行檢查。 如果 VM 不是處於執行中狀態，您就會看到 [啟動 VM] 的按鈕。

在 [對更新代理程式進行疑難排解] 頁面上，按一下 [執行檢查] 以啟動疑難排解員。 疑難排解員會使用[執行命令](../../virtual-machines/windows/run-command.md)在機器上執行指令碼，以確認代理程式所具有的相依性。 當疑難排解員完成時，它會傳回檢查結果。

![疑難排解頁面](../media/update-agent-issues/troubleshoot-page.png)

完成時，會在視窗中傳回結果。 [檢查區段](#pre-requisistes-checks)會提供有關每個檢查所尋找項目的資訊。

![更新代理程式檢查頁面](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>先決條件檢查

### <a name="operating-system"></a>作業系統

OS 檢查會確認「混合式 Runbook 背景工作角色」是否正在執行下列其中一個作業系統：

|作業系統  |注意  |
|---------|---------|
|Windows Server 2008、Windows Server 2008 R2 RTM    | 僅支援更新評估。         |
|Windows Server 2008 R2 SP1 和更新版本     |必須要有 .NET Framework 4.5 或更新版本。 ([下載 .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> 必須要有 Windows PowerShell 4.0 或更新的版本。 ([下載 WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> 建議使用 Windows PowerShell 5.1 以增加可靠性。  ([下載 WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) 和 7 (x64)      | Linux 代理程式必須能夠存取更新存放庫。 分類型修補需要 'yum' 來傳回 CentOS 未內建的安全性資料。         |
|Red Hat Enterprise 6 (x86/x64) 和 7 (x64)     | Linux 代理程式必須能夠存取更新存放庫。        |
|SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)     | Linux 代理程式必須能夠存取更新存放庫。        |
|Ubuntu 14.04 LTS 和 16.04 LTS (x86/x64)      |Linux 代理程式必須能夠存取更新存放庫。         |

### <a name="net-45"></a>.NET 4.5

.NET Framework 檢查會確認系統是否至少有 [.NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)。

### <a name="wmf-51"></a>WMF 5.1

WMF 檢查會確認系統是否具有所需版本的 Windows Management Framework。 [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) 是所支援的最低版本。 建議您安裝 [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616) 以提升「混合式 Runbook 背景工作角色」可靠性。

### <a name="tls-12"></a>TLS 1.2

此檢查會判斷您是否使用 TLS 1.2 來加密通訊。 此平台已不支援 TLS 1.0，建議用戶端使用 TLS 1.2 與「更新管理」進行通訊。

## <a name="connectivity-checks"></a>連線能力檢查

### <a name="registration-endpoint"></a>註冊端點

此檢查會判斷代理程式是否能夠正確地與代理程式服務進行通訊。

Proxy 和防火牆設定必須允許「混合式 Runbook 背景工作角色」代理程式與註冊端點進行通訊。 如需要開放的位址和連接埠清單，請參閱[混合式背景工作角色的網路規劃](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>作業端點

此檢查會判斷代理程式是否能夠正確地與「作業執行階段資料服務」進行通訊。

Proxy 和防火牆設定必須允許「混合式 Runbook 背景工作角色」代理程式與「作業執行階段資料服務」進行通訊。 如需要開放的位址和連接埠清單，請參閱[混合式背景工作角色的網路規劃](../automation-hybrid-runbook-worker.md#network-planning)

## <a name="vm-service-health-checks"></a>VM 服務健康情況檢查

### <a name="monitoring-agent-service-status"></a>監視代理程式服務狀態

此檢查會判斷 Microsoft Monitoring Agent `HealthService` 是否正在機器上執行。

若要深入了解如何針對此服務的問題進行疑難排解，請參閱 [Microsoft Monitoring Agent 未執行](hybrid-runbook-worker.md#mma-not-running)。

若要安裝 Microsoft Monitoring Agent，請參閱[安裝並設定 Microsoft Monitoring Agent](/log-analytics/log-analytics-concept-hybrid.md#install-and-configure-agent)

### <a name="monitoring-agent-service-events"></a>監視代理程式服務事件

此檢查會判斷在機器上的 Operations Manager 記錄中，過去 24 小時內是否有任何 `4502` 事件。

若要深入了解此事件，請參閱此事件的[疑難排解指南](hybrid-runbook-worker.md#event-4502)。

## <a name="access-permissions-checks"></a>存取權限檢查

### <a name="machinekeys-folder-access"></a>MachineKeys 資料夾存取

「Crypto 資料夾存取權」檢查會判斷「本機系統帳戶」是否能夠存取 `C:\ProgramData\Microsoft\Crypto\RSA`

## <a name="troubleshoot-offline"></a>離線疑難排解

您可以藉由在本機執行指令碼，在「混合式 Runbook 背景工作角色」上離線使用疑難排解員。 您可以在「PowerShell 資源庫」上找到 [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration) 指令碼。 以下範例顯示此指令碼的輸出範例：

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.1.7601 (Windows Server 2008 R2 SP1) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .Net Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             : 
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Microsoft Monitoring Agent, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Microsoft Monitoring Agent, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>後續步驟

若要針對「混合式 Runbook 背景工作角色」的其他問題進行疑難排解，請參閱[疑難排解 - 混合式 Runbook 背景工作角色](hybrid-runbook-worker.md)
