---
title: 針對更新管理的相關錯誤進行疑難排解
description: Learn how to troubleshoot issues with Update Management.
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 952bcb85484e885d45876de1e4cf3326db0a146a
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693397"
---
# <a name="troubleshooting-issues-with-update-management"></a>針對更新管理問題進行疑難排解

This article discusses solutions to issues that you might encounter when you use Update Management.

There's an agent troubleshooter for the Hybrid Worker agent to determine the underlying problem. 若要深入了解疑難排解員，請參閱[針對更新代理程式問題進行疑難排解](update-agent-issues.md)。 For all other issues, use the following troubleshooting guidance.

If you encounter issues while you're trying to onboard the solution on a virtual machine (VM), check the <bpt id="p1">**</bpt>Operations Manager<ept id="p1">**</ept> log under <bpt id="p2">**</bpt>Application and Services Logs<ept id="p2">**</ept> on the local machine for events with event ID 4502 and event details that contain <bpt id="p3">**</bpt>Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent<ept id="p3">**</ept>.

The following section highlights specific error messages and possible resolutions for each. For other onboarding issues see <bpt id="p1">[</bpt>Troubleshoot solution onboarding<ept id="p1">](onboarding.md)</ept>.

## <bpt id="p1">&lt;a name="nologs"&gt;</bpt><ept id="p1">&lt;/a&gt;</ept>Scenario: Machines don't show up in the portal under Update Management

### <a name="issue"></a>問題

You experience the following symptoms:

* Your machine shows <bpt id="p1">**</bpt>Not configured<ept id="p1">**</ept> from the Update Management view of a VM.

* Your machines are missing from the Update Management view of your Azure Automation account.

* You have machines that show as <bpt id="p1">**</bpt>Not Assessed<ept id="p1">**</ept> under <bpt id="p2">**</bpt>Compliance<ept id="p2">**</ept>. However, you see heartbeat data in Azure Monitor logs for the Hybrid Runbook Worker but not for Update Management.

### <a name="cause"></a>原因

This issue can be caused by local configuration issues or by improperly configured scope configuration.

You might have to reregister and reinstall the Hybrid Runbook Worker.

You might have defined a quota in your workspace that's been reached and that's preventing further data storage.

### <a name="resolution"></a>解析度

* Run the troubleshooter for <bpt id="p1">[</bpt>Windows<ept id="p1">](update-agent-issues.md#troubleshoot-offline)</ept> or <bpt id="p2">[</bpt>Linux<ept id="p2">](update-agent-issues-linux.md#troubleshoot-offline)</ept>, depending on the OS.

* Make sure your machine is reporting to the correct workspace. For guidance on how to verify this aspect, see <bpt id="p1">[</bpt>Verify agent connectivity to Log Analytics<ept id="p1">](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics)</ept>. Also make sure this workspace is linked to your Azure Automation account. To confirm, go to your Automation account and select <bpt id="p1">**</bpt>Linked workspace<ept id="p1">**</ept> under <bpt id="p2">**</bpt>Related Resources<ept id="p2">**</ept>.

* Make sure the machines show up in your Log Analytics workspace. Run the following query in the Log Analytics workspace that's linked to your Automation account:

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
  If you don't see your machine in the query results, it hasn't recently checked in, which means there's probably a local configuration issue and you should <bpt id="p1">[</bpt>reinstall the agent<ept id="p1">](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)</ept>. If your machine shows up in the query results, you need to verify the scope configuration specified in the next bulleted item in this list.

* Check for scope configuration problems. <bpt id="p1">[</bpt>Scope configuration<ept id="p1">](../automation-onboard-solutions-from-automation-account.md#scope-configuration)</ept> determines which machines get configured for the solution. If your machine is showing up in your workspace but not in the <bpt id="p1">**</bpt>Update Management<ept id="p1">**</ept> portal, you'll need to configure the scope configuration to target the machines. To learn how to do this, see <bpt id="p1">[</bpt>Onboard machines in the workspace<ept id="p1">](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)</ept>.

* In your workspace, run the following query:

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```
  If you get a <ph id="ph1">`Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota`</ph> result, there's a quota defined on your workspace that's been reached and that has stopped data from being saved. In your workspace, go to <bpt id="p1">**</bpt>Usage and estimated costs<ept id="p1">**</ept><ph id="ph1"> &gt; </ph><bpt id="p2">**</bpt>data volume management<ept id="p2">**</ept> and check your quota or remove it.

* If these steps don't resolve your problem, follow the steps at <bpt id="p1">[</bpt>Deploy a Windows Hybrid Runbook Worker<ept id="p1">](../automation-windows-hrw-install.md)</ept> to reinstall the Hybrid Worker for Windows. Or, for Linux, <bpt id="p1">[</bpt>deploy a Linux Hybrid Runbook Worker<ept id="p1">](../automation-linux-hrw-install.md)</ept>.

## <bpt id="p1">&lt;a name="rp-register"&gt;</bpt><ept id="p1">&lt;/a&gt;</ept>Scenario: Unable to register Automation Resource Provider for subscriptions

### <a name="issue"></a>問題

When you work with solutions in your Automation account, you encounter the following error:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>原因

The Automation Resource Provider isn't registered in the subscription.

### <a name="resolution"></a>解析度

To register the Automation Resource Provider, follow these steps in the Azure portal:

1. In the Azure service list at the bottom of the portal, select <bpt id="p1">**</bpt>All services<ept id="p1">**</ept>, and then select <bpt id="p2">**</bpt>Subscriptions<ept id="p2">**</ept> in the General service group.
2. 選取您的訂用帳戶。
3. Under <bpt id="p1">**</bpt>Settings<ept id="p1">**</ept>, select <bpt id="p2">**</bpt>Resource Providers<ept id="p2">**</ept>.
4. From the list of resource providers, verify that the <bpt id="p1">**</bpt>Microsoft.Automation<ept id="p1">**</ept> resource provider is registered.
5. If it's not listed, register the <bpt id="p1">**</bpt>Microsoft.Automation<ept id="p1">**</ept> provider by following the steps at <bpt id="p2">[</bpt>Resolve errors for resource provider registration<ept id="p2">](/azure/azure-resource-manager/resource-manager-register-provider-errors)</ept>.

## <bpt id="p1">&lt;a name="components-enabled-not-working"&gt;</bpt><ept id="p1">&lt;/a&gt;</ept>Scenario: The components for the Update Management solution have been enabled, and now this virtual machine is being configured

### <a name="issue"></a>問題

上架後 15 分鐘，您會繼續在虛擬機器上看見下列訊息：

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>原因

This error can occur for the following reasons:

- Communication with the Automation account is being blocked.
- The VM being onboarded might have come from a cloned machine that wasn't sysprepped with the Microsoft Monitoring Agent (MMA) installed.

### <a name="resolution"></a>解析度

1. Go to <bpt id="p1">[</bpt>Network planning<ept id="p1">](../automation-hybrid-runbook-worker.md#network-planning)</ept> to learn about which addresses and ports must be allowed for Update Management to work.
2. If you're using a cloned image:
   1. In your Log Analytics workspace, remove the VM from the saved search for the <ph id="ph1">`MicrosoftDefaultScopeConfig-Updates`</ph> scope configuration if it's shown. 您可以在工作區中的 [一般] 底下找到儲存的搜尋。
   2. 執行 `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`。
   3. 執行 `Restart-Service HealthService` 以重新啟動 `HealthService`。 This recreates the key and generates a new UUID.
   4. If this approach doesn't work, run sysprep on the image first and then install the MMA.

## <bpt id="p1">&lt;a name="multi-tenant"&gt;</bpt><ept id="p1">&lt;/a&gt;</ept>Scenario: You receive a linked subscription error when you create an update deployment for machines in another Azure tenant

### <a name="issue"></a>問題

You encounter the following error when you try to create an update deployment for machines in another Azure tenant:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>原因

This error occurs when you create an update deployment that has Azure VMs in another tenant that's included in an update deployment.

### <a name="resolution"></a>解析度

Use the following workaround to get these items scheduled. You can use the <bpt id="p1">[</bpt>New-AzureRmAutomationSchedule<ept id="p1">](/powershell/module/azurerm.automation/new-azurermautomationschedule)</ept> cmdlet with the <ph id="ph1">`-ForUpdate`</ph> switch to create a schedule. Then, use the <bpt id="p1">[</bpt>New-AzureRmAutomationSoftwareUpdateConfiguration<ept id="p1">](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
)</ept> cmdlet and pass the machines in the other tenant to the <ph id="ph1">`-NonAzureComputer`</ph> parameter. 下列範例示範如何執行：

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <bpt id="p1">&lt;a name="node-reboots"&gt;</bpt><ept id="p1">&lt;/a&gt;</ept>Scenario: Unexplained reboots

### <a name="issue"></a>問題

Even though you've set the <bpt id="p1">**</bpt>Reboot Control<ept id="p1">**</ept> option to <bpt id="p2">**</bpt>Never Reboot<ept id="p2">**</ept>, machines are still rebooting after updates are installed.

### <a name="cause"></a>原因

Windows Update can be modified by several registry keys, any of which can modify reboot behavior.

### <a name="resolution"></a>解析度

Review the registry keys listed under <bpt id="p1">[</bpt>Configuring Automatic Updates by editing the registry<ept id="p1">](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-rej7uijui7jgistry)</ept> and <bpt id="p2">[</bpt>Registry keys used to manage restart<ept id="p2">](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)</ept> to make sure your machines are configured properly.

## <bpt id="p1">&lt;a name="failed-to-start"&gt;</bpt><ept id="p1">&lt;/a&gt;</ept>Scenario: Machine shows "Failed to start" in an update deployment

### <a name="issue"></a>問題

A machine shows a <bpt id="p1">**</bpt>Failed to start<ept id="p1">**</ept> status. When you view the specific details for the machine, you see the following error:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>原因

此錯誤可能會因為下列其中一個原因而發生：

* The machine doesn’t exist anymore.
* The machine is turned off and unreachable.
* The machine has a network connectivity issue, and therefore the hybrid worker on the machine is unreachable.
* There was an update to the MMA that changed the SourceComputerId.
* Your update run was throttled if you hit the limit of 2,000 concurrent jobs in an Automation account. Each deployment is considered a job, and each machine in an update deployment counts as a job. Any other automation job or update deployment currently running in your Automation account counts toward the concurrent job limit.

### <a name="resolution"></a>解析度

When applicable, use <bpt id="p1">[</bpt>dynamic groups<ept id="p1">](../automation-update-management-groups.md)</ept> for your update deployments. Additionally:

* Verify that the machine still exists and is reachable. If it doesn't exist, edit your deployment and remove the machine.
* See the <bpt id="p1">[</bpt>network planning<ept id="p1">](../automation-update-management.md#ports)</ept> section for a list of ports and addresses that are required for Update Management, and then verify that your machine meets these requirements.
* Run the following query in Log Analytics to find machines in your environment whose <ph id="ph1">`SourceComputerId`</ph> has changed. Look for computers that have the same <ph id="ph1">`Computer`</ph> value but a different <ph id="ph2">`SourceComputerId`</ph> value. 

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```
   After you find affected machines, edit the update deployments that target those machines, and then remove and re-add them so that <ph id="ph1">`SourceComputerId`</ph> reflects the correct value.

## <bpt id="p1">&lt;a name="updates-nodeployment"&gt;</bpt><ept id="p1">&lt;/a&gt;</ept>Scenario: Updates are installed without a deployment

### <a name="issue"></a>問題

When you enroll a Windows machine in Update Management, you see updates installed without a deployment.

### <a name="cause"></a>原因

On Windows, updates are installed automatically as soon as they're available. This behavior can cause confusion if you didn't schedule an update to be deployed to the machine.

### <a name="resolution"></a>解析度

The  <ph id="ph1">`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU`</ph> registry key defaults to a setting of 4: <bpt id="p1">**</bpt>auto download and install<ept id="p1">**</ept>.

For Update Management clients, we recommend setting this key to 3: <bpt id="p1">**</bpt>auto download but do not auto install<ept id="p1">**</ept>.

For more information, see <bpt id="p1">[</bpt>Configuring Automatic Updates<ept id="p1">](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates)</ept>.

## <a name="machine-already-registered"></a>案例：電腦已經註冊至不同的帳戶

### <a name="issue"></a>問題

您收到下列錯誤訊息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>原因

The machine has already been onboarded to another workspace for Update Management.

### <a name="resolution"></a>解析度

1. Follow the steps under <bpt id="p1">[</bpt>Machines don't show up in the portal under Update Management<ept id="p1">](#nologs)</ept> to make sure the machine is reporting to the correct workspace.
2. Clean up old artifacts on the machine by <bpt id="p1">[</bpt>deleting the hybrid runbook group<ept id="p1">](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)</ept>, and then try again.

## <bpt id="p1">&lt;a name="machine-unable-to-communicate"&gt;</bpt><ept id="p1">&lt;/a&gt;</ept>Scenario: Machine can't communicate with the service

### <a name="issue"></a>問題

您會收到下列其中一個錯誤訊息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

### <a name="cause"></a>原因

A proxy, gateway, or firewall might be blocking network communication.

### <a name="resolution"></a>解析度

Review your networking and make sure appropriate ports and addresses are allowed. See <bpt id="p1">[</bpt>network requirements<ept id="p1">](../automation-hybrid-runbook-worker.md#network-planning)</ept> for a list of ports and addresses that are required by Update Management and Hybrid Runbook Workers.

## <a name="unable-to-create-selfsigned-cert"></a>案例：無法建立自我簽署憑證

### <a name="issue"></a>問題

您會收到下列其中一個錯誤訊息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>原因

The Hybrid Runbook Worker couldn't generate a self-signed certificate.

### <a name="resolution"></a>解析度

Verify that the system account has read access to the <bpt id="p1">**</bpt>C:\ProgramData\Microsoft\Crypto\RSA<ept id="p1">**</ept> folder, and try again.

## <bpt id="p1">&lt;a name="mw-exceeded"&gt;</bpt><ept id="p1">&lt;/a&gt;</ept>Scenario: The scheduled update failed with a MaintenanceWindowExceeded error

### <a name="issue"></a>問題

The default maintenance window for updates is 120 minutes. You can increase the maintenance window to a maximum of 6 hours, or 360 minutes.

### <a name="resolution"></a>解析度

Edit any failing scheduled update deployments, and increase the maintenance window.

For more information on maintenance windows, see <bpt id="p1">[</bpt>Install updates<ept id="p1">](../automation-tutorial-update-management.md#schedule-an-update-deployment)</ept>.

## <bpt id="p1">&lt;a name="hresult"&gt;</bpt><ept id="p1">&lt;/a&gt;</ept>Scenario: Machine shows as "Not assessed" and shows an HResult exception

### <a name="issue"></a>問題

* 您有在 [合規性] 底下顯示為 [未評估] 的機器，並在其下方看到例外狀況訊息。
* You have machines that show as not assessed.
* You see an HRESULT error code in the portal.

### <a name="cause"></a>原因

The Update Agent (Windows Update Agent on Windows; the package manager for a Linux distribution) isn't configured correctly. Update Management relies on the machine's Update Agent to provide the updates that are needed, the status of the patch, and the results of deployed patches. Without this information, Update Management can't properly report on the patches that are needed or installed.

### <a name="resolution"></a>解析度

Try to perform updates locally on the machine. If this fails, it typically means there's a configuration error with the update agent.

This problem is frequently caused by network configuration and firewall issues. 請嘗試下列作業：

* For Linux, check the appropriate documentation to make sure you can reach the network endpoint of your package repository.
* For Windows, check your agent configuration as listed in <bpt id="p1">[</bpt>Updates aren't downloading from the intranet endpoint (WSUS/SCCM)<ept id="p1">](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)</ept>.
  * If the machines are configured for Windows Update, make sure you can reach the endpoints described in <bpt id="p1">[</bpt>Issues related to HTTP/proxy<ept id="p1">](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)</ept>.
  * If the machines are configured for Windows Server Update Services (WSUS), make sure you can reach the WSUS server configured by the <bpt id="p1">[</bpt>WUServer registry key<ept id="p1">](/windows/deployment/update/waas-wu-settings)</ept>.

If you see an HRESULT, double-click the exception displayed in red to see the entire exception message. Review the following table for potential solutions or recommended actions:

|例外狀況  |Resolution or action  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Search the relevant error code in <bpt id="p1">[</bpt>Windows update error code list<ept id="p1">](https://support.microsoft.com/help/938205/windows-update-error-code-list)</ept> to find additional details about the cause of the exception.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | These indicate network connectivity issues. Make sure your machine has network connectivity to Update Management. See the <bpt id="p1">[</bpt>network planning<ept id="p1">](../automation-update-management.md#ports)</ept> section for a list of required ports and addresses.        |
|`0x8024001E`| The update operation didn't complete because the service or system was shutting down.|
|`0x8024002E`| Windows Update service is disabled.|
|`0x8024402C`     | If you're using a WSUS server, make sure the registry values for <ph id="ph1">`WUServer`</ph> and <ph id="ph2">`WUStatusServer`</ph> under the  <ph id="ph3">`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate`</ph> registry key specify the correct WSUS server.        |
|`0x80072EE2`|There's a network connectivity issue or an issue in talking to a configured WSUS server. Check WSUS settings and make sure the service is accessible from the client.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Make sure the Windows Update service (wuauserv) is running and not disabled.        |
|任何其他一般例外狀況     | Run a search on the internet for  possible solutions, and work with your local IT support.         |

Reviewing the Windowsupdate.log file can also help you determine possible causes. For more information about how to read the log, see <bpt id="p1">[</bpt>How to read the Windowsupdate.log file<ept id="p1">](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file)</ept>.

You can also download and run the <bpt id="p1">[</bpt>Windows Update troubleshooter<ept id="p1">](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)</ept> to check for any issues with Windows Update on the machine.

> [!NOTE]
> The <bpt id="p1">[</bpt>Windows Update troubleshooter<ept id="p1">](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)</ept> documentation indicates that it's for use on Windows clients, but it also works on Windows Server.

## <a name="scenario-update-run-returns-failed-status"></a>Scenario: Update run returns "Failed" status

### <a name="issue"></a>問題

An update run starts but encounters errors during the run.

### <a name="cause"></a>原因

可能的原因：

* Package manager is unhealthy.
* Update Agent (WUA for Windows, distro-specific package manager for Linux) is misconfigured.
* Specific packages are interfering with cloud-based patching.
* The machine is unreachable.
* Updates had dependencies that weren't resolved.

### <a name="resolution"></a>解析度

If failures occur during an update run after it starts successfully, <bpt id="p1">[</bpt>check the job output<ept id="p1">](../manage-update-multi.md#view-results-of-an-update-deployment)</ept> from the affected machine in the run. You might find specific error messages from your machines that you can research and take action on. 「更新管理」必須在套件管理員狀況良好的情況下，才能進行成功的更新部署。

If specific patches, packages, or updates are seen immediately before the job fails, you can try <bpt id="p1">[</bpt>excluding<ept id="p1">](../automation-tutorial-update-management.md#schedule-an-update-deployment)</ept> those from the next update deployment. To gather log info from Windows Update, see <bpt id="p1">[</bpt>Windows Update log files<ept id="p1">](/windows/deployment/update/windows-update-logs)</ept>.

If you can't resolve a patching issue, make a copy of the following log file and preserve it for troubleshooting purposes <bpt id="p1">*</bpt>before<ept id="p1">*</ept> the next update deployment starts:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>Patches aren't installed

### <a name="machines-dont-install-updates"></a>Machines don't install updates

* 請嘗試直接在機器上執行更新。 If the machine can't apply the updates, consult the <bpt id="p1">[</bpt>list of potential errors in the troubleshooting guide<ept id="p1">](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult)</ept>.
* If updates run locally, try removing and reinstalling the agent on the machine by following the guidance at <bpt id="p1">[</bpt>Remove a VM from Update Management<ept id="p1">](https://docs.microsoft.com/azure/automation/automation-update-management#remove-a-vm-from-update-management)</ept>.

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>I know updates are available, but they don't show as available on my machines

* This often happens if machines are configured to get updates from WSUS or System Center Configuration Manager (SCCM) but WSUS and SCCM haven't approved the updates.
* You can check whether the machines are configured for WSUS and SCCM by <bpt id="p1">[</bpt>cross-referencing the UseWUServer registry key to the registry keys in the "Configuring Automatic Updates by Editing the Registry" section of this article<ept id="p1">](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)</ept>.
* If updates aren't approved in WSUS, they won't be installed. You can check for unapproved updates in Log Analytics by running the following query:

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Updates show as installed, but I can't find them on my machine

* 更新常會被其他更新取代。 For more information, see <bpt id="p1">[</bpt>Update is superseded<ept id="p1">](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)</ept> in the Windows Update Troubleshooting guide.

### <a name="installing-updates-by-classification-on-linux"></a>依分類在 Linux 上安裝更新

* 依分類 (「重大與安全性更新」) 將更新部署至 Linux 時有重要事項須留意，尤其是針對 CentOS。 These limitations are documented on the <bpt id="p1">[</bpt>Update Management overview page<ept id="p1">](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)</ept>.

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 is consistently missing

* KB2267602 是 [Windows Defender 定義更新](https://www.microsoft.com/wdsi/definitions)。 It's updated daily.

## <a name="next-steps"></a>後續步驟

If you didn't see your problem or can't resolve your issue, try one of the following channels for additional support:

* Get answers from Azure experts through <bpt id="p1">[</bpt>Azure Forums<ept id="p1">](https://azure.microsoft.com/support/forums/)</ept>.
* Connect with <bpt id="p1">[</bpt><ph id="ph1">@AzureSupport</ph><ept id="p1">](https://twitter.com/azuresupport)</ept>, the official Microsoft Azure account for improving customer experience by connecting the Azure community to the right resources: answers, support, and experts.
* 提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
