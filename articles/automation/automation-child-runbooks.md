---
title: Azure 自動化中的子 Runbook
description: 說明在 Azure 自動化中從另一個 Runbook 啟動 Runbook，以及在它們之間共用資訊的不同方法。
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 37cf44e2c9d28b1aac8f2ab80ba29d126fb8651f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2019
ms.locfileid: "54422963"
---
# <a name="child-runbooks-in-azure-automation"></a>Azure 自動化中的子 Runbook

Azure 自動化中的建議作法是撰寫可重複使用、模組化的 Runbook，並包含其他 Runbook 所使用的不同功能。 父 Runbook 通常會呼叫一或多個子 Runbook 來執行必要的功能。 有兩種方式可以呼叫子 Runbook，而且各有您應該了解的明顯差異，如此您才能判斷在不同的情況下哪一種最適用。

## <a name="invoking-a-child-runbook-using-inline-execution"></a>使用內嵌執行叫用子 Runbook

若要從其他 Runbook 叫用 Runbook 內嵌，請使用 Runbook 的名稱並為其提供參數值，如同您使用活動或 Cmdlet 時一樣。  在相同自動化帳戶中的所有 Runbook 均可以此方式提供所有其他 Runbook 使用。 父 Runbook 會等候子 Runbook 完成，才會移動到下一行，而且任何輸出都會直接回傳到父代。

當您叫用 Runbook 內嵌時，它會在父 Runbook 所在的相同工作中執行。 其執行之子 Runbook 的工作歷程記錄中不會指示。 來自子 Runbook 的任何例外狀況及任何資料流輸出都會與父代相關聯。 這個行為會減少工作數目，並使工作更容易追蹤和疑難排解，因為子 Runbook 擲回的任何例外狀況及其任何資料流輸出，都與父 Runbook 的工作相關聯。

發佈 Runbook 時，其呼叫的所有子 Runbook 都必須是已發佈的。 這是因為在編譯 Runbook 時，Azure 自動化會建置與任何子 Runbook 的關聯。 如果沒有，父 Runbook 會顯示正常發佈，但在啟動時會產生例外狀況。 如果發生這種情況，您可以重新發佈父 Runbook 以正確參照子 Runbook。 因為已經建立關聯，所以如果任何子 Runbook 有變更，您都不需要重新發佈父 Runbook。

以內嵌方式呼叫的子 Runbook 之參數可以是任何資料類型 (包括複雜物件)。 沒有您使用 Azure 入口網站或使用 Start-AzureRmAutomationRunbook Cmdlet 啟動 Runbook 時的 [JSON 序列化](automation-starting-a-runbook.md#runbook-parameters)。

### <a name="runbook-types"></a>Runbook 類型

哪些類型可以彼此呼叫：

* [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 和[圖形化 Runbook](automation-runbook-types.md#graphical-runbooks) 可以內嵌方式呼叫彼此 (這兩者都是 PowerShell)。
* [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 和圖形化 PowerShell 工作流程 Runbook 可以內嵌方式呼叫彼此 (這兩者都是 PowerShell 工作流程)。
* PowerShell 類型與 PowerShell 工作流程類型無法以內嵌方式彼此呼叫，而且必須使用 Start-AzureRmAutomationRunbook。

何時會與發行順序有關：

* Runbook 的發行順序只對 PowerShell 工作流程和圖形化 PowerShell 工作流程 Runbook 有關係。

在呼叫使用內嵌執行的圖形化或 PowerShell 工作流程子 Runbook 時，您要使用 Runbook 的名稱。  當您呼叫 PowerShell 子系 Runbook 時，名稱必須以 *.\\* 開頭，以指定指令碼位於本機目錄中。

### <a name="example"></a>範例

下列範例會啟動一個測試子 Runbook，它會接受三個參數、一個複雜物件、一個整數和一個布林值。 子 Runbook 的輸出會指派給一個變數。  此案例的子 Runbook 是 PowerShell 工作流程 Runbook。

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

以下是使用 PowerShell Runbook 做為子 Runbook 的相同範例。

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>使用 Cmdlet 啟動子 Runbook

> [!IMPORTANT]
> 如果您要使用 `Start-AzureRmAutomationRunbook` Cmdlet 與 `-Wait` 參數來叫用子 Runbook，而且子 Runbook 的結果是物件，則可能會遇到錯誤。 若要解決這個錯誤，請參閱[子 Runbook 與物件輸出](troubleshoot/runbooks.md#child-runbook-object)，以了解如何實作邏輯來輪詢結果，並使用 [Get-AzureRmAutomationJobOutputRecord](/powershell/module/azurerm.automation/get-azurermautomationjoboutputrecord)

您可以依照[使用 Windows PowerShell 啟動 Runbook](automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell) 中的說明，使用 [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) Cmdlet 啟動 Runbook。 使用這個 Cmdlet 的模式有兩種。  在第一個模式中，Cmdlet 會在子 Runbook 的子作業建立時傳回作業識別碼。  在第二個模式 (藉由指定 **-wait** 參數啟用) 中，Cmdlet 會等候子作業完成，並且會傳回子 Runbook 的輸出。

使用 Cmdlet 啟動之子 Runbook 的工作，會與父 Runbook 的工作分開執行。 這個行為會讓產生的作業比啟動內嵌 Runbook 更多，並使它們更難以追蹤。父 Runbook 可以利用非同步方式啟動多個子 Runbook，不需要等候每個子 Runbook 完成。 對於呼叫子 Runbook 內嵌的同類型平行執行作業，父 Runbook 將需要使用 [平行關鍵字](automation-powershell-workflow.md#parallel-processing)。

子 Runbook 的輸出會因為計時，而無法可靠地傳回至父 Runbook。 此外，$VerbosePreference、$WarningPreference 等有些變數和其他變數可能不會傳播至子 Runbook。 若要避免這些問題，您可以使用 `Start-AzureRmAutomationRunbook` Cmdlet 搭配 `-Wait` 參數，將子 Runbook 當作個別的自動化作業來啟動。 這會在子 Runbook 完成前封鎖父 Runbook。

如果您不希望父 Runbook 在等待時遭到封鎖，您可以使用 `Start-AzureRmAutomationRunbook` Cmdlet (沒有 `-Wait` 參數) 啟動子 Runbook。 然後您必須使用 `Get-AzureRmAutomationJob` 等候工作完成，以及使用 `Get-AzureRmAutomationJobOutput` 和 `Get-AzureRmAutomationJobOutputRecord` 來擷取結果。

使用 Cmdlet 啟動之子 Runbook 的參數是以雜湊表方式提供，如 [Runbook 參數](automation-starting-a-runbook.md#runbook-parameters)中所述。 只能使用簡單資料類型。 若 Runbook 有複雜資料類型的參數，必須以內嵌方式呼叫。

以個別作業的形式啟動子 Runbook 時，可能會遺失訂用帳戶內容。 若要讓讓子 Runbook 對特定的 Azure 訂用帳戶執行 Azure RM Cmdlet，子 Runbook 必須在父 Runbook 以外單獨對此訂用帳戶進行驗證。

如果相同自動化帳戶中的作業使用多個訂用帳戶，則在某個作業中選取訂用帳戶時，可能會變更其他作業目前選取的訂用帳戶內容。 若要避免這個問題，請在每個 Runbook 的開頭使用 `Disable-AzureRmContextAutosave –Scope Processsave`。 此動作只會將內容儲存至該 Runbook 執行。

### <a name="example"></a>範例

下列範例使用參數啟動子 Runbook，然後使用 Start-AzureRmAutomationRunbook -wait 參數等待其完成。 完成後，系統會從子 Runbook 收集其輸出。 若要使用 `Start-AzureRmAutomationRunbook` 命令，您必須驗證您的 Azure 訂用帳戶。

```azurepowershell-interactive
# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -DefaultProfile $AzureContext `
    –Parameters $params –wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>子 Runbook 的呼叫方法比較

下表摘要說明從另一個 Runbook 呼叫 Runbook 之兩種方法之間的差異。

|  | 內嵌 | Cmdlet |
|:--- |:--- |:--- |
| 工作 (Job) |與父代在相同的工作中執行的子 Runbook。 |會為子 Runbook 建立個別的工作。 |
| 執行 |父 Runbook 會等待子 Runbook 完成後再繼續執行。 |父 Runbook 會在子 Runbook 啟動後立刻繼續執行，或  父 Runbook 會等候子作業完成。 |
| 輸出 |父 Runbook 可以直接從子 Runbook 取得輸出。 |父 Runbook 必須擷取子 Runbook 作業的輸出，或  父 Runbook 可以直接從子 Runbook 取得輸出。 |
| 參數 |子 Runbook 參數的值是個別指定，而且可以使用任何資料類型。 |子 Runbook 參數的值必須結合成單一雜湊表。 這個雜湊表只能包含簡單、陣列，以及使用 JSON 序列化的物件資料類型。 |
| 自動化帳戶 |父 Runbook 只能使用相同自動化帳戶中的子 Runbook。 |父 Runbook 可以使用來自相同 Azure 訂用帳戶，甚至是不同訂用帳戶 (您已連線的) 之任何自動化帳戶的子 Runbook。 |
| 發佈 |發佈父 Runbook 之前必須先發佈子 Runbook。 |啟動父 Runbook 之前必須先發佈子 Runbook。 |

## <a name="next-steps"></a>後續步驟

* [在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md)
* [Azure 自動化中的 Runbook 輸出與訊息](automation-runbook-output-and-messages.md)

