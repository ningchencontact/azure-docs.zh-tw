---
title: Azure 自動化中的變數資產
description: 變數資產是可用於 Azure 自動化中所有 Runbook 和 DSC 設定的值。  這篇文章說明變數的詳細資料，以及如何以文字式和圖形化編寫形式加以使用。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0ac34f1d1e7fc2a967c7608f31f3b943f9380d01
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786197"
---
# <a name="variable-assets-in-azure-automation"></a>Azure 自動化中的變數資產

變數資產是可用於自動化帳戶中所有 Runbook 和 DSC 設定的值。 可以通过 Azure 门户、PowerShell、Runbook 或 DSC 配置管理这些资产。 自動化變數對下列案例很實用：

- 在多個 Runbook 或 DSC 設定之間共用值。

- 在相同 Runbook 或 DSC 設定的多個作業之間共用值。

- 从门户或 PowerShell 命令行管理由 Runbook 或 DSC 配置使用的值，例如一组常用配置项，如特定的 VM 名称列表、特定资源组、AD 域名，等等。  

由于自动化变量将会持久保存，即使 Runbook 或 DSC 配置失败，它们也仍然可用。 此行为允许一个 Runbook 设置的值随后由另一个 Runbook 使用，或由同一 Runbook 或 DSC 配置在下次运行时使用。

建立變數時，您可以指定將其加密儲存。 加密的变量安全地存储在 Azure 自动化中并且不能从 Azure PowerShell 模块随附的 [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) cmdlet 检索变量值。 可以擷取加密值的唯一方法是從 Runbook 或 DSC 設定中的 **Get-AutomationVariable** 活動。

>[!NOTE]
>Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產都會經過加密，並使用為每個自動化帳戶產生的唯一金鑰儲存在 Azure 自動化中。 此金鑰會儲存在系統管理的 Key Vault 中。 在儲存安全資產之前，系統會從 Key Vault 載入金鑰，然後用來加密資產。 此程序是由 Azure 自動化所管理。

## <a name="variable-types"></a>變數型別

使用 Azure 入口網站建立變數時，您必須從下拉式清單中指定資料類型，使得入口網站可以顯示適當的控制項來輸入變數值。 该变量并不局限于此数据类型。 如果想要指定不同类型的值，则必须使用 Windows PowerShell 设置该变量。 如果指定为“未定义”，则该变量的值将设置为 **$null**，并且必须使用 [Set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) cmdlet 或 **Set-AutomationVariable** 活动来设置该值。 无法在该门户中创建或更改复杂变量类型的值，但可以使用 Windows PowerShell 提供任何类型的值。 複雜類型會傳回為 [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject)。

您可以藉由建立陣列或雜湊表，並將它儲存到變數中，來儲存多個值到單一變數。

以下是可在自動化中使用的變數類型清單：

* 字串
* Integer
* DateTime
* Boolean
* Null

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell Cmdlet

針對 AzureRM，下表中的 Cmdlet 可透過 Windows PowerShell 來建立和管理自動化認證資產。 可在自动化 Runbook 和 DSC 配置中使用的 [AzureRM.Automation 模块](/powershell/azure/overview)已随附了这些 cmdlet。

| Cmdlet | 說明 |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|擷取現有變數的值。|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|建立新的變數並設定其值。|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|移除現有的變數。|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|設定現有的變數的值。|

## <a name="activities"></a>活動

下表中的活動用來存取中 Runbook 和 DSC 設定的認證。

| 活動 | 說明 |
|:---|:---|
|Get-AutomationVariable|擷取現有變數的值。|
|Set-AutomationVariable|設定現有的變數的值。|

> [!NOTE]
> 您應該避免在 Runbook 或 DSC 設定中 **Get-AutomationVariable** 的 -Name 參數中使用變數，因為這可能會使在設計階段中探索 Runbook 或 DSC 設定與自動化變數之間的相依性變得複雜。

下表中的函式可用來存取和取出 Python2 Runbook 中的變數。

|Python2 函式|說明|
|:---|:---|
|automationassets.get_automation_variable|擷取現有變數的值。 |
|automationassets.set_automation_variable|設定現有的變數的值。 |

> [!NOTE]
> 您必須在 Python Runbook 的頂端匯入「automationassets」模組，才能存取資產函式。

## <a name="creating-a-new-automation-variable"></a>建立新自動化變數

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>使用 Azure 入口網站建立新的變數

1. 從您的自動化帳戶，按一下 [資產] 圖格，然後在 [資產] 刀鋒視窗上選取 [變數]。
2. 在 [變數] 圖格上，選取 [新增變數]。
3. 完成 [新增變數] 刀鋒視窗上的選項，然後按一下 [建立] 並儲存新變數。

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

下列範例命令顯示如何設定及擷取文字式 Runbook 中的變數。 在此示例中，假定已创建名为 *NumberOfIterations* 和 *NumberOfRunnings* 的整数类型变量以及名为 *SampleMessage* 的字符串类型变量。

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

下圖顯示的範例活動會在圖形化 Runbook 中使用簡單值更新變數。 在此示例中，**Get-AzureRmVM** 检索一个 Azure 虚拟机并将计算机名称保存到一个字符串类型的现有自动化变量。 [連結是管線或順序](../automation-graphical-authoring-intro.md#links-and-workflow)都沒關係，因為您在輸出中只預期單一物件。

![設定簡單變數](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>後續步驟

- 若要深入了解如何在圖形化編寫中將活動連接在一起，請參閱 [圖形化編寫中的連結](../automation-graphical-authoring-intro.md#links-and-workflow)
- 若要開始使用圖形化 Runbook，請參閱 [我的第一個圖形化 Runbook](../automation-first-runbook-graphical.md)
