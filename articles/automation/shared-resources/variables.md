---
title: Azure 自動化中的變數資產
description: 變數資產是可用於 Azure 自動化中所有 Runbook 和 DSC 設定的值。  這篇文章說明變數的詳細資料，以及如何以文字式和圖形化編寫形式加以使用。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 39282e816be875e598d7e0599eeb358a79941be7
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478074"
---
# <a name="variable-assets-in-azure-automation"></a>Azure 自動化中的變數資產

變數資產是可用於自動化帳戶中所有 Runbook 和 DSC 設定的值。 他們可以管理在 Azure 入口網站、 PowerShell、 runbook 或 DSC 組態內。 自動化變數對下列案例很實用：

- 在多個 Runbook 或 DSC 設定之間共用值。

- 在相同 Runbook 或 DSC 設定的多個作業之間共用值。

- 從入口網站或 PowerShell 命令列，以供 runbook 或 DSC 組態，例如一組常見設定項目等特定清單中的 VM 名稱、 特定的資源群組、 AD 網域名稱等等，請管理值。  

自動化變數會保存，因為它們會出現即使 runbook 或 DSC 設定失敗。 此行為可讓一個 runbook，然後由另一個，或使用相同的 runbook 或 DSC 設定下一次執行時所要設定的值。

建立變數時，您可以指定將其加密儲存。 加密的變數會安全地儲存在 Azure 自動化中，且其值無法擷取從[Get-azurermautomationvariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)隨附於 Azure PowerShell 模組的 cmdlet。 可以擷取加密值的唯一方法是從 Runbook 或 DSC 設定中的 **Get-AutomationVariable** 活動。 如果您想要將加密的變數變更為 未加密，您可以必須刪除並重新建立未加密的變數。

>[!NOTE]
>Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產都會經過加密，並使用為每個自動化帳戶產生的唯一金鑰儲存在 Azure 自動化中。 此金鑰會儲存在系統管理的 Key Vault 中。 在儲存安全資產之前，系統會從 Key Vault 載入金鑰，然後用來加密資產。 此程序是由 Azure 自動化所管理。

## <a name="variable-types"></a>變數型別

使用 Azure 入口網站建立變數時，您必須從下拉式清單中指定資料類型，使得入口網站可以顯示適當的控制項來輸入變數值。 變數不限於這個資料型別。 您必須設定使用 Windows PowerShell，如果您想要指定不同的型別值的變數。 如果您指定**未定義**，則變數的值設 **$null**，而且您必須將值[Set-azurermautomationvariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) cmdlet 或**Set-automationvariable**活動。 您無法建立或變更在入口網站中，複雜變數類型的值，但您可以使用 Windows PowerShell 的任何類型的值。 複雜類型會傳回為 [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject)。

您可以藉由建立陣列或雜湊表，並將它儲存到變數中，來儲存多個值到單一變數。

以下是可在自動化中使用的變數類型清單：

* String
* Integer
* DateTime
* Boolean
* Null

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell Cmdlet

針對 AzureRM，下表中的 Cmdlet 可透過 Windows PowerShell 來建立和管理自動化認證資產。 它們附屬於[AzureRM.Automation 模組](/powershell/azure/overview)，這是可用於自動化 runbook 和 DSC 設定。

| Cmdlet | 描述 |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|擷取現有變數的值。|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|建立新的變數並設定其值。|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|移除現有的變數。|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|設定現有的變數的值。|

## <a name="activities"></a>activities

下表中的活動用來存取中 Runbook 和 DSC 設定的認證。

| activities | 描述 |
|:---|:---|
|Get-AutomationVariable|擷取現有變數的值。|
|Set-AutomationVariable|設定現有的變數的值。|

> [!NOTE]
> 您應該避免在 Runbook 或 DSC 設定中 **Get-AutomationVariable** 的 -Name 參數中使用變數，因為這可能會使在設計階段中探索 Runbook 或 DSC 設定與自動化變數之間的相依性變得複雜。

下表中的函式可用來存取和取出 Python2 Runbook 中的變數。

|Python2 函式|描述|
|:---|:---|
|automationassets.get_automation_variable|擷取現有變數的值。 |
|automationassets.set_automation_variable|設定現有的變數的值。 |

> [!NOTE]
> 您必須在 Python Runbook 的頂端匯入「automationassets」模組，才能存取資產函式。

## <a name="creating-a-new-automation-variable"></a>建立新自動化變數

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>使用 Azure 入口網站建立新的變數

1. 從您的自動化帳戶，按一下 [資產]  圖格，然後在 [資產]  刀鋒視窗上選取 [變數]  。
2. 在 [變數]  圖格上，選取 [新增變數]  。
3. 完成 [新增變數]  刀鋒視窗上的選項，然後按一下 [建立]  並儲存新變數。

### <a name="to-create-a-new-variable-with-windows-powershell"></a>使用 Windows PowerShell 建立新變數

[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) Cmdlet 會建立新變數，並設定其初始值。 您可以使用 [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) 來擷取值。 如果值為簡單型別，則會傳回該相同的型別。 如果它是複雜型別，則會傳回 **PSCustomObject** 。

下列範例命令顯示如何建立字串型別的變數，然後傳回它的值。

```powershell
New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

下列範例命令顯示如何建立具有複雜型別的變數，然後傳回其屬性。 在此情況下，會使用來自 **Get-AzureRmVm** 的虛擬機器物件。

```powershell
$vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>在 Runbook 或 DSC 設定中使用變數

使用 **Set-AutomationVariable** 活動來設定 PowerShell Runbook 或 DSC 設定中自動化變數的值，並使用 **Get-AutomationVariable** 來取出該值。 您不應該在 Runbook 或 DSC 設定中使用 **Set-AzureRMAutomationVariable** 或 **Get-AzureRMAutomationVariable** Cmdlet，因為它們都不如工作流程活動有效率。 您也無法使用 **Get-AzureRMAutomationVariable** 來擷取安全變數的值。 在 Runbook 或 DSC 設定中建立新變數的唯一方法是使用 [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) Cmdlet。

### <a name="textual-runbook-samples"></a>文字式 Runbook 範例

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>從變數設定和擷取簡單的值

下列範例命令顯示如何設定及擷取文字式 Runbook 中的變數。 在此範例中，它會假設整數類型的變數命名為*NumberOfIterations*並*NumberOfRunnings*名為字串類型變數，以及*SampleMessage*有已建立。

```powershell
$NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="setting-and-retrieving-a-variable-in-python2"></a>設定和取出 Python2 中的變數

以下範例程式碼示範如何在 Python2 Runbook 中使用變數、設定變數，以及處理不存在變數的例外狀況。

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("non-existing variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>圖形化 Runbook 範例

在圖形化 Runbook 中，您會加入 **Get-AutomationVariable** 或 **Set-AutomationVariable**，方法是在圖形化編輯器 [文件庫] 窗格中的變數上按一下滑鼠右鍵，然後選取您想要的活動。

![加入變數至畫布](../media/variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>變數中的設定值

下圖顯示的範例活動會在圖形化 Runbook 中使用簡單值更新變數。 在此範例中， **Get-azurermvm**擷取單一的 Azure 虛擬機器和電腦名稱儲存至現有的自動化變數類型為字串。 [連結是管線或順序](../automation-graphical-authoring-intro.md#links-and-workflow)都沒關係，因為您在輸出中只預期單一物件。

![設定簡單變數](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>後續步驟

- 若要深入了解如何在圖形化編寫中將活動連接在一起，請參閱 [圖形化編寫中的連結](../automation-graphical-authoring-intro.md#links-and-workflow)
- 若要開始使用圖形化 Runbook，請參閱 [我的第一個圖形化 Runbook](../automation-first-runbook-graphical.md)
