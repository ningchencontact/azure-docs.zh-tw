---
title: 管理 Azure 自動化中的 Runbook
description: 本文描述如何管理 Azure 自動化中的 Runbook。
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: da3b09998d163ffcc16bfcbbf9f516467dd3311d
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418378"
---
# <a name="manage-runbooks-in-azure-automation"></a>管理 Azure 自動化中的 Runbook

您可以將 Runbook 新增至 Azure 自動化，方法是[建立新的](#creating-a-new-runbook)，或是從檔案或 [Runbook 資源庫](automation-runbook-gallery.md)匯入現有 Runbook。 本文提供從檔案建立和匯入 Runbook 的資訊。  您可以在 [Azure 自動化的 Runbook 和模組資源庫](automation-runbook-gallery.md)中取得有關存取社群 Runbook 和模組的所有詳細資料。

## <a name="create-a-runbook"></a>建立 Runbook

您可以使用其中一個 Azure 入口網站或 Windows PowerShell，在 Azure 自動化中建立新的 Runbook。 一旦建立 Runbook 之後，您就能使用[了解 PowerShell 工作流程](automation-powershell-workflow.md)和 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)中的資訊來編輯它。

### <a name="create-a-runbook-in-the-azure-portal"></a>在 Azure 入口網站中建立 Runbook

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 從 [中樞] 選取 [Runbook] 以開啟 Runbook 清單。
3. 按一下 [加入 Runbook] 按鈕，然後按一下 [建立新的 Runbook]。
4. 輸入 Runbook 的 [名稱]  ，然後選取其 [類型](automation-runbook-types.md)。 Runbook 名稱必須以字母開頭，可以具有字母、數字、底線和連字號。
5. 按一下 [建立]  來建立 Runbook 並開啟編輯器。

### <a name="create-a-runbook-with-powershell"></a>使用 PowerShell 建立 Runbook

您可以使用 [New-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/new-azurermautomationrunbook) Cmdlet，建立空的 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 使用 **Type** 參數指定四個 Runbook 類型的其中一個。

下列範例命令顯示如何建立新的空白 Runbook。

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>匯入 Runbook

您可以在 Azure 自動化中建立新的 Runbook，方法是匯入 PowerShell 指令碼或 PowerShell 工作流程 (.ps1 副檔名)、已匯出的圖形化 Runbook (.graphrunbook) 或 Python 2 指令碼 (.py 副檔名)。  您必須指定匯入期間建立的 [Runbook 類型](automation-runbook-types.md)，並考量下列事項。

* `.graphrunbook` 檔案只能匯入到新的[圖形化 Runbook](automation-runbook-types.md#graphical-runbooks)，而圖形化 Runbook 只能從 `.graphrunbook` 檔案建立。
* 包含 PowerShell 工作流程的 `.ps1` 檔案只能匯入至 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)。  如果檔案包含多個 PowerShell 工作流程，則匯入將會失敗。 您必須將每個工作流程儲存到它們各自的檔案，並且個別匯入。
* 未包含工作流程的 `.ps1` 檔案可以匯入至 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 或 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)。  如果是匯入 PowerShell 工作流程 Runbook 中，則會轉換為工作流程，而且 Runbook 會包含註解來指明所做的變更。

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>使用 Azure 入口網站從檔案匯入 Runbook

您可以使用下列程序，將指令碼檔案匯入到 Azure 自動化。  

> [!NOTE]
> 請注意，您只能使用入口網站，將 .ps1 檔案匯入 PowerShell 工作流程 Runbook。

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 從 [中樞] 選取 [Runbook] 以開啟 Runbook 清單。
3. 按一下 [加入 Runbook] 按鈕，然後按一下 [匯入]。
4. 按一下 [Runbook 檔案]  以選取要匯入的檔案
5. 如果 [名稱]  欄位已啟用，則您可以選擇變更它。  Runbook 名稱必須以字母開頭，可以具有字母、數字、底線和連字號。
6. [Runbook 類型](automation-runbook-types.md) 會自動選取，但在考量適用的限制之後，您可以變更類型。 
7. 新的 Runbook 會出現在自動化帳戶的 Runbook 清單中。
8. 您必須 [發佈 Runbook](#publishing-a-runbook) ，才能執行。

> [!NOTE]
> 當您匯入圖形化 Runbook 或圖形化 PowerShell 工作流程 Runbook 之後，您可以視需要選擇轉換成另一種類型。 無法轉換為文字 Runbook。

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>使用 Windows PowerShell 從指令碼檔案匯入 Runbook

您可以使用 [Import-AzureRMAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/import-azurermautomationrunbook) Cmdlet，來匯入為指令碼檔案以做為草稿 PowerShell 工作流程 Runbook。 如果 Runbook 已經存在，除非您使用 *-Force* 參數，否則匯入會失敗。

以下範例命令示範如何將指令碼檔案匯入 Runbook。

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>測試 Runbook

當您測試 Runbook 時， [草稿版本](#publishing-a-runbook) 會執行，而且它執行的任何動作都會完成。 不會建立任何工作歷程記錄，但 [測試輸出] 窗格中會顯示[輸出](automation-runbook-output-and-messages.md#output-stream)與[警告和錯誤](automation-runbook-output-and-messages.md#message-streams)串流。 只有將 [$VerbosePreference 變數](automation-runbook-output-and-messages.md#preference-variables)設為 Continue，傳送給[詳細資訊串流](automation-runbook-output-and-messages.md#message-streams)的訊息才會顯示在 [輸出] 窗格中。

即使執行的是草稿版本，Runbook 仍會正常執行，並對環境中的資源執行任何動作。 因此，您只應在非生產資源中測試 Runbook。

測試每個 [Runbook 類型](automation-runbook-types.md)的程序都相同，而且無論是在文字式編輯器或 Azure 入口網站的圖形化編輯器中進行測試，都沒有任何差別。  

1. 您可以在[文字編輯器](automation-edit-textual-runbook.md)或[圖形化編輯器](automation-graphical-authoring-intro.md)中開啟 Runbook 的草稿版本。
1. 按一下 [測試] 按鈕以開啟 [測試] 頁面。
1. 如果 Runbook 含有參數，這些參數會列在左窗格中，您可以在此提供值，以供測試使用。
1. 如果您想要在[混合式 Runbook 背景工作](automation-hybrid-runbook-worker.md)上執行測試，則需將 [執行設定] 變更為 [混合式背景工作]，然後選取目標群組的名稱。  否則，請保留預設值 **Azure**，以便在雲端中執行測試。
1. 按一下 [啟動]  按鈕以啟動測試。
1. 如果 Runbook 是 [PowerShell 工作流程](automation-runbook-types.md#powershell-workflow-runbooks)或[圖形化](automation-runbook-types.md#graphical-runbooks) Runbook，則在測試過程中，您可以使用 [輸出] 窗格下方的按鈕來停止或暫停 Runbook。 暫停 Runbook 時，它會先完成目前的活動後才暫停。 Runbook 一旦暫停，您可以選擇停止或重新啟動。
1. 您可以在 [輸出] 窗格中檢查 Runbook 的輸出。

## <a name="publish-a-runbook"></a>發行 Runbook

當您建立或匯入新的 Runbook 時，您必須發佈才能執行它。  自動化中的每個 Runbook 都有草稿和已發行的版本。 只可執行已發行版本，而且只可編輯草稿版本。 已發行版本不會受到草稿版本的任何變更影響。 草稿版本應該已可供使用時，您會將它發佈，以草稿版本覆寫已發佈版本。

### <a name="azure-portal"></a>Azure 入口網站

1. 在 Azure 入口網站中開啟 Runbook。
2. 按一下 [ **編輯** ] 按鈕。
3. 按一下 [發佈] 按鈕，然後對驗證訊息按一下 [是]。

### <a name="powershell"></a>PowerShell

您可以使用 [Publish-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/publish-azurermautomationrunbook) Cmdlet，利用 Windows PowerShell 發佈 Runbook。 下列範例命令顯示如何發佈範例 Runbook。

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>後續步驟

* 若要了解如何從 Runbook 和 PowerShell 模組資源庫中受益，請參閱 [Azure 自動化的 Runbook 和模組資源庫](automation-runbook-gallery.md)
* 若要深入了解使用文字編輯器編輯 PowerShell 和 PowerShell 工作流程 Runbook，請參閱 [在 Azure 自動化中編輯文字式 Runbook](automation-edit-textual-runbook.md)
* 若要深入了解如何編寫圖形化 Runbook，請參閱 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)
