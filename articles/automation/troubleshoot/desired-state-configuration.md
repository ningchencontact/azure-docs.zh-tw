---
title: 針對 Azure 自動化 Desired State Configuration (DSC) 問題進行疑難排解
description: 本文提供有關針對 Desired State Configuration (DSC) 問題進行疑難排解的資訊
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7cb0d77a266dbe8afd331782965e7e9a44663671
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514453"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>針對 Desired State Configuration (DSC) 問題進行疑難排解

本文提供有關針對 Desired State Configuration (DSC) 問題進行疑難排解的資訊。

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>使用 Desired State Configuration (DSC) 時的常見錯誤

### <a name="unsupported-characters"></a>案例：無法從入口網站中刪除具有特殊字元的組態

#### <a name="issue"></a>問題

嘗試將從入口網站中刪除 DSC 設定時，您會看到下列錯誤：

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>原因

此錯誤是打算解決的暫時性問題。

#### <a name="resolution"></a>解決方案

* 若要刪除的組態使用 Az Cmdlet 」 移除 AzAutomationDscConfiguration"。
* 此 cmdlet 的文件尚未更新。  在那之前，請參閱文件以取得 AzureRM 模組。
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>案例：無法註冊 Dsc 代理程式

#### <a name="issue"></a>問題

當您嘗試執行時`Set-DscLocalConfigurationManager`或另一個 DSC cmdlet，您會收到錯誤：

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

此錯誤通常被因為防火牆、 proxy 伺服器或其他網路錯誤後方的電腦。

#### <a name="resolution"></a>解決方案

確認您的電腦具有適當的端點的存取權的 Azure 自動化 DSC，並再試一次。 如需連接埠和位址所需的清單，請參閱[網路規劃](../automation-dsc-overview.md#network-planning)

### <a name="failed-not-found"></a>案例：節點處於失敗狀態，並發生「找不到」錯誤

#### <a name="issue"></a>問題

節點的報告具有「失敗」  狀態且包含錯誤：

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>原因

此錯誤通常發生在將節點指派給設定名稱 (例如 ABC)，而不是指派給節點設定名稱 (例如 ABC.WebServer) 的情況下。

#### <a name="resolution"></a>解決方案

* 請確定您要指派具有 「 節點組態名稱 」 而不是 「 組態名稱"的節點。
* 您可以使用 Azure 入口網站或使用 PowerShell Cmdlet，將節點組態指派至節點。

  * 若要將節點組態指派給節點，使用 Azure 入口網站中，開啟**DSC 節點**頁面上，然後選取節點，然後按一下**指派節點組態** 按鈕。  
  * 若要將節點組態指派至節點，使用 PowerShell cmdlet，請使用**Set-azurermautomationdscnode** cmdlet

### <a name="no-mof-files"></a>案例：編譯設定時，沒有產生任何節點設定 (MOF 檔案)

#### <a name="issue"></a>問題

您的 DSC 編譯工作因發生下列錯誤而暫停：

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>原因

當 DSC 設定中緊接在 **Node** 關鍵字後面的運算式評估為 `$null` 時，便不會產生任何節點設定。

#### <a name="resolution"></a>解決方案

下列任何一個解決方案都可以修正此問題：

* 請確定運算式旁的**節點**$null 不評估組態定義中的關鍵字。
* 如果您在編譯組態時傳遞 ConfigurationData，請確定您傳遞的是組態向 [ConfigurationData](../automation-dsc-compile.md#configurationdata)要求的預期值。

### <a name="dsc-in-progress"></a>案例：DSC 節點報告變成停留在「進行中」狀態

#### <a name="issue"></a>問題

DSC 代理程式輸出：

```error
No instance found with given property values
```

#### <a name="cause"></a>原因

您已將 WMF 版本升級，且有損毀的 WMI。

#### <a name="resolution"></a>解決方案

若要修正此問題，請依照中的指示[DSC 已知問題和限制](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc)文章。

### <a name="issue-using-credential"></a>案例：無法在 DSC 設定中使用認證

#### <a name="issue"></a>問題

您的 DSC 編譯工作因發生下列錯誤而暫停：

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>原因

您已在組態中使用的認證，但沒有提供適當**ConfigurationData**來設定**PSDscAllowPlainTextPassword**設為 true，每個節點組態。

#### <a name="resolution"></a>解決方案

* 請確認傳入適當**ConfigurationData**來設定**PSDscAllowPlainTextPassword**設為 true，每個節點組態設定中所述。 如需詳細資訊，請參閱 [Azure 自動化 DSC 中的資產](../automation-dsc-compile.md#assets)。

### <a name="failure-processing-extension"></a>案例：上架的 dsc 擴充功能，「 失敗的處理延伸模組 」 的錯誤

#### <a name="issue"></a>問題

使用 DSC 延伸模組，失敗的登入發生包含錯誤時：

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>原因

節點指派節點組態名稱不存在服務中時，通常就會發生此錯誤。

#### <a name="resolution"></a>解決方案

* 請確定您要指派具有完全符合的名稱，在服務中的節點組態名稱的節點。
* 您可以選擇不包含節點，但未指派節點設定，將會導致上架的節點組態名稱

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答
* 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫－專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
* 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]  。
