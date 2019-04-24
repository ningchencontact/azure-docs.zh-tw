---
title: 在 Azure 自动化中管理模块
description: 本文介绍如何在 Azure 自动化中管理模块。
services: automation
ms.service: automation
ms.subservice: shared-resources
author: georgewallace
ms.author: gwallace
ms.date: 03/13/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fa7f5d3fb38eb1dbca51dec9b73dca3c998436aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60500306"
---
# <a name="manage-modules-in-azure-automation"></a>在 Azure 自动化中管理模块

Azure 自动化提供相应的功能用于将 PowerShell 模块导入到基于 PowerShell 的 Runbook 所用的自动化帐户。 您已建立，從 PowerShell 資源庫的自訂模組或 AzureRM 和 Az 模組適用於 Azure，可以使用這些模組。

## <a name="import-modules"></a>匯入模組

可通过多种方法将模块导入到自动化帐户。 以下部分介绍了不同的模块导入方法。

> [!NOTE]
> 在 Azure 自动化中使用的模块中文件的最大路径长度为 140 个字符。 如果任何路径的长度超过 140 个字符，则无法使用 `Import-Module` 将其导入到 PowerShell 会话。

### <a name="powershell"></a>PowerShell

可以使用 [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) 将模块导入到自动化帐户。 该 cmdlet 采用某个模块 zip 包的 URL。

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>Azure 入口網站

在 Azure 门户中导航到你的自动化帐户，然后选择“共享资源”下的“模块”。 单击“+ 添加模块”。 选择包含你的模块的 **.zip** 文件，然后单击“确定”开始执行导入过程。

### <a name="powershell-gallery"></a>PowerShell 資源庫

從 PowerShell 資源庫的模組可以從匯入[PowerShell 資源庫](https://www.powershellgallery.com)直接或從您的自動化帳戶。

若要匯入模組，從 PowerShell 資源庫，移至 https://www.powershellgallery.com並搜尋您想要匯入的模組。 按一下 **部署至 Azure 自動化**上**Azure 自動化**索引標籤下**安裝選項**。 這個動作會開啟 Azure 入口網站中。 在 **匯入**頁面上，選取您的自動化帳戶，然後按一下**確定**。

![PowerShell 資源庫匯入模組](../media/modules/powershell-gallery.png)

您也可以從 「 PowerShell 資源庫的模組匯入直接從您的自動化帳戶。 在您的自動化帳戶中選取**模組**下方**共用資源**。 在 模組 頁面中，按一下 **瀏覽資源庫**。 這會開啟**瀏覽資源庫**頁面。 您可以使用此頁面來搜尋模組 「 PowerShell 資源庫。 選取您想要匯入，並按一下的模組**匯入**。 在 **匯入**頁面上，按一下**確定**開始匯入程序。

![PowerShell 資源庫匯入從 Azure 入口網站](../media/modules/gallery-azure-portal.png)

## <a name="internal-cmdlets"></a>内部 cmdlet

下面是导入到每个自动化帐户的内部 `Orchestrator.AssetManagement.Cmdlets` 模块中的 cmdlet 列表。 可在 Runbook 和 DSC 配置中访问这些 cmdlet，使用它们可与自动化帐户中的资产进行交互。 此外，使用内部 cmdlet 还可以从加密的“变量”值、“凭据”和加密的“连接”字段中检索机密。 Azure PowerShell cmdlet 无法检索这些机密。 无需隐式连接到 Azure 即可使用这些 cmdlet。 如果需要使用某个连接（例如运行方式帐户）在 Azure 中进行身份验证，则这些内部 cmdlet 就非常有利。

|Name|描述|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>将连接类型添加到模块

可以通过将一个可选文件添加到模块来提供自定义的[连接类型](../automation-connections.md)，以便在自动化帐户中使用。 此文件是一个元数据文件，用于指定要在自动化帐户中配合模块 cmdlet 使用的 Azure 自动化连接类型。 若要实现此目的，首先必须知道如何创作 PowerShell 模块。 有关模块创作的详细信息，请参阅[如何编写 PowerShell 脚本模块](/powershell/developer/module/how-to-write-a-powershell-script-module)。

![在 Azure 门户中使用自定义连接](../media/modules/connection-create-new.png)

若要添加 Azure 自动化连接类型，模块必须包含用于指定连接类型属性的名为 `<ModuleName>-Automation.json` 的文件。 這是 json 檔案，放置在壓縮 .zip 檔案的模組資料夾內。 此文件包含连接到模块所代表的系统或服务所需的连接的字段。 這個組態最終會在 Azure 自動化中建立連線類型。 可以使用此文件为模块的连接类型设置字段名称、类型，以及这些字段是否应加密或可选。 以下示例是一个采用 JSON 文件格式的模板，用于定义用户名和密码属性：

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

## <a name="module-best-practices"></a>模块最佳做法

PowerShell 模組都可以匯入到 Azure 自動化，以使其 Cmdlet 可供在 Runbook 內使用，以及使其 DSC 資源可供在 DSC 組態內使用。 Azure 自動化會在幕後儲存這些模組，並在執行 Runbook 作業和 DSC 編譯作業時將其載入 Azure 自動化沙箱，以在其中執行 Runbook 和編譯 DSC 組態。 模組中的任何 DSC 資源也會自動放置於 Automation DSC 提取伺服器。 它們可以在機器套用 DSC 組態時由機器提取。

创作一个要在 Azure 自动化中使用的 PowerShell 模块时，我们建议考虑以下事项：

* 在模組中加入每個 Cmdlet 的概要、描述和說明 URI。 在 PowerShell 中，您可以為 Cmdlet 定義特定說明資訊，以讓使用者透過 **Get-Help** Cmdlet 獲得其使用說明。 以下示例演示如何定义要在 .psm1 模块文件中使用的摘要和帮助 URI：

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

  提供此資訊會顯示在 PowerShell 主控台使用 **Get-Help** Cmdlet 的說明。 Azure 门户中也会显示此说明。

  ![整合模組說明](../media/modules/module-activity-description.png)

* 如果模块连接到外部服务，则它应该包含一个[连接类型](#add-a-connection-type-to-your-module)。 模組中的每個 Cmdlet 應該要能夠採用連線物件 (該連線類型的執行個體) 來做為參數。 這樣可讓使用者在每次呼叫 Cmdlet 時，將連線資產的參數對應至 Cmdlet 的相對應參數。 根据上面的 Runbook 示例，该模块使用名为 ContosoConnection 的示例 Contoso 连接资产来访问 Contoso 资源，并从外部服务返回数据。

  在以下示例中，字段将映射到 `PSCredential` 对象的 UserName 和 Password 属性，然后传递给 cmdlet。

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  实现此行为的更轻松且更好的方法是直接将连接对象传递给 cmdlet：

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  您可以透過讓 Cmdlet 接受直接將連線物件當做參數，而非只是參數的連線欄位，來讓 Cmdlet 具有像是上述範例的行為。 通常您會想讓每個 Cmdlet 都設定參數，讓未使用 Azure 自動化的使用者可以直接呼叫 Cmdlet，而不必建構雜湊表來作為連線物件。 参数集 `UserAccount` 用于传递连接字段属性。 `ConnectionObject` 用于一直传递连接。

* 在模块中定义所有 cmdlet 的输出类型。 為 Cmdlet 定義輸出類型，可讓設計階段 IntelliSense 協助您判斷 Cmdlet 的輸出屬性，以供在撰寫期間使用。 在圖形化撰寫自動化 Runbook 期間，它會特別有幫助，因為設計階段的知識是讓模組的使用者獲得容易使用體驗的關鍵。

  可以通过添加 `[OutputType([<MyOutputType>])]`（其中，MyOutputType 是有效类型）来实现此目的。 若要详细了解 OutputType，请参阅[关于函数 OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)。 以下代码示例演示如何将 `OutputType` 添加到 cmdlet：

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

* 讓模組中的所有 Cmdlet 變成無狀態。 多个 Runbook 作业可在同一个 AppDomain 和同一个进程与沙盒中同时运行。 如果在这两些级别共享了任何状态，则作业可能会相互影响。 此行为可能导致间歇性的且难以诊断的问题。  以下是不該做之事情的範例：

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

* 该模块应完全包含在能够进行 xcopy 操作的包中。 需要执行 Runbook 时，Azure 自动化模块将分发到自动化沙盒中。 模組需要與它們執行所在的主機獨立運作。 您應該能夠進行壓縮並且移動模組套件，在將它匯入至另一部主機的 PowerShell 環境時讓它正常運作。 為了讓它運作，模組不應該相依於模組資料夾外部的任何檔案。 此資料夾是在模組匯入至 Azure 自動化時，進行壓縮的資料夾。 此模組也不應該相依於主機上的任何唯一登錄設定，例如安裝產品時所設定的這些設定。 该模块中所有文件的路径长度应小于 140 个字符。 长度超过 140 个字符的任何路径将导致导入 Runbook 时出现问题。 若未遵循此最佳做法，模組在 Azure 自動化中將無法使用。  

* 如果在您的模組中參考 [Azure Powershell Az 模組](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)，請確定您未同時參考 `AzureRM`。 `Az` 模組無法與 `AzureRM` 模組搭配使用。 `Az` 在 Runbook 中受到支援，但是預設未匯入。 若要深入了解 `Az` 模組和要考量的事項，請參閱 [Azure 自動化中的 Az 模組支援](../az-modules.md)。

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何建立 PowerShell 模組，請參閱 [撰寫 Windows PowerShell 模組](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)