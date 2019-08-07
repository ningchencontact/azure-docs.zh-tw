---
title: 針對更新管理的相關錯誤進行疑難排解
description: 了解如何針對更新管理問題進行疑難排解
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 884ded67c25aca78225baef2d7e4c5de1cc94fd0
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782280"
---
# <a name="troubleshooting-issues-with-update-management"></a>針對更新管理問題進行疑難排解

本文探討您使用「更新管理」時可能遇到之問題的解決方案。

混合式背景工作角色代理程式有代理程式疑難排解員可判斷根本問題。 若要深入了解疑難排解員，請參閱[針對更新代理程式問題進行疑難排解](update-agent-issues.md)。 對於所有其他問題，請參閱以下有關可能問題的詳細資訊。

## <a name="general"></a>一般

### <a name="components-enabled-not-working"></a>案例：已啟用「更新管理」解決方案的元件，而此虛擬機器現在正在設定中

#### <a name="issue"></a>問題

上架後 15 分鐘，您會繼續在虛擬機器上看見下列訊息：

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>原因

這個錯誤可能是下列原因所造成：

1. 正在封鎖連回自動化帳戶的通訊。
2. 所要上線的 VM 可能來自一部已安裝 Microsoft Monitoring Agent 但未執行過 sysprep 的所複製機器。

#### <a name="resolution"></a>解決方法

1. 請瀏覽[網路規劃](../automation-hybrid-runbook-worker.md#network-planning)，了解必須允許哪些位址和連接埠，才能進行更新管理。
2. 若使用複製的映像：
   1. 在您的 Log Analytics 工作區中, 從已儲存的搜尋範圍`MicrosoftDefaultScopeConfig-Updates`設定中移除 VM (若有顯示)。 您可以在工作區中的 [一般] 底下找到儲存的搜尋。
   2. 執行 `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`
   3. 執行 `Restart-Service HealthService` 以重新啟動 `HealthService`。 這將會重建金鑰並產生新的 UUID。
   4. 如果無法解決問題, 請先對映射執行 sysprep, 然後再安裝 MMA 代理程式。

### <a name="multi-tenant"></a>案例：您在另一個 Azure 租用戶中建立機器的更新部署時，發生連結訂用帳戶錯誤。

#### <a name="issue"></a>問題

您在另一個 Azure 租用戶中嘗試為機器建立更新部署時，發生下列錯誤：

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>原因

當您建立的更新部署中有 Azure 虛擬機器位在某個更新部署中的其他租用戶內，就會發生此錯誤。

#### <a name="resolution"></a>解決方法

請採取以下因應措施將之安排妥當。 您可以搭配參數 `-ForUpdate` 使用 [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) Cmdlet，藉此建立排程，然後使用 [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) Cmdlet，將其他租用戶中的機器傳遞到 `-NonAzureComputer` 參數。 下列範例示範了做法：

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="nologs"></a>案例：電腦不會顯示在入口網站的 更新管理

#### <a name="issue"></a>問題

您可以在下列案例中執行:

* 您的機器顯示未從 VM 的更新管理視圖進行**設定**

* 您的電腦在您的自動化帳戶的更新管理視圖中遺失

* 您的機器在**合規性**之下會顯示為 [**未評估**], 但您會在混合式 Runbook 背景工作角色的 Azure 監視器記錄中看到心跳資料, 但不會更新管理。

#### <a name="cause"></a>原因

這可能是因可能的本機設定問題, 或設定不正確的範圍而造成。

混合式 Runbook 背景工作角色可能需要重新註冊及重新安裝。

您可能已在工作區中定義已達到的配額, 並停止儲存資料。

#### <a name="resolution"></a>解決方法

* 請確定您的電腦已回報至正確的工作區。 確認您的機器回報的工作區。 如需有關如何驗證這種情況的指示, 請參閱[確認代理程式與 Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics)的連線能力。 然後, 確定這是連結到您 Azure 自動化帳戶的工作區。 若要確認這一點, 請流覽至您的自動化帳戶, 然後按一下 **相關資源** 底下的 **連結工作區**

* 檢查以確定電腦出現在您的 Log Analytics 工作區中。 在與您的自動化帳戶連結的 Log Analytics 工作區中, 執行下列查詢。 如果您在查詢結果中看不到您的電腦, 表示您的電腦未進行心跳, 這代表很可能是本機設定的問題。 您可以根據作業系統執行[Windows](update-agent-issues.md#troubleshoot-offline)或[Linux](update-agent-issues-linux.md#troubleshoot-offline)的疑難排解員, 也可以[重新安裝代理程式](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)。 如果您的電腦顯示在查詢結果中, 則您必須在下列專案符號中指定範圍設定。

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```

* 檢查範圍設定問題。 [範圍](../automation-onboard-solutions-from-automation-account.md#scope-configuration)設定會決定哪些機器會針對解決方案進行設定。 如果您的電腦顯示在您的工作區中, 但未顯示, 您必須將範圍設定設定為以電腦為目標。 若要瞭解如何執行這項操作, 請參閱在[工作區中將電腦上架](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)。

* 如果上述步驟無法解決您的問題, 請遵循[部署 Windows 混合式 runbook 背景工作角色](../automation-windows-hrw-install.md)中的步驟來重新安裝適用于 Windows 的混合式背景工作角色, 或部署 Linux 的[Linux 混合式 runbook 背景工作](../automation-linux-hrw-install.md)角色。

* 在您的工作區中, 執行下列查詢。 如果您看到結果`Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` , 表示您已在工作區上定義配額, 並已停止儲存資料。 在您的工作區中, 流覽至 [**使用量和估計成本** > ] [**資料量管理**], 並檢查您的配額或移除您擁有的配額。

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

## <a name="windows"></a>Windows

如果您在嘗試讓解決方案在虛擬機器上線時遇到問題，請查看本機電腦上 [應用程式及服務記錄] 底下的 [Operations Manager] 事件記錄中，是否有事件識別碼為 **4502** 且事件訊息包含 **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** 的事件。

下節特別說明特定的錯誤訊息，以及每個錯誤訊息的可能解決方式。 如需了解其他上線問題，請參閱[針對解決方案上線問題進行疑難排解](onboarding.md)。

### <a name="machine-already-registered"></a>案例：電腦已經註冊至不同的帳戶

#### <a name="issue"></a>問題

您收到下列錯誤訊息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>原因

電腦已經在另一個工作區上線以供進行「更新管理」。

#### <a name="resolution"></a>解決方法

在電腦上藉由[刪除混合式 Runbook 群組](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)來清除舊成品，然後再試一次。

### <a name="machine-unable-to-communicate"></a>案例：電腦無法與服務進行通訊

#### <a name="issue"></a>問題

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

#### <a name="cause"></a>原因

可能有 Proxy、閘道或防火牆封鎖網路通訊。

#### <a name="resolution"></a>解決方法

檢閱您的網路並確定已允許適當的連接埠和位址。 如需「更新管理」和「混合式 Runbook 背景工作角色」所需的連接埠和位址清單，請參閱[網路需求](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="unable-to-create-selfsigned-cert"></a>案例：無法建立自我簽署憑證

#### <a name="issue"></a>問題

您會收到下列其中一個錯誤訊息：

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>原因

「混合式 Runbook 背景工作角色」無法產生自我簽署憑證

#### <a name="resolution"></a>解決方法

確認系統帳戶具有 **C:\ProgramData\Microsoft\Crypto\RSA** 資料夾的讀取存取權，然後再試一次。

### <a name="failed-to-start"></a>案例：電腦顯示無法在更新部署中啟動

#### <a name="issue"></a>問題

機器的狀態為 [**無法啟動**]。 當您查看電腦的特定詳細資料時, 您會看到下列錯誤:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

#### <a name="cause"></a>原因

此錯誤可能是因為下列其中一個原因而發生:

* 機器不再存在。
* 機器已關閉且無法連線。
* 電腦發生網路連線問題, 無法連線到電腦上的混合式背景工作角色。
* 已變更 SourceComputerId 的 Microsoft Monitoring Agent 更新
* 如果您在自動化帳戶中達到2000的並行作業限制, 則您的更新執行可能已節流。 系統會將每個部署視為一項作業, 而更新部署中的每一部機器都會計算為一項作業。 目前在您的自動化帳戶中執行的任何其他自動化作業或更新部署都會計入並行作業限制。

#### <a name="resolution"></a>解決方法

適用時, 請將[動態群組](../automation-update-management.md#using-dynamic-groups)用於更新部署。

* 確認電腦仍然存在且可連線。 如果不存在, 請編輯您的部署, 並移除電腦。
* 請參閱[網路規劃](../automation-update-management.md#ports)一節, 以取得更新管理所需的埠和地址清單, 並確認您的電腦符合這些需求。
* 在 Log Analytics 中執行下列查詢, 以找出環境`SourceComputerId`中已變更的電腦。 尋找具有相同`Computer`值但值不同`SourceComputerId`的電腦。 一旦找到受影響的機器之後, 您必須編輯以這些機器為目標的更新部署, 並移除並重新新增機器, 使`SourceComputerId`反映正確的值。

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

### <a name="hresult"></a>案例：機器顯示為 [未評估] 並顯示 HResult 例外狀況

#### <a name="issue"></a>問題

您有在 [合規性] 底下顯示為 [未評估] 的機器，並在其下方看到例外狀況訊息。

#### <a name="cause"></a>原因

機器中的 Windows Update 或 WSUS 設定不正確。 「更新管理」依賴 Windows Update 或 WSUS 提供所需的更新、修補程式的狀態，以及所部署修補程式的結果。 若無此資訊，「更新管理」無法正確回報是否需要或安裝了修補程式。

#### <a name="resolution"></a>解決方法

按兩下以紅色顯示的例外狀況，以查看整個例外狀況訊息。 檢閱下表以了解可採取的可能解決方案或動作：

|例外狀況  |解決方案或動作  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | 在 [Windows 更新錯誤碼清單](https://support.microsoft.com/help/938205/windows-update-error-code-list)中搜尋相關的錯誤碼，以尋找有關例外狀況原因的額外詳細資料。        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | 這些錯誤是網路連線問題。 請確定您的機器有適當的網路連線能力來連線到「更新管理」。 如需所需的連接埠和位址清單，請參閱有關[網路規劃](../automation-update-management.md#ports)的小節。        |
|`0x8024001E`| 更新作業未完成, 因為服務或系統已關閉。|
|`0x8024002E`| Windows Update 服務已停用。|
|`0x8024402C`     | 如果您使用 WSUS 伺服器，請確定 `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` 登錄機碼底下 `WUServer` 和 `WUStatusServer` 的登錄值具有正確的 WSUS 伺服器。        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | 確定 Windows Update 服務 (wuauserv) 正在執行而未停用。        |
|任何其他一般例外狀況     | 搜尋網際網路來尋找可能的解決方案，並與您當地的 IT 支援人員合作。         |

您也`windowsupdate.log`可以查看, 協助您嘗試判斷可能的原因。 如需如何讀取記錄檔的詳細資訊, 請參閱[如何讀取 windowsupdate.log。](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file)

此外，您可以下載並執行 [Windows Update 疑難排解員](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)，以檢查機器上的 Windows Update 是否有任何問題。

> [!NOTE]
> [Windows Update 疑難排解員](https://support.microsoft.com/help/4027322/windows-update-troubleshooter)指出其適用標的為 Windows 用戶端，但實際上也可在 Windows Server 上運作。

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>案例：更新執行無法開始

#### <a name="issue"></a>問題

更新執行無法在 Linux 電腦上開始。

#### <a name="cause"></a>原因

「Linux 混合式背景工作角色」狀況不良。

#### <a name="resolution"></a>解決方法

複製以下記錄檔並將其保留供疑難排解使用：

```bash
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>案例：更新執行開始，但發生錯誤

#### <a name="issue"></a>問題

更新執行開始，但在執行時發生錯誤。

#### <a name="cause"></a>原因

可能的原因：

* 套件管理員狀況不良
* 特定套件可能干擾雲端式修補
* 其他原因

#### <a name="resolution"></a>解決方法

如果在更新執行於 Linux 上成功開始後，於更新執行期間發生失敗，請查看來自該執行中受影響電腦的工作輸出。 您可能可以從電腦套件管理員找到可供研究和採取動作的特定錯誤訊息。 「更新管理」必須在套件管理員狀況良好的情況下，才能進行成功的更新部署。

在某些情況下，套件更新可能干擾「更新管理」，而導致更新部署無法完成。 如果看到此情況，您將必須將這些套件從未來的更新執行中排除，或自行手動安裝這些套件。

如果您無法解決修補問題，請在下一個更新部署開始**之前**，先複製以下記錄檔並將其保留供疑難排解使用：

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

### <a name="other"></a>案例：並未列出我的問題

### <a name="issue"></a>問題

您有未由列出的其他案例解決的問題。

### <a name="cause"></a>原因

設定錯誤或遺失的登錄機碼可能會造成更新管理的問題。

### <a name="resolution"></a>解決方法

刪除登錄機碼`HKLM:\SOFTWARE\Microsoft\HybridRunbookWorker` , 然後重新開機**HealthService**。

您也可以使用下列 PowerShell 命令。

```powershell
Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
Restart-Service healthservice
```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答
* 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫－專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
* 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
