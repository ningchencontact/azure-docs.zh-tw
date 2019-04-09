---
title: Azure 自動化中的 Runbook 執行
description: 描述如何處理 Azure 自動化中的 Runbook 的詳細資料。
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/03/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9d4661f6c975265ec710b29a8a05cc7ef41b4011
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057416"
---
# <a name="runbook-execution-in-azure-automation"></a>在 Azure 自动化中执行 Runbook

當您在 Azure 自動化中啟動 Runbook 時即會建立一個工作。 工作是 Runbook 的單一執行個體。 會指派 Azure 自動化背景工作，以執行每項工作。 雖然背景工作是由多個 Azure 帳戶共用，來自不同自動化帳戶的工作會彼此隔離。 您無法控制哪個背景工作要為您的作業要求提供服務。 單一 Runbook 一次可以執行許多工作。 來自相同自動化帳戶的作業執行環境可重複使用。 同時執行的作業越多，越常會分派到相同的沙箱。 在相同沙箱處理序中執行的作業可能會影響彼此，範例之一就是執行 `Disconnect-AzureRMAccount` Cmdlet。 執行此 Cmdlet 會中斷連接共用沙箱處理序中的每個 Runbook 作業。 當您在 Azure 入口網站中檢視 Runbook 清單時，它會列出已針對每個 Runbook 啟動之所有作業的狀態。 您可以檢視每一個 Runbook工作的清單，以追蹤每個 Runbook 的狀態。 作業記錄最多儲存 30 天。 如需不同作業狀態的說明，請參閱[作業狀態](#job-statuses)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

下图显示 [PowerShell Runbooks](automation-runbook-types.md#powershell-runbooks)、[图形 Runbook](automation-runbook-types.md#graphical-runbooks) 和 [PowerShell 工作流 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 的 Runbook 作业生命周期。

![工作狀態 -PowerShell 工作流程](./media/automation-runbook-execution/job-statuses.png)

藉由連線至您的 Azure 訂用帳戶，您的作業即可存取您的 Azure 資源。 如果可從公用雲端存取這些資源，他們將只可以存取您的資料中心中的資源。

## <a name="where-to-run-your-runbooks"></a>在何處執行您的 Runbook

Azure 自動化中的 Runbook 可以在 Azure 中的沙箱或[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)上執行。 沙箱是 Azure 中可供多個作業使用的共用環境。 使用相同沙箱的作業均受限於該沙箱的資源限制。 混合式 Runbook 背景工作角色可執行 runbook，直接在裝載角色的電腦上，針對要管理這些本機資源的環境中的資源。 Runbook 會儲存並在 Azure 自動化中管理，接著傳遞至一或多個指派的電腦。 在 Azure 沙箱時，可以輕鬆執行大部分的 runbook。 有一些特定案例可能會建議您在 Azure 沙箱上選擇混合式 Runbook 來執行 Runbook。 請參閱下表，以取得一些範例案例清單：

|Task|最佳選項|注意|
|---|---|---|
|與 Azure 資源整合|Azure 沙箱|裝載於 Azure 中，驗證會比較簡單。 如果您在 Azure VM 上使用混合式 Runbook 背景工作角色，則可改用 [Azure 資源的受控識別](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)|
|管理 Azure 資源的最佳效能|Azure 沙箱|執行指令碼在相同的環境中，依次具有較少的延遲|
|將營運成本最小化|Azure 沙箱|沒有任何計算額外負荷，不需針對 VM 使用|
|長時間執行指令碼|Hybrid Runbook Worker|Azure 沙箱會有[資源限制](../azure-subscription-service-limits.md#automation-limits)|
|與本機服務互動|Hybrid Runbook Worker|可以直接存取主機電腦|
|需要第三方軟體和可執行檔|Hybrid Runbook Worker|您要管理 OS 且可安裝軟體|
|使用 Runbook 監視檔案或資料夾|Hybrid Runbook Worker|在混合式 Runbook 背景工作角色上使用[監看員工作](automation-watchers-tutorial.md)|
|耗用大量資源的指令碼|Hybrid Runbook Worker| Azure 沙箱會有[資源限制](../azure-subscription-service-limits.md#automation-limits)|
|使用具特定需求的模組| Hybrid Runbook Worker|部分範例如下：</br> **WinSCP**：winscp.exe 上的相依性 </br> **IISAdministration**：需要啟用 IIS|
|安裝需要安裝程式的模組|Hybrid Runbook Worker|沙箱的模組必須是 copiable|
|使用需要 4.7.2 以外之 .NET Framework 的 Runbook 或模組|Hybrid Runbook Worker|自動化沙箱具備 .NET Framework 4.7.2，而且沒有任何方法可將它升級|
|需要提高權限的指令碼|Hybrid Runbook Worker|沙箱並不會允許提高權限。 若要解決這個問題，使用混合式 Runbook 背景工作角色，您可以關閉 UAC，並使用`Invoke-Command`時執行此命令需要提高權限|
|需要存取 WMI 的指令碼|Hybrid Runbook Worker|在沙箱中執行雲端作業[不能存取 WMI](#device-and-application-characteristics)|

## <a name="runbook-behavior"></a>Runbook 行為

Runbook 會根據定義於其內部的邏輯來執行。 如果 Runbook 中斷，該 Runbook 會從頭重新啟動。 需要將 Runbook 撰寫為支援在發生暫時性問題時可重新啟動，才能有此行為。

### <a name="creating-resources"></a>建立資源

如果您的指令碼會建立資源，您應該檢查以查看資源是否已經存在，然後嘗試重新建立它。 下列範例示範一個基本範例：

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzureRmResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exists, creating"
    New-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="time-dependant-scripts"></a>與時間相依的指令碼

編寫 Runbook 時應該仔細考量。 如先前所述，您需要將 Runbook 編寫為健全的，而且可以處理可能造成 Runbook 重新啟動或失敗的暫時性錯誤。 如果某个 Runbook 失败，将会重试该 Runbook。 如果 Runbook 在时间约束内正常运行，则应在 Runbook 中实现检查执行时间的逻辑，确保只在特定时间运行启动、关闭或横向扩展等操作。

### <a name="tracking-progress"></a>追蹤進度

最佳做法是本質上將 Runbook 編寫為模組。 這意味著在 Runbook 中建構邏輯，使其可輕鬆地重複使用和重新啟動。 跟踪 Runbook 的进度能够很好地确保在出现问题时正确执行 Runbook 中的逻辑。 部分可能用來追蹤 Runbook 進度的方式是使用外部來源，例如儲存體帳戶、資料庫或共用檔案。 通过在外部跟踪状态可以在 Runbook 中创建逻辑，用于先检查 Runbook 所执行的最后一个操作的状态。 然后根据结果跳过或继续执行 Runbook 中的特定任务。

### <a name="prevent-concurrent-jobs"></a>防止並行作業

某些 Runbook 如果跨多個作業同時執行，可能就會以奇怪的方式運作。 在此情況下，請務必實作邏輯進行檢查，以查看 Runbook 是否已經具有執行中的作業。 下列範例是示範如何執行此行為的基本範例：

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzureRmAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzureRmContext $AzureContext

# If then check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>使用多個訂用帳戶

您的 runbook 時撰寫處理多個訂用帳戶的 runbook，必須使用[Disable-azurermcontextautosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) cmdlet 以確認您的驗證內容不會擷取從可能的另一個 runbook在相同的沙箱中執行。 然後，您必須使用`-AzureRmContext`參數，在您`AzureRM`cmdlet 並將它傳遞適當的內容。

```powershell
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzureRmContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzureRmAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>處理例外狀況

创作脚本时，必须能够处理异常和潜在的间歇性故障。 下面是用于处理 Runbook 异常或间歇性问题的一些方法：

#### <a name="erroractionpreference"></a>$ErrorActionPreference

[$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) 首选项变量确定 PowerShell 如何对非终止性错误做出响应。 终止性错误不受 `$ErrorActionPreference` 的影响，它们始终会终止。 使用 `$ErrorActionPreference` 时，正常的非终止性错误（例如 `Get-ChildItem` cmdlet 返回的 `PathNotFound`）会阻止 Runbook 完成。 以下示例演示 `$ErrorActionPreference` 的用法。 因为脚本将会停止，最后一个 `Write-Output` 行永远不会执行。

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Try Catch Finally

在 PowerShell 脚本中使用 [Try Catch](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) 可以帮助处理终止性错误。 使用 Try Catch 可以捕获特定的异常或一般的异常。 应该使用 Catch 语句来跟踪错误或尝试处理错误。 以下示例尝试下载一个不存在的文件。 它捕获 `System.Net.WebException` 异常，如果存在另一个异常，则返回最后一个值。

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Throw

可以使用 [Throw](/powershell/module/microsoft.powershell.core/about/about_throw) 来生成终止性错误。 在 Runbook 中定义自己的逻辑时，此语句非常有用。 如果满足停止脚本的特定条件，则可以使用 `throw` 来停止脚本。 以下示例使用 `throw` 要求在计算机上提供某个函数参数。

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>使用可執行檔或呼叫處理序

在 Azure 沙盒中运行的 Runbook 不支持调用进程（例如 .exe 或 subprocess.call）。 这是因为，Azure 沙盒是在容器中运行的共享进程，它可能无权访问所有底层 API。 針對您需要第三方軟體或呼叫子處理序的案例，建議您在[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)上執行 Runbook。

### <a name="device-and-application-characteristics"></a>设备和应用程序特征

在 Azure 沙盒中运行的 Runbook 作业无权访问任何设备或应用程序特征。 最常用于在 Windows 上查询性能指标的 API 是 WMI。 其中的一些常见指标是内存和 CPU 使用率。 但是，使用哪个 API 并不重要。 在雲端執行的作業無法存取 Microsoft 實作的 Web 式企業管理 (WBEM)，內建在通用訊息模型 (CIM)，這是業界標準，用來定義裝置和應用程式的特性。

## <a name="job-statuses"></a>工作狀態

下表描述工作可能會有不同的狀態。 PowerShell 有兩種錯誤類型：終止和非終止錯誤。 如果發生終止錯誤，則會將 Runbook 狀態設為 [失敗]。 非終止錯誤可讓指令碼在錯誤發生後繼續執行。 非終止錯誤的範例會使用 `Get-ChildItem` Cmdlet 搭配不存在的路徑。 PowerShell 發現路徑不存在，則會擲回錯誤，並繼續下一步資料夾。 此錯誤不會將 Runbook 狀態設為 [失敗] 且可能標示為 [已完成]。 若要強制 Runbook 在非終止錯誤時停止，您可以在 Cmdlet 上使用 `-ErrorAction Stop`。

| 狀態 | 描述 |
|:--- |:--- |
| Completed |工作已成功完成。 |
| Failed |針對 [圖形化和 PowerShell 工作流程 Runbook](automation-runbook-types.md)，此 Runbook 無法編譯。 針對 [PowerShell 指令碼 Runbook](automation-runbook-types.md)，此 Runbook 無法啟動，或作業發生例外狀況。 |
| 處理失敗，正在等候資源 |工作失敗，因為其達到 [公平共用](#fair-share) 的三次上限，且每次從相同的檢查點或啟動 Runbook 開始。 |
| 已排队 |工作正在等候取得自動化背景工作中的資源，以便可啟動。 |
| 啟動中 |此作業已指派給背景工作角色，並且系統正在進行啟動。 |
| 繼續中 |工作暫停後，系統正在繼續工作。 |
| 執行中 |工作正在執行。 |
| 執行中，正在等候資源 |工作已卸載，因為已達到 [公平共用](#fair-share) 上限。 作業很快會從其上一個檢查點繼續。 |
| 已停止 |工作完成之前已由使用者停止。 |
| 停止中 |系統正在停止作業。 |
| 暫止 |工作已由使用者、系統或 Runbook 中的命令暫停。 如果 Runbook 沒有檢查點，它會從 Runbook 的開頭開始。 如果它有檢查點，則可重新啟動並從其最後一個檢查點繼續。 只有在發生例外狀況時，系統才會暫止 Runbook。 根據預設，ErrorActionPreference 會設定為 [繼續]，代表作業會在發生錯誤時繼續執行。 如果此喜好設定變數設定為 [停止]，作業會在發生錯誤時暫停。 只適用於 [圖形化和 PowerShell 工作流程 Runbook](automation-runbook-types.md) 。 |
| 暫停中 |因使用者要求，系統正在嘗試暫停工作。 Runbook 必須達到其下一個檢查點才能暫停。 如果它已通過其最後一個檢查點，則可在暫停之前完成。 只適用於 [圖形化和 PowerShell 工作流程 Runbook](automation-runbook-types.md) 。 |

## <a name="viewing-job-status-from-the-azure-portal"></a>從 Azure 入口網站檢視作業狀態

您可以檢視所有 Runbook 作業的摘要狀態，或在 Azure 入口網站中向下切入至特定 Runbook 作業的詳細資訊。 您也可以設定與 Log Analytics 工作區的整合，以轉送 Runbook 作業狀態和作業串流。 有关与 Azure Monitor 日志集成的详细信息，请参阅[将作业状态和作业流从自动化转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md)。

### <a name="automation-runbook-jobs-summary"></a>自動化 Runbook 作業摘要

在所選自動化帳戶的右邊，您可以在 [作業統計資料] 圖格下方，看到所有 Runbook 作業的摘要。

![[作業統計資料] 圖格](./media/automation-runbook-execution/automation-account-job-status-summary.png)

這個圖格會針對所有已執行的作業顯示作業狀態的計數和圖形表示。

按一下圖格，即會顯示 [作業] 頁面，其中包含所有已執行作業的摘要清單。 此頁面會顯示狀態、開始時間和完成時間。

![自動化帳戶的 [作業] 頁面](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

您可以篩選作業清單，方法是選取 [篩選作業]，然後篩選特定的 Runbook、作業狀態，或從下拉式清單中，選取要在其中進行搜尋的時間範圍。

![篩選作業狀態](./media/automation-runbook-execution/automation-account-jobs-filter.png)

或者，您可以檢視特定 Runbook 的作業摘要詳細資料，方法是在您的自動化帳戶中，從 [Runbook] 頁面中選取該 Runbook，然後選取 [作業] 圖格。 此動作會顯示 [作業] 頁面，而您可以從此處按一下作業記錄，以檢視其詳細資料和輸出。

![自動化帳戶的 [作業] 頁面](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>工作摘要

您可以檢視所有針對特定 Runbook 建立之作業的清單及其最新狀態。 您可以依工作狀態和上次工作變更的日期範圍來篩選此清單。 若要檢視其詳細資訊和輸出，按一下作業的名稱。 工作的詳細檢視包含提供給該工作的 Runbook 參數值。

您可以使用下列步驟來檢視 Runbook 工作。

1. 在 Azure 入口網站中，選取 [自動化]，然後選取自動化帳戶的名稱。
2. 從中樞選取 [Runbook]，然後在 [Runbook] 頁面上選取清單中的 Runbook。
3. 在已選取 Runbook 的頁面上，按一下 [作業] 圖格。
4. 按一下清單中的其中一個作業，然後可以在 [Runbook 作業詳細資料] 頁面上檢視其詳細資料和輸出。

## <a name="retrieving-job-status-using-powershell"></a>使用 PowerShell 擷取作業狀態

您可以使用 [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob)，來擷取針對 Runbook 建立的作業以及特定作業的詳細資料。 如果您使用 PowerShell 啟動 runbook [Start-azurermautomationrunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook)，則它會傳回產生的作業。 使用 [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) 來取得作業的輸出。

下列範例命令會針對範例 Runbook 擷取最後一個作業並顯示其狀態、提供給 Runbook 參數的值，以及來自作業的輸出。

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

下列範例會擷取特定作業的輸出，並傳回每一筆記錄。 在其中一筆記錄含有例外狀況的情況下，會寫出該例外狀況，而不是值。 此行為非常有用，因為例外狀況可以提供通常可能不會在輸出期間記錄的額外資訊。

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="get-details-from-activity-log"></a>從活動記錄取得詳細資料

您可以從自動化帳戶的活動記錄中擷取其他詳細資料 (例如啟動 Runbook 的人員或帳戶)。 下列 PowerShell 範例提供最後一位執行上述 Runbook 的使用者：

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzureRmLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzureRmResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    { 
        # Get runbook
        $Runbook = Get-AzureRmAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hash table
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>公平共用

为了在云中的所有 Runbook 之间共享资源，Azure 自动化会暂时卸载或停止已运行三小时以上的所有作业。 [PowerShell 型 Runbook](automation-runbook-types.md#powershell-runbooks) 和 [Python Runbook](automation-runbook-types.md#python-runbooks) 的作業會停止且不會重新啟動，而作業狀態顯示 [已停止]。

對於長時間執行的工作，建議使用[混合式 Runbook 背景工作角色](automation-hrw-run-runbooks.md#job-behavior)。 混合式 Runbook 背景工作角色並未受限於公平共用，而且未限制 Runbook 執行時間長度。 其他作業[限制](../azure-subscription-service-limits.md#automation-limits)會套用至 Azure 沙箱和混合式 Runbook 背景工作角色。 雖然混合式 Runbook 背景工作角色不受限於 3 小時公平共用的限制，在其上執行的 runbook 應該加以開發以支援從非預期的本機基礎結構問題的重新啟動行為。

另一個選項是使用子 Runbook 將 Runbook 最佳化。 如果您的 Runbook 會在多個資源上重複執行同一個函式，例如在數個資料庫上重複進行某一個資料庫作業，您可以將該函式移到[子 Runbook](automation-child-runbooks.md) 並使用 [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) Cmdlet 呼叫它。 每一個 Runbook 會在個別的處理程序中平行執行。 此行為可減少完成父代 Runbook 的時間總計。 如果有在子 Runbook 完成后执行的操作，可使用 Runbook 中的 [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) cmdlet 检查每个子 Runbook 的作业状态。

## <a name="next-steps"></a>後續步驟

* 若要深入了解可在 Azure 自動化中用來啟動 Runbook 的不同方法，請參閱[在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md)

