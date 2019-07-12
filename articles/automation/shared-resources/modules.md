---
title: 管理 Azure 自動化中的模組
description: 這篇文章說明如何管理 Azure 自動化中的模組
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 06/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd085164fc9804e0c1c822df1c72d3ef94093a07
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672793"
---
# <a name="manage-modules-in-azure-automation"></a>管理 Azure 自動化中的模組

Azure 自動化提供的 PowerShell 模組匯入至您的自動化帳戶，以供基礎的 PowerShell runbook 的能力。 您已建立，從 PowerShell 資源庫的自訂模組或 AzureRM 和 Az 模組適用於 Azure，可以使用這些模組。 當您建立自動化帳戶時一些模組預設會匯入的。

## <a name="import-modules"></a>匯入模組

有多種方式，您可以匯入模組到您的自動化帳戶。 下列各節說明不同的方式，匯入模組。

> [!NOTE]
> 使用 Azure 自動化中的模組中的最大路徑會是檔案的 140 個字元。 任何超過 140 個字元的路徑不能匯入的 PowerShell 工作階段`Import-Module`。

### <a name="powershell"></a>PowerShell

您可以使用[New-azurermautomationmodule](/powershell/module/azurerm.automation/new-azurermautomationmodule)將模組匯入您的自動化帳戶。 此 cmdlet 會加入模組 zip 封裝的 url。

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站中，瀏覽至您的自動化帳戶，然後選取**模組**下方**共用資源**。 按一下  **+ 加入模組**。 選取  **.zip**檔案，其中包含您的模組，然後按一下 **確定**開始匯入程序。

### <a name="powershell-gallery"></a>PowerShell 資源庫

從 PowerShell 資源庫的模組可以從匯入[PowerShell 資源庫](https://www.powershellgallery.com)直接或從您的自動化帳戶。

若要匯入模組，從 PowerShell 資源庫，移至 https://www.powershellgallery.com 並搜尋您想要匯入的模組。 按一下 **部署至 Azure 自動化**上**Azure 自動化**索引標籤下**安裝選項**。 這個動作會開啟 Azure 入口網站中。 在 **匯入**頁面上，選取您的自動化帳戶，然後按一下**確定**。

![PowerShell 資源庫匯入模組](../media/modules/powershell-gallery.png)

您也可以從 「 PowerShell 資源庫的模組匯入直接從您的自動化帳戶。 在您的自動化帳戶中選取**模組**下方**共用資源**。 在 模組 頁面中，按一下 **瀏覽資源庫**。 這會開啟**瀏覽資源庫**頁面。 您可以使用此頁面來搜尋模組 「 PowerShell 資源庫。 選取您想要匯入，並按一下的模組**匯入**。 在 **匯入**頁面上，按一下**確定**開始匯入程序。

![PowerShell 資源庫匯入從 Azure 入口網站](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>刪除模組

如果您有與模組的問題，或者您需要回復至舊版的模組，您可以從您的自動化帳戶中刪除它。 您不能刪除原始版本[預設模組](#default-modules)匯入，當您建立自動化帳戶。 如果您想要刪除此的模組是新版的其中一個[預設模組](#default-modules)安裝，它將會回復至您的自動化帳戶與已安裝的版本。 否則，將會移除您從您的自動化帳戶中刪除任何模組。

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站中，瀏覽至您的自動化帳戶，然後選取**模組**下方**共用資源**。 選取您想要移除的模組。 在 **模組**頁面上，clcick**刪除**。 如果此模組是其中一個[預設模組](#default-modules)它將會回復到之前建立的自動化帳戶時的目前的版本。

### <a name="powershell"></a>PowerShell

若要移除的模組，透過 PowerShell，執行下列命令：

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>內部的 cmdlet

下列是在內部的 cmdlet 清單`Orchestrator.AssetManagement.Cmdlets`模組匯入至每個自動化帳戶。 這些指令程式可在您的 runbook 和 DSC 設定中存取，並可讓您與您在您的自動化帳戶內的資產互動。 此外，內部的 cmdlet 可讓您擷取祕密從加密**可變**的值，**認證**，且加密**連接**欄位。 Azure PowerShell cmdlet 不能夠擷取這些密碼。 這些 cmdlet 不需要您使用它們時，會隱含地連線到 Azure。 這是幫助您有連線，例如執行身分帳戶，您要用來向 Azure 驗證的情況。

|名稱|描述|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>將連線類型新增至您的模組

您可以提供自訂[連線類型](../automation-connections.md)供您使用您的自動化帳戶中的選擇性檔案加入您的模組。 這個檔案是中繼資料檔案，並指定要在您的自動化帳戶中的模組的 cmdlet 搭配使用 Azure 自動化連線類型。 若要這麼做，您必須先知道如何撰寫 PowerShell 模組。 如需有關如何撰寫模組的詳細資訊，請參閱[如何撰寫 PowerShell 指令碼模組](/powershell/developer/module/how-to-write-a-powershell-script-module)。

![在 Azure 入口網站中使用自訂連線](../media/modules/connection-create-new.png)

若要新增的 Azure 自動化連線類型，您的模組必須包含名稱的檔案`<ModuleName>-Automation.json`，指定連線類型屬性。 這是 json 檔案，放置在壓縮 .zip 檔案的模組資料夾內。 此檔案包含的欄位，才能連線到系統或服務代表模組的連線。 這個組態最終會在 Azure 自動化中建立連線類型。 使用此檔案，您可以設定欄位名稱、 類型和欄位應該是加密或選用的連線類型的模組。 以下是範例中定義的使用者名稱和密碼屬性的 json 檔案格式的範本：

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="module-best-practices"></a>模組的最佳作法

PowerShell 模組都可以匯入到 Azure 自動化，以使其 Cmdlet 可供在 Runbook 內使用，以及使其 DSC 資源可供在 DSC 組態內使用。 Azure 自動化會在幕後儲存這些模組，並在執行 Runbook 作業和 DSC 編譯作業時將其載入 Azure 自動化沙箱，以在其中執行 Runbook 和編譯 DSC 組態。 模組中的任何 DSC 資源也會自動放置於 Automation DSC 提取伺服器。 它們可以在機器套用 DSC 組態時由機器提取。

我們建議您在撰寫 PowerShell 模組，可在 Azure 自動化中使用時，請考慮下列：

* 在模組中加入每個 Cmdlet 的概要、描述和說明 URI。 在 PowerShell 中，您可以為 Cmdlet 定義特定說明資訊，以讓使用者透過 **Get-Help** Cmdlet 獲得其使用說明。 下列範例示範如何定義概要和說明 URI 為.psm1 模組檔案中：

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  提供此資訊會顯示在 PowerShell 主控台使用 **Get-Help** Cmdlet 的說明。 在 Azure 入口網站中，也會顯示這個描述。

  ![整合模組說明](../media/modules/module-activity-description.png)

* 如果模組連接到外部服務，它應該包含[連線類型](#add-a-connection-type-to-your-module)。 模組中的每個 Cmdlet 應該要能夠採用連線物件 (該連線類型的執行個體) 來做為參數。 這樣可讓使用者在每次呼叫 Cmdlet 時，將連線資產的參數對應至 Cmdlet 的相對應參數。 根據上述的 runbook 範例，它會使用稱為 ContosoConnection 範例 Contoso 連線資產存取 Contoso 資源並且從外部服務傳回的資料。

  在下列範例中，欄位會對應至的使用者名稱和密碼屬性`PSCredential`物件，並接著傳遞至 cmdlet。

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  更容易且更好的處理這種行為的方法直接將連線物件傳遞給 cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  您可以透過讓 Cmdlet 接受直接將連線物件當做參數，而非只是參數的連線欄位，來讓 Cmdlet 具有像是上述範例的行為。 通常您會想讓每個 Cmdlet 都設定參數，讓未使用 Azure 自動化的使用者可以直接呼叫 Cmdlet，而不必建構雜湊表來作為連線物件。 參數集`UserAccount`，用來傳遞連線欄位屬性。 `ConnectionObject` 可讓您透過直接傳遞連線。

* 在模組中定義的所有 cmdlet 的輸出類型。 為 Cmdlet 定義輸出類型，可讓設計階段 IntelliSense 協助您判斷 Cmdlet 的輸出屬性，以供在撰寫期間使用。 在圖形化撰寫自動化 Runbook 期間，它會特別有幫助，因為設計階段的知識是讓模組的使用者獲得容易使用體驗的關鍵。

  這可藉由新增`[OutputType([<MyOutputType>])]`MyOutputType 其中是有效的型別。 若要深入了解 OutputType，請參閱[的相關函式 OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)。 下列程式碼是範例會新增`OutputType`cmdlet:

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![圖形化 Runbook 輸出類型](../media/modules/runbook-graphical-module-output-type.png)

  此行為類似於 Cmdlet 在 PowerShell ISE 中的輸出的「自動提示」功能，但不需要加以執行。

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

* 讓模組中的所有 Cmdlet 變成無狀態。 在相同的 AppDomain 的相同程序和沙箱中，可以同時執行多個 runbook 作業。 如果沒有任何這些層級上共用的狀態，作業可能會影響彼此。 此行為可能會斷斷續續出現且難以診斷的問題導致。  以下是不該做之事情的範例：

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

* 模組應該完全包含在 xcopy 的封裝中。 Azure 自動化模組會散發到自動化沙箱中，當 runbook 需要執行。 模組需要與它們執行所在的主機獨立運作。 您應該能夠進行壓縮並且移動模組套件，在將它匯入至另一部主機的 PowerShell 環境時讓它正常運作。 為了讓它運作，模組不應該相依於模組資料夾外部的任何檔案。 此資料夾是在模組匯入至 Azure 自動化時，進行壓縮的資料夾。 此模組也不應該相依於主機上的任何唯一登錄設定，例如安裝產品時所設定的這些設定。 在模組中的所有檔案應該都有超過 140 個字元的路徑。 任何超過 140 個字元的路徑會造成匯入您的 runbook 的問題。 若未遵循此最佳做法，模組在 Azure 自動化中將無法使用。  

* 如果在您的模組中參考 [Azure Powershell Az 模組](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)，請確定您未同時參考 `AzureRM`。 `Az` 模組無法與 `AzureRM` 模組搭配使用。 `Az` 在 Runbook 中受到支援，但是預設未匯入。 若要深入了解 `Az` 模組和要考量的事項，請參閱 [Azure 自動化中的 Az 模組支援](../az-modules.md)。

## <a name="default-modules"></a>預設模組

下表列出自動化帳戶建立時依預設匯入的模組。 下面所列的模組可以有較新版本的這些匯入，但是即使您刪除它們的較新版本，原始的版本可以移除從您的自動化帳戶。

|模組名稱|Version|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0.2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何建立 PowerShell 模組，請參閱 [撰寫 Windows PowerShell 模組](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)