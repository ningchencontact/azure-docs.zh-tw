---
title: 針對 Azure 自動化 Desired State Configuration (DSC) 問題進行疑難排解
description: 本文提供有關針對 Desired State Configuration (DSC) 問題進行疑難排解的資訊
services: automation
ms.service: automation
ms.subservice: ''
author: bobbytreed
ms.author: robreed
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 67e5364996be2945d67aa1a95cbc3ab8137e077e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850263"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>針對 Desired State Configuration (DSC) 問題進行疑難排解

本文提供有關針對 Desired State Configuration (DSC) 問題進行疑難排解的資訊。

## <a name="steps-to-troubleshoot-desired-state-configuration-dsc"></a>疑難排解 Desired State Configuration 的步驟 (DSC)

當您在 Azure 狀態設定中編譯或部署設定發生錯誤時, 以下是可協助您診斷問題的幾個步驟。

1. **請確定您的設定在本機電腦上成功編譯:** Azure 狀態設定建置於 PowerShell DSC 上。 您可以在[POWERSHELL dsc](https://docs.microsoft.com/en-us/powershell/scripting/overview)檔中找到 DSC 語言和語法的檔。

   藉由在本機電腦上編譯 DSC 設定, 您可以探索並解決常見錯誤, 例如:

   - **遺失的模組**
   - **語法錯誤**
   - **邏輯錯誤**

2. **在您的節點上查看 DSC 記錄:** 如果您的設定成功編譯, 但套用至節點時失敗, 您可以在記錄檔中找到詳細資訊。 如需有關哪裡可以找到 DSC 記錄的資訊, 請參閱[Dsc 事件記錄檔的位置](/powershell/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)。

   此外, [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics)可協助您剖析來自 DSC 記錄檔的詳細資訊。 如果您聯繫支援人員, 他們將需要這些記錄來診斷您的問題。

   您可以使用[安裝穩定版本模組](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)底下找到的指示, 在本機電腦上安裝**xDscDiagnostics** 。

   若要在您的 Azure 電腦上安裝**xDscDiagnostics** , 您可以使用[az vm run-command](/cli/azure/vm/run-command)或[Invoke-AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand)。 您也可以[依照在具有執行命令的 WINDOWS VM 中執行 PowerShell 腳本](../../virtual-machines/windows/run-command.md)中的步驟, 使用入口網站中的 [**執行命令**] 選項。

   如需使用**xDscDiagnostics**的詳細資訊, 請參閱[使用 XDSCDIAGNOSTICS 來分析 DSC 記錄](/powershell/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs)檔, 以及[xDscDiagnostics Cmdlet](https://github.com/PowerShell/xDscDiagnostics#cmdlets)。
3. **請確定您的節點和自動化工作區具有必要的模組:** Desired State Configuration 取決於節點上安裝的模組。  使用 Azure 自動化狀態設定時, 請使用匯[入模組](../shared-resources/modules.md#import-modules)中列出的步驟, 將任何必要的模組匯入到您的自動化帳戶。 設定也可能相依于特定版本的模組。  如需詳細資訊, 請參閱[疑難排解模組](shared-resources.md#modules)。

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>使用 Desired State Configuration (DSC) 時的常見錯誤

### <a name="unsupported-characters"></a>案例：無法從入口網站刪除具有特殊字元的設定

#### <a name="issue"></a>問題

嘗試從入口網站刪除 DSC 設定時, 您會看到下列錯誤:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>原因

此錯誤是計畫要解決的暫時性問題。

#### <a name="resolution"></a>解決方法

* 使用 Az Cmdlet "Remove-AzAutomationDscConfiguration" 來刪除設定。
* 此 Cmdlet 的檔尚未更新。  在那之前, 請參閱 AzureRM 模組的檔。
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>案例：無法註冊 Dsc 代理程式

#### <a name="issue"></a>問題

當嘗試執行`Set-DscLocalConfigurationManager`或另一個 DSC Cmdlet 時, 您會收到錯誤:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

#### <a name="cause"></a>原因

此錯誤通常是由防火牆、位於 proxy 伺服器後方的電腦, 或其他網路錯誤所造成。

#### <a name="resolution"></a>解決方法

請確認您的電腦可存取適當的 Azure 自動化 DSC 端點, 然後再試一次。 如需所需的埠和地址清單, 請參閱[網路規劃](../automation-dsc-overview.md#network-planning)

### <a name="failed-not-found"></a>案例：節點處於失敗狀態，並發生「找不到」錯誤

#### <a name="issue"></a>問題

節點的報告具有「失敗」狀態且包含錯誤：

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>原因

此錯誤通常發生在將節點指派給設定名稱 (例如 ABC)，而不是指派給節點設定名稱 (例如 ABC.WebServer) 的情況下。

#### <a name="resolution"></a>解決方法

* 請確定您指派的節點具有「節點設定名稱」, 而不是「設定名稱」。
* 您可以使用 Azure 入口網站或使用 PowerShell Cmdlet，將節點組態指派至節點。

  * 若要使用 Azure 入口網站將節點設定指派給節點, 請開啟 [ **DSC 節點**] 頁面, 然後選取一個節點, 再按一下 [**指派節點**設定] 按鈕。
  * 若要使用 PowerShell Cmdlet 將節點設定指派給節點, 請使用**unregister-azurermautomationdscnode** Cmdlet

### <a name="no-mof-files"></a>案例：編譯設定時，沒有產生任何節點設定 (MOF 檔案)

#### <a name="issue"></a>問題

您的 DSC 編譯工作因發生下列錯誤而暫停：

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>原因

當 DSC 設定中緊接在 **Node** 關鍵字後面的運算式評估為 `$null` 時，便不會產生任何節點設定。

#### <a name="resolution"></a>解決方法

下列任何一個解決方案都可以修正此問題：

* 請確定設定定義中**Node**關鍵字旁的運算式未評估為 $null。
* 如果您在編譯組態時傳遞 ConfigurationData，請確定您傳遞的是組態向 [ConfigurationData](../automation-dsc-compile.md)要求的預期值。

### <a name="dsc-in-progress"></a>案例：DSC 節點報告變成停留在「進行中」狀態

#### <a name="issue"></a>問題

DSC 代理程式輸出：

```error
No instance found with given property values
```

#### <a name="cause"></a>原因

您已將 WMF 版本升級，且有損毀的 WMI。

#### <a name="resolution"></a>解決方法

若要修正此問題, 請依照[DSC 已知問題和限制](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc)一文中的指示進行。

### <a name="issue-using-credential"></a>案例：無法在 DSC 設定中使用認證

#### <a name="issue"></a>問題

您的 DSC 編譯工作因發生下列錯誤而暫停：

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>原因

您已在設定中使用認證, 但未提供適當的**ConfigurationData** , 將每個節點設定的**PSDscAllowPlainTextPassword**設為 true。

#### <a name="resolution"></a>解決方法

* 請務必傳入適當的**ConfigurationData** , 以針對設定中所述的每個節點設定, 將**PSDscAllowPlainTextPassword**設為 true。 如需詳細資訊，請參閱 [Azure 自動化 DSC 中的資產](../automation-dsc-compile.md#working-with-assets-in-azure-automation-during-compilation)。

### <a name="failure-processing-extension"></a>案例：從 dsc 延伸模組上線, 「失敗處理延伸模組」錯誤

#### <a name="issue"></a>問題

當使用 DSC 延伸模組上線時, 會發生失敗, 其中包含錯誤:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>原因

當節點指派的節點設定名稱不存在於服務中時, 通常會發生此錯誤。

#### <a name="resolution"></a>解決方法

* 請確定您所指派的節點的節點設定名稱與服務中的名稱完全相符。
* 您可以選擇不包含節點設定名稱, 這會導致節點上架, 但不會指派節點設定

### <a name="failure-linux-temp-noexec"></a>案例：在 Linux 中套用設定, 發生一般錯誤時失敗

#### <a name="issue"></a>問題

在 Linux 中套用設定時, 會發生失敗, 其中包含錯誤:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>原因

客戶已識別出如果`/tmp`位置設定為`noexec`, 則目前的 DSC 版本將無法套用設定。

#### <a name="resolution"></a>解決方法

* 從位置移除`noexec`選項 `/tmp` 。

### <a name="compilation-node-name-overlap"></a>案例：重迭的節點設定名稱可能會導致版本錯誤

#### <a name="issue"></a>問題

如果使用單一設定腳本來產生多個節點設定, 而某些節點設定的名稱是其他專案的子集, 則編譯服務中的問題可能會導致指派錯誤的設定。  只有在使用單一腳本來產生具有每個節點之設定資料的設定, 以及在字串開頭髮生名稱重迭時, 才會發生這種情況。

例如, 如果使用單一設定腳本, 根據使用 Cmdlet 當做雜湊表傳遞的節點資料來產生設定, 則節點資料會包含名為 "server" 和 "1server" 的伺服器。

#### <a name="cause"></a>原因

編譯服務的已知問題。

#### <a name="resolution"></a>解決方法

最佳的解決方法是在本機或在 CI/CD 管線中進行編譯, 並將 MOF 檔案直接上傳至服務。  如果服務中的編譯是需求, 則下一個最佳的解決方法是分割編譯工作, 讓名稱中沒有任何重迭。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答
* 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫－專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
* 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
