---
title: 針對 Azure 自動化 Runbook 的錯誤進行疑難排解
description: 了解如何針對 Azure 自動化 Runbook 的錯誤進行疑難排解
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 65de80004dd05e3eb29f3313bc17405c40450d7a
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66397133"
---
# <a name="troubleshoot-errors-with-runbooks"></a>針對 Runbook 的錯誤進行疑難排解

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>使用 Azure 自動化 Runbook 時的驗證錯誤

### <a name="sign-in-failed"></a>案例：登入 Azure 帳戶失敗

#### <a name="issue"></a>問題

使用 `Add-AzureAccount` 或 `Connect-AzureRmAccount` Cmdlet 時，收到下列錯誤
：

```error
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>原因

如果認證資產名稱無效，就會發生此錯誤。 如果您用來設定自動化認證資產的使用者名稱和密碼無效，可能也會發生此錯誤。

#### <a name="resolution"></a>解決方案

為了判斷錯誤原因，請執行下列步驟：  

1. 確定您不具任何特殊字元。 這些字元包括用來連線到 Azure 之自動化認證資產名稱中的 **\@** 字元。  
2. 請確認您可以在本機 PowerShell ISE 編輯器中使用儲存在 Azure 自動化認證的使用者名稱和密碼。 您可以在 PowerShell ISE 中執行下列 Cmdlet，以檢查使用者名稱和密碼是否正確：  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. 如果您的驗證在本機失敗，即表示您尚未正確設定 Azure Active Directory 認證。 請參閱 [Authenticating to Azure using Azure Active Directory (使用 Azure Active Directory 對 Azure 進行驗證)](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) 部落格文章，以正確設定 Azure Active Directory 帳戶。  

4. 如果看起來像是暫時性錯誤，請嘗試在您的驗證常式中新增重試邏輯，讓驗證變得更健全。

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
   $LogonAttempt++
   # Logging in to Azure...
   $connectionResult = Connect-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $servicePrincipalConnection.TenantId `
      -ApplicationId $servicePrincipalConnection.ApplicationId `
      -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

   Start-Sleep -Seconds 30
   }
   ```

### <a name="unable-to-find-subscription"></a>案例：找不到 Azure 訂用帳戶

#### <a name="issue"></a>問題

您在使用 `Select-AzureSubscription` 或 `Select-AzureRmSubscription` Cmdlet 時，收到下列錯誤：

```error
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Error

如果發生下列情況，可能就會發生此錯誤：

* 訂用帳戶名稱無效

* 未將嘗試取得訂用帳戶詳細資料的 Azure Active Directory 使用者設定為訂用帳戶的管理員。

#### <a name="resolution"></a>解決方案

採取下列步驟，以判斷您是否已向 Azure 進行驗證並取得您嘗試選取之訂用帳戶的存取權：  

1. 為了確定它能獨立運作，請在 Azure 自動化外部測試您的指令碼。
2. 請務必先執行 `Add-AzureAccount` Cmdlet，再執行 `Select-AzureSubscription` Cmdlet。 
3. 在 Runbook 的開頭加上 `Disable-AzureRmContextAutosave –Scope Process`。 這個 Cmdlet 確保所有認證只會套用到目前 Runbook 的執行。
4. 如果您仍然看到此錯誤訊息，請在 `Add-AzureAccount` Cmdlet 之後新增 **AzureRmContext** 參數，以修改您的程式碼，然後執行此程式碼。

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

### <a name="auth-failed-mfa"></a>案例：對 Azure 的驗證失敗，因為已啟用多重要素驗證

#### <a name="issue"></a>問題

使用 Azure 使用者名稱與密碼向 Azure 進行驗證時，收到下列錯誤：

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>原因

如果您的 Azure 帳戶使用多重要素驗證，您就無法使用 Azure Active Directory 使用者來向 Azure 進行驗證。 相反地，您必須使用憑證或服務主體來向 Azure 驗證。

#### <a name="resolution"></a>解決方案

若要搭配 Azure 傳統部署模型 Cmdlet 使用憑證，請參閱 [creating and adding a certificate to manage Azure services](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) (建立及新增憑證來管理 Azure 服務)。 若要搭配 Azure Resource Manager Cmdlet 來使用服務主體，請參閱[使用 Azure 入口網站來建立服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)和[使用 Azure Resource Manager 驗證服務主體](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="common-errors-when-working-with-runbooks"></a>使用 Runbook 時的常見錯誤

### <a name="child-runbook-object"></a>子 Runbook 會在輸出資料流包含物件而非簡單資料類型時傳回錯誤

#### <a name="issue"></a>問題

叫用子 runbook 時，收到下列錯誤`-Wait`參數和輸出資料流包含與物件：

```error
Object reference not set to an instance of an object
```

#### <a name="cause"></a>原因

有個已知問題：如果輸出資料流包含物件，[Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) 就不會正確地處理它。

#### <a name="resolution"></a>解決方案

若要解決此問題，建議您改為實作輪詢邏輯，並使用 [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) Cmdlet 來擷取輸出。 下列範例中定義了此邏輯的範例。

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

### <a name="get-serializationsettings"></a>案例：您在 get_SerializationSettings 方法的相關作業串流中發現錯誤

#### <a name="issue"></a>問題

您在 Runbook 的作業串流中發現錯誤，出現的訊息如下：

```error
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type 
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly 
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' 
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>原因

此錯誤是由於在 Runbook 中使用 AzureRM 和 Az Cmdlet 所致。 您先匯入 `Az` 再匯入 `AzureRM` 時發生了此錯誤。

#### <a name="resolution"></a>解決方案

無法在同一個 Runbook 中匯入並使用 Az 和 AzureRM Cmdlet，若要深入了解 Azure 自動化中的 Az 支援，請參閱[Azure 自動化中的 Az 模組支援](../az-modules.md)。

### <a name="task-was-cancelled"></a>案例：Runbook 失敗，錯誤為：已取消工作

#### <a name="issue"></a>問題

您的 Runbook 失敗，並具有類似下列範例的錯誤：

```error
Exception: A task was canceled.
```

#### <a name="cause"></a>原因

此錯誤可能是因為使用過期的 Azure 模組所造成。

#### <a name="resolution"></a>解決方案

此錯誤可以透過將 Azure 模組更新至最新版本來解決。

在您的自動化帳戶中，按一下 [模組]  ，然後按一下 [更新 Azure 模組]  。 更新大約需要 15 分鐘，完成之後，請重新執行先前失敗的 Runbook。 若要深入了解如何更新模組，請參閱[更新 Azure 自動化中的 Azure 模組](../automation-update-azure-modules.md)。

### <a name="runbook-auth-failure"></a>案例：Runbook 在處理多個子訂用帳戶時發生失敗

#### <a name="issue"></a>問題

使用 `Start-AzureRmAutomationRunbook` 執行 Runbook 時，Runbook 無法管理 Azure 資源。

#### <a name="cause"></a>原因

Runbook 在執行時未使用正確的內容。

#### <a name="resolution"></a>解決方案

在使用多個訂用帳戶時，若叫用 Runbook，可能會遺失訂用帳戶內容。 若要確保訂用帳戶的內容會傳遞至 Runbook，請將 `AzureRmContext` 參數新增至 Cmdlet，並將內容傳遞給它。 也建議您使用 `Disable-AzureRmContextAutosave` Cmdlet 搭配**程序**範圍，以確保您使用的認證只會用於目前的 Runbook。

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

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
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

### <a name="not-recognized-as-cmdlet"></a>案例：因為遺失 Cmdlet，所以 Runbook 失敗

#### <a name="issue"></a>問題

您的 Runbook 失敗，並具有類似下列範例的錯誤：

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>原因

可能因為下列其中一個原因而發生此錯誤：

1. 未將包含 Cmdlet 的模組匯入到自動化帳戶
2. 已將包含 Cmdlet 的模組匯入，但該模組已過期

#### <a name="resolution"></a>解決方案

藉由完成下列其中一項工作，就可以解決此錯誤：

如果模組是 Azure 模組，請參閱[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)以了解如何在您的自動化帳戶中更新模組。

如果是個別模組，則請確定已將該模組匯入到您的「自動化帳戶」中。

### <a name="job-attempted-3-times"></a>案例：已嘗試啟動 Runbook 作業三次，但無法每次都順利啟動

#### <a name="issue"></a>問題

您的 Runbook 失敗，錯誤為：

```error
The job was tried three times but it failed
```

#### <a name="cause"></a>原因

可能因為下列其中一個問題而發生此錯誤：

1. 記憶體限制。 配置給沙箱的記憶體數量限制記載於[自動化服務限制](../../azure-subscription-service-limits.md#automation-limits)。 如果作業使用超過 400 MB 的記憶體，此作業可能失敗。

2. 網路通訊端。 Azure 沙箱受限於如[自動化服務限制](../../azure-subscription-service-limits.md#automation-limits)所述的 1000 個並存網路通訊端。

3. 模組不相容。 如果模組相依性不正確，就可能發生此錯誤，且當相依性不正確時，您的 Runbook 通常會傳回「找不到命令」或「無法繫結參數」訊息。

4. 您的 runbook 會嘗試呼叫可執行檔或 subprocess 會在 Azure 的沙箱中執行的 runbook。 Azure 沙箱不支援此案例。

5. 您的 runbook 嘗試寫入過多的例外狀況資料至輸出資料流。

#### <a name="resolution"></a>解決方案

下列任何一個解決方案都可以修正此問題：

* 在記憶體限制內運作的建議方法是分割多個 Runbook 之間的工作負載、不要處理記憶體中的太多資料、不要寫入來自 Runbook 的不必要輸出，或考慮您寫入 PowerShell 工作流程 Runbook 的檢查點數目。 您可以使用 clear 方法 (例如 `$myVar.clear()`) 來清除變數，並使用 `[GC]::Collect()` 立即執行記憶體回收。 這些動作會減少 Runbook 在執行階段的記憶體使用量。

* 遵循[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)步驟來更新 Azure 模組。  

* 另一個解決方案是在[混合式 Runbook 背景工作角色](../automation-hrw-run-runbooks.md)上執行 Runbook。 「混合式背景工作角色」不受限於 Azure 沙箱所受的記憶體和網路限制。

* 如果您需要在 Runbook 中呼叫處理序 (例如 .exe 或 subprocess.call)，您將必須在[混合式 Runbook 背景工作角色](../automation-hrw-run-runbooks.md)上執行 Runbook。

* 在 作業輸出資料流上沒有 1 MB 的限制。 請確定您將可執行檔或表示子處理序的呼叫括在 try/catch 區塊。 如果則會擲回例外狀況，請從自動化變數到該例外狀況寫入訊息。 這可防止它被寫入作業的輸出資料流。

### <a name="fails-deserialized-object"></a>案例：Runbook 因還原序列化物件而失敗

#### <a name="issue"></a>問題

您的 Runbook 失敗，錯誤為：

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>原因

如果您的 Runbook 是「PowerShell 工作流程」，它會以還原序列化格式儲存複雜物件，以在暫止工作流程時保存 Runbook 狀態。

#### <a name="resolution"></a>解決方案

下列三個解決方案的任何一個可以修正此問題：

1. 如果您要透過管線將複雜物件從一個 Cmdlet 傳送到另一個 Cmdlet，請將這些 Cmdlet 包裝在 InlineScript 中。
2. 從複雜物件傳遞您需要的名稱或值，而非傳遞整個物件。
3. 使用 PowerShell Runbook，而不是 PowerShell 工作流程 Runbook。

### <a name="runbook-fails"></a>案例：我的 Runbook 失敗，但會在本機執行時運作

#### <a name="issue"></a>問題

您的指令碼在作為 Runbook 執行時失敗，但會在本機執行時運作。

#### <a name="cause"></a>原因

您的指令碼在作為 Runbook 執行時，可能因為下列其中一個原因而失敗：

1. 驗證問題
2. 必要的模組並未匯入或已過期。
3. 您的指令碼可能提示要進行使用者互動。
4. 某些模組會對 Windows 電腦上出現的程式庫進行相關假設。 這些程式庫可能不存在於沙箱上。
5. 某些模組依賴的 .NET 版本與沙箱上的可用版本不同。

#### <a name="resolution"></a>解決方案

下列任一個解決方案可能可修正此問題：

1. 確認您已正確地[向 Azure 進行驗證](../manage-runas-account.md)。
2. 確定您的 [Azure 模組已匯入且為最新狀態](../automation-update-azure-modules.md)。
3. 確認您的所有 Cmdlet 都未提示要輸入資訊。 Runbook 中不支援這種行為。
4. 檢查您的模組是否有任何部分相依於模組中未包含的項目。
5. Azure 沙箱會使用 .NET Framework 4.7.2，如果模組使用較新版本，它將無法運作。 在此情況下，您應該使用[混合式 Runbook 背景工作角色](../automation-hybrid-runbook-worker.md)

如果這些解決方案均無法解決您的問題，請檢閱[作業記錄](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)，以取得可能導致 Runbook 失敗之原因的特定詳細資料。

### <a name="quota-exceeded"></a>案例：Runbook 作業失敗，因為超過已配置的配額

#### <a name="issue"></a>問題

您的 Runbook 作業失敗，錯誤為：

```error
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>原因

當作業執行超過您的帳戶的 500 分鐘免費配額時，會發生此錯誤。 這個配額會套用至所有類型的作業執行工作。 這其中部分工作可能會測試作業、從入口網站中啟動作業、使用 Webhook 執行作業，或者使用 Azure 入口網站或您的資料中心排程作業來執行。 若要深入了解自動化的價格，請參閱[自動化價格](https://azure.microsoft.com/pricing/details/automation/)。

#### <a name="resolution"></a>解決方案

如果您每個月想要使用超過 500 分鐘的處理時間，您必須將您的訂用帳戶從免費層變更為基本層。 您可以採取下列步驟，升級至基本層：  

1. 登入您的 Azure 訂用帳戶：  
2. 選取您想要升級的自動化帳戶  
3. 按一下 [設定]   > [價格]  。
4. 按一下頁面底端的 [啟用]  ，將您的帳戶升級至**基本**層。

### <a name="cmdlet-not-recognized"></a>案例：執行 Runbook 時，無法辨識 Cmdlet

#### <a name="issue"></a>問題

您的 Runbook 作業失敗，錯誤為：

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>原因

當 PowerShell 引擎找不到您在 Runbook 中使用的 Cmdlet 時，就會發生此錯誤。 這個錯誤可能是因為帳戶中遺漏了包含該 Cmdlet 的模組、與 Runbook 名稱發生名稱衝突，或該 Cmdlet 也存在於另一個模組中而自動化無法解析名稱。

#### <a name="resolution"></a>解決方案

下列任何一個解決方案都可以修正此問題：  

* 確認您已正確輸入 Cmdlet 名稱。  
* 請確定 Cmdlet 存在於您的自動化帳戶中且沒有衝突。 如要確認 Cmdlet 是否存在，請以編輯模式開啟 Runbook，然後搜尋您想要在程式庫中尋找的 Cmdlet，或執行 `Get-Command <CommandName>`。 在驗證 Cmdlet 可供帳戶使用，且與其他 Cmdlet 或 Runbook 沒有名稱衝突之後，請將其新增至畫布，並確定您在 Runbook 中使用有效的參數集。  
* 如果有名稱衝突且 Cmdlet 可在兩個不同的模組中使用，您可以使用 Cmdlet 的完整名稱來解決此問題。 例如，您可以使用 **ModuleName\CmdletName**。  
* 如果您是以混合式背景工作角色群組身分在內部部署環境中執行 Runbook，則請確定模組和 Cmdlet 是安裝在裝載混合式背景工作角色的機器上。

### <a name="long-running-runbook"></a>案例：長時間執行的 Runbook 無法完成

#### <a name="issue"></a>問題

您的 Runbook 在執行 3 小時之後顯示為 [已停止]  狀態。 您可能還收到此錯誤：

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

這是 Azure 沙箱中故意設計的行為，原因是 Azure 自動化中會進行處理序「公平共用」監視。 如果執行時間超過三小時，公平共用就會自動停止 Runbook。 超過公平共用時間限制時的 Runbook 狀態會依 Runbook 類型而有所不同。 PowerShell 和 Python Runbook 是設定為 [已停止]  狀態。 「PowerShell 工作流程」Runbook 是設定為 [失敗]  狀態。

#### <a name="cause"></a>原因

Runbook 的執行時間已超過「Azure 沙箱」中公平共用所允許的 3 小時限制。

#### <a name="resolution"></a>解決方案

其中一個建議的解決方案是在[混合式 Runbook 背景工作角色](../automation-hrw-run-runbooks.md)上執行 Runbook。

「混合式背景工作角色」不受限於 Azure 沙箱所受的[公平共用](../automation-runbook-execution.md#fair-share) 3 小時 Runbook 限制。 開發在混合式 Runbook 背景工作角色上執行的 Runbook 時，應使它們支援重新啟動行為，以免發生非預期的本機基礎結構問題。

另一個選項是藉由建立[子 Runbook](../automation-child-runbooks.md) 將 Runbook 最佳化。 如果您的 Runbook 會在多個資源上重複執行同一個函式，例如在數個資料庫上重複進行某一個資料庫作業，您可以將該函式移到子 Runbook。 每一個 Runbook 會在個別的處理程序中平行執行。 此行為可減少完成父代 Runbook 的時間總計。

啟用子 Runbook 案例的 PowerShell Cmdlet 是：

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) - 此 Cmdlet 可讓您啟動 Runbook，並將參數傳遞給 Runbook

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) - 如果有需要在子 Runbook 完成後執行的作業，此 Cmdlet 可讓您檢查每個子項的作業狀態。

### <a name="expired webhook"></a>案例：狀態：呼叫 Webhook 時出現「400 不正確的要求」

#### <a name="issue"></a>問題

在您嘗試叫用 Azure 自動化 Runbook 的 Webhook 時，收到下列錯誤：

```error
400 Bad Request : This webhook has expired or is disabled
```

#### <a name="cause"></a>原因

您嘗試呼叫的 Webhook 已停用或過期。

#### <a name="resolution"></a>解決方案

如果 Webhook 已停用，您可以透過 Azure 入口網站重新啟用 Webhook。 如果 Webhook 已過期，就必須先刪除 Webhook 再加以重新建立。 如果尚未過期，您只能[更新 Webhook](../automation-webhooks.md#renew-webhook)。

### <a name="429"></a>案例：429：目前的要求率太大。 請再試一次

#### <a name="issue"></a>問題

您在執行 `Get-AzureRmAutomationJobOutput` Cmdlet 時出現下列錯誤訊息：

```error
429: The request rate is currently too large. Please try again
```

#### <a name="cause"></a>原因

從有許多[詳細資訊資料流](../automation-runbook-output-and-messages.md#verbose-stream)的 Runbook 中擷取作業輸出時，就可能會發生此錯誤。

#### <a name="resolution"></a>解決方案

有兩種方法可以解決此錯誤：

* 編輯 Runbook，並減少它所發出的作業資料流數目。
* 減少在執行 Cmdlet 時所要擷取的資料流數目。 若要這麼做，您可以指定讓 `Get-AzureRmAutomationJobOutput` Cmdlet 的 `-Stream Output` 參數僅擷取輸出資料流。 

### <a name="cannot-invoke-method"></a>案例：PowerShell 工作失敗，錯誤：無法叫用方法

#### <a name="issue"></a>問題

開始在 Azure 中執行 runbook 中的 PowerShell 工作時，您會收到下列錯誤訊息：

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

#### <a name="cause"></a>原因

當您啟動的 PowerShell，在 Azure 中執行的 runbook 中的工作，可能會發生此錯誤。 這種行為可能是因為在 Azure 中執行的 runbook 無法執行沙箱[完整的語言模式](/powershell/module/microsoft.powershell.core/about/about_language_modes))。

#### <a name="resolution"></a>解決方案

有兩種方法可以解決此錯誤：

* 而不是使用`Start-Job`，使用`Start-AzureRmAutomationRunbook`來啟動 runbook
* 如果您的 runbook 會有此錯誤訊息，其混合式 Runbook 背景工作角色上執行

若要深入了解 Azure 自動化 Runbook 的其他行為，此種行為，請參閱[Runbook 行為](../automation-runbook-execution.md#runbook-behavior)。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答
* 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫－專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
* 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]  。
