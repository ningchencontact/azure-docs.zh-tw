---
title: Azure 自動化中的 Runbook 執行
description: 描述如何處理 Azure 自動化中的 Runbook 的詳細資料。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 10/30/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bb6236203a1165361505c8699ba94bff54e41c2a
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2018
ms.locfileid: "50247345"
---
# <a name="runbook-execution-in-azure-automation"></a>Azure 自動化中的 Runbook 執行

當您在 Azure 自動化中啟動 Runbook 時即會建立一個工作。 工作是 Runbook 的單一執行個體。 會指派 Azure 自動化背景工作，以執行每項工作。 雖然背景工作是由多個 Azure 帳戶共用，來自不同自動化帳戶的工作會彼此隔離。 您無法控制哪個背景工作要為您的作業要求提供服務。 單一 Runbook 一次可以執行許多工作。 來自相同自動化帳戶的作業執行環境可重複使用。 同時執行的作業越多，越常會分派到相同的沙箱。 在相同沙箱處理序中執行的作業可能會影響彼此，範例之一就是執行 `Disconnect-AzureRMAccount` Cmdlet。 執行此 Cmdlet 會中斷連接共用沙箱處理序中的每個 Runbook 作業。 當您在 Azure 入口網站中檢視 Runbook 清單時，它會列出已針對每個 Runbook 啟動之所有作業的狀態。 您可以檢視每一個 Runbook工作的清單，以追蹤每個 Runbook 的狀態。 作業記錄會儲存最多 30 天。 如需不同作業狀態的說明，請參閱[作業狀態](#job-statuses)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

下圖顯示[圖形化 Runbook](automation-runbook-types.md#graphical-runbooks) 和 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 之 Runbook 作業的生命週期。

![工作狀態 -PowerShell 工作流程](./media/automation-runbook-execution/job-statuses.png)

下圖顯示 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)之 Runbook 工作的生命週期。

![工作狀態 - PowerShell 指令碼](./media/automation-runbook-execution/job-statuses-script.png)

藉由連線至您的 Azure 訂用帳戶，您的作業即可存取您的 Azure 資源。 如果可從公用雲端存取這些資源，他們將只可以存取您的資料中心中的資源。

## <a name="job-statuses"></a>工作狀態

下表描述工作可能會有不同的狀態。 PowerShell 有兩種錯誤類型：終止和非終止錯誤。 如果發生終止錯誤，則會將 Runbook 狀態設為 [失敗]。 非終止錯誤可讓指令碼在錯誤發生後繼續執行。 非終止錯誤的範例會使用 `Get-ChildItem` Cmdlet 搭配不存在的路徑。 PowerShell 發現路徑不存在，則會擲回錯誤，並繼續下一步資料夾。 此錯誤不會將 Runbook 狀態設為 [失敗] 且可能標示為 [已完成]。 若要強制 Runbook 在非終止錯誤時停止，您可以在 Cmdlet 上使用 `-ErrorAction Stop`。

| 狀態 | 說明 |
|:--- |:--- |
| Completed |工作已成功完成。 |
| Failed |針對 [圖形化和 PowerShell 工作流程 Runbook](automation-runbook-types.md)，此 Runbook 無法編譯。 針對 [PowerShell 指令碼 Runbook](automation-runbook-types.md)，此 Runbook 無法啟動，或作業發生例外狀況。 |
| 處理失敗，正在等候資源 |工作失敗，因為其達到 [公平共用](#fair-share) 的三次上限，且每次從相同的檢查點或啟動 Runbook 開始。 |
| 已排入佇列 |工作正在等候取得自動化背景工作中的資源，以便可啟動。 |
| 啟動中 |此作業已指派給背景工作角色，並且系統正在進行啟動。 |
| 繼續中 |工作暫停後，系統正在繼續工作。 |
| 執行中 |工作正在執行。 |
| 執行中，正在等候資源 |工作已卸載，因為已達到 [公平共用](#fair-share) 上限。 作業很快會從其上一個檢查點繼續。 |
| 已停止 |工作完成之前已由使用者停止。 |
| 停止中 |系統正在停止作業。 |
| 暫止 |工作已由使用者、系統或 Runbook 中的命令暫停。 如果 Runbook 沒有檢查點，它會從 Runbook 的開頭開始。 如果它有檢查點，則可重新啟動並從其最後一個檢查點繼續。 只有在發生例外狀況時，系統才會暫停 Runbook。 根據預設，ErrorActionPreference 會設定為 [繼續]，代表作業會在發生錯誤時繼續執行。 如果此喜好設定變數設定為 [停止]，作業會在發生錯誤時暫停。 只適用於 [圖形化和 PowerShell 工作流程 Runbook](automation-runbook-types.md) 。 |
| 暫停中 |因使用者要求，系統正在嘗試暫停工作。 Runbook 必須達到其下一個檢查點才能暫停。 如果它已通過其最後一個檢查點，則可在暫停之前完成。 只適用於 [圖形化和 PowerShell 工作流程 Runbook](automation-runbook-types.md) 。 |

## <a name="viewing-job-status-from-the-azure-portal"></a>從 Azure 入口網站檢視作業狀態

您可以檢視所有 Runbook 作業的摘要狀態，或在 Azure 入口網站中向下切入至特定 Runbook 作業的詳細資訊。 您也可以設定與 Log Analytics 工作區的整合，以轉送 Runbook 作業狀態和作業串流。 如需與 Log Analytics 整合的詳細資訊，請參閱[從自動化將作業狀態和作業串流轉送到 Log Analytics](automation-manage-send-joblogs-log-analytics.md)。

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

您可以針對特定 Runbook 及其最新狀態，檢視所有已建立工作的清單。 您可以依工作狀態和上次工作變更的日期範圍來篩選此清單。 若要檢視其詳細資訊和輸出，請按一下作業的名稱。 工作的詳細檢視包含提供給該工作的 Runbook 參數值。

您可以使用下列步驟來檢視 Runbook 工作。

1. 在 Azure 入口網站中，選取 [自動化]，然後選取自動化帳戶的名稱。
2. 從中樞選取 [Runbook]，然後在 [Runbook] 頁面上選取清單中的 Runbook。
3. 在已選取 Runbook 的頁面上，按一下 [作業] 圖格。
4. 按一下清單中的其中一個作業，然後在 [Runbook 作業詳細資料] 頁面，您可以檢視其詳細資料與輸出。

## <a name="retrieving-job-status-using-windows-powershell"></a>使用 Windows PowerShell 擷取工作狀態

您可以使用 [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob)，來擷取針對 Runbook 建立的作業以及特定作業的詳細資料。 如果您使用 [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook)，透過 Windows PowerShell 來啟動 Runbook，則其會傳回產生的作業。 使用 [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) 來取得作業的輸出。

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
$rg = "ResourceGroup01"
$AutomationAccount = "MyAutomationAccount"
$RunbookName = "Test-Runbook"
$JobResourceID = "/subscriptions/$subid/resourcegroups/$rg/providers/Microsoft.Automation/automationAccounts/$AutomationAccount/jobs"

Get-AzureRmLog -ResourceId $JobResourceID -MaxRecord 1 | Select Caller
```

## <a name="fair-share"></a>公平共用

為了在雲端中的所有 Runbook 之間共用資源，Azure 自動化會暫時卸載或停止任何已執行超過 3 小時的作業。 [PowerShell 型 Runbook](automation-runbook-types.md#powershell-runbooks) 和 [Python Runbook](automation-runbook-types.md#python-runbooks) 的作業會停止且不會重新啟動，而作業狀態顯示 [已停止]。

對於長時間執行的工作，建議使用[混合式 Runbook 背景工作角色](automation-hrw-run-runbooks.md#job-behavior)。 混合式 Runbook 背景工作角色未受限於公平共用，而且未限制 Runbook 執行時間長度。 其他作業[限制](../azure-subscription-service-limits.md#automation-limits)會套用至 Azure 沙箱和混合式 Runbook 背景工作角色。 雖然混合式 Runbook 背景工作角色不會受限於 3 小時的公平共用限制，但是仍應開發在其上執行的 Runbook 以支援從非預期本機基礎結構問題重新啟動的行為。

另一個選項是使用子 Runbook 將 Runbook 最佳化。 如果您的 Runbook 在多個資源上重複同一函式，例如在數個資料庫上重複一項資料庫作業，您可以將該函式移到[子 Runbook](automation-child-runbooks.md) 並使用 [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) cmdlet 呼叫它。 每個子 Runbook 會在個別程序中平行執行，以減少父 Runbook 完成的總時間量。 如果有需要在子 Runbook 完成後執行的作業，您可以使用在 Runbook 中 [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) Cmdlet 來檢查每個子項目的作業狀態。

## <a name="next-steps"></a>後續步驟

* 若要深入了解可在 Azure 自動化中用來啟動 Runbook 的不同方法，請參閱[在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md)
