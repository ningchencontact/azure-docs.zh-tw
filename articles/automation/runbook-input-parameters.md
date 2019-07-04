---
title: Runbook 輸入參數
description: Runbook 輸入參數可讓您將資料傳遞至剛啟動的 Runbook，以增加 Runbook 的彈性。 本文說明在 Runbook 中使用輸入參數的不同案例。
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5f190d60a059108b9763f35e2ee8cf99ae77b694
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60499960"
---
# <a name="runbook-input-parameters"></a>Runbook 輸入參數

Runbook 輸入參數可讓您將資料傳遞至剛啟動的 Runbook，以增加 Runbook 的彈性。 這些參數可讓Runbook 動作以特定案例和環境做為目標。 在本文中，您會逐步進行在 Runbook 中使用輸入參數的不同案例。

## <a name="configure-input-parameters"></a>設定輸入參數

輸入參數可以在 PowerShell、PowerShell 工作流程、Python 和圖形化 Runbook 中設定。 一個 Runbook 可以使用具有不同資料類型的多個參數，或完全不使用參數。 輸入參數可以是強制性或選擇性的，而且您可以為選擇性參數設定預設值。 您透過其中一種可用方法在啟動 Runbook 時，指派 Runbook 的輸入參數值。 這些方法包括從 Azure 入口網站、Web 服務或 PowerShell 啟動 Runbook。 您也可以啟動一個 Runbook 做為在另一個 Runbook 中以內嵌方式呼叫的子 Runbook。

## <a name="configure-input-parameters-in-powershell-runbooks"></a>在 PowerShell Runbook 中設定輸入參數

Azure 自動化中的 PowerShell 和 PowerShell 工作流程 Runbook 支援透過下列屬性定義的輸入參數：  

| **屬性** | **說明** |
|:--- |:--- |
| `Type` |必要。 對參數值預期的資料類型。 任何 .NET 類型皆有效。 |
| `Name` |必要。 參數名稱。 這在 Runbook 中必須是唯一的，並且只能包含字母、數字或底線字元。 而且必須以字母開頭。 |
| `Mandatory` |選用。 指定是否必須提供參數的值。 如果您將此項目設定為 **\$true**，則在 Runbook 啟動時必須提供其值。 如果您將此項目設定為 **\$false**，則該值是選擇性的。 |
| `Default value` |選用。 指定在 Runbook 啟動時未傳遞值的情況下所將用於參數的值。 任何參數皆可設定為預設值，此值將會使參數自動成為選擇性，無論 [強制] 設定為何。 |

Windows PowerShell 所支援的輸入參數屬性比此處所列的多，例如驗證、別名和參數集。 不過，Azure 自動化目前僅支援上述的輸入參數。

PowerShell 工作流程 Runbook 中的參數定義具有下列一般形式，其中，多個參數會以逗號分隔。

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

> [!NOTE]
> 定義參數時，如果您未指定 **Mandatory** 屬性，依預設會將該參數視為選擇性。 此外，如果您在 PowerShell 工作流程 Runbook 中設定某個參數的預設值，則 Powershell 會將其視為選擇性參數，無論其 **Mandatory** 屬性值為何。

舉例來說，我們為輸出虛擬機器 (可以是單一 VM 或資源群組內的所有 VM) 相關詳細資料的 PowerShell 工作流程 Runbook 設定輸入參數。 如以下螢幕擷取畫面所示，此 Runbook 有兩個參數：虛擬機器的名稱和資源群組的名稱。

![自動化 PowerShell 工作流程](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

在此參數定義中， **\$VMName** 和 **\$resourceGroupName** 參數是字串類型的簡單參數。 不過，PowerShell 和 PowerShell 工作流程 Runbook 支援所有簡單類型和複雜類型，例如輸入參數的 **object** 或 **PSCredential**。

如果您的 Runbook 有 object 類型的輸入參數，則使用具有 (name, value) 配對的 PowerShell 雜湊表來傳入值。 例如，如果您在 Runbook 中有下列參數：

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

則您可以將下列值傳遞至參數：

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> 當您未將任何值傳遞至 _預設值_ 為 `\$null` 的選用 `[String]` 類型參數時，參數的值會是 _空白字串_ ，而**不是** `\$null`。

## <a name="configure-input-parameters-in-graphical-runbooks"></a>在圖形化 Runbook 中設定輸入參數

為了使用輸入參數[設定圖形化 Runbook](automation-first-runbook-graphical.md)，我們將建立會輸出虛擬機器 (可以是單一 VM 或資源群組內的所有 VM) 相關詳細資料的圖形化 Runbook。 設定 Runbook 包含兩個主要活動，如下所述。

[**使用 Azure 執行身分帳戶驗證 Runbook**](automation-sec-configure-azure-runas-account.md) 以 Azure 驗證。

以 [**Get-AzureRmVm**](/powershell/module/azurerm.compute/get-azurermvm) 取得虛擬機器的屬性。

您可以使用 [**Write-Output**](/powershell/module/microsoft.powershell.utility/write-output) 活動來輸出虛擬機器的名稱。 **Get-AzureRmVm** 活動會接受兩個參數：**虛擬機器名稱**和**資源群組名稱**。 由於這些參數在您每次啟動 Runbook 時可能需要不同的值，因此您可以將輸入參數新增至您的 Runbook。 以下是新增輸入參數的步驟：

1. 從 [Runbook]  刀鋒視窗中選取圖形化 Runbook，然後按一下 [**編輯**](automation-graphical-authoring-intro.md)。
2. 在 Runbook 編輯器中，按一下 [輸入和輸出]  以開啟 [輸入和輸出]  刀鋒視窗。

   ![自動化圖形化 Runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. [輸入和輸出]  刀鋒視窗會顯示為 Runbook 定義的輸入參數清單。 在此刀鋒視窗上，您可以新增輸入參數，或編輯現有輸入參數的組態。 若要為 Runbook 新增參數，請按一下 [新增輸入]  以開啟 [Runbook 輸入參數]  刀鋒視窗。 您可以在其中設定下列連線參數：

   | **屬性** | **說明** |
   |:--- |:--- |
   | `Name` |必要。 參數名稱。 這在 Runbook 中必須是唯一的，並且只能包含字母、數字或底線字元。 而且必須以字母開頭。 |
   | `Description` |選用。 關於輸入參數用途的說明。 |
   | `Type` |選用。 對參數值預期的資料類型。 支援的參數類型有：**String**、**Int32**、**Int64**、**Decimal**、**Boolean**、**DateTime**、**Object**。 若未選取資料類型，將預設為 **String**。 |
   | `Mandatory` |選用。 指定是否必須提供參數的值。 如果您選擇 [是]  ，則在 Runbook 啟動時必須提供其值。 如果您選擇 [否]  ，則在 Runbook 啟動時不一定需要值，並且可設定預設值。 |
   | `Default Value` |選用。 指定在 Runbook 啟動時未傳遞值的情況下所將用於參數的值。 不是強制性的參數可以設定預設值。 若要設定預設值，請選擇 [自訂]  。 除非在 Runbook 啟動時提供了其他值，否則將會使用此值。 若不想提供任何預設值，請選擇 [無]  。 |

    ![加入新的輸入](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. 使用下列屬性，建立由 **Get-AzureRmVm** 活動使用的兩個參數：

   * **參數1：**
     * Name - VMName
     * Type - String
     * Mandatory - No
   * **參數 2：**
     * Name - resourceGroupName
     * Type - String
     * Mandatory - No
     * Default value - Custom
     * 自訂預設值 - \<包含虛擬機器之資源群組的名稱\>

5. 新增參數之後，請按一下 [確定]  。 現在，您可以在 [輸入及輸出]  頁面中檢視它們。 再按一下 [確定]  ，然後按一下 [儲存]  並 [發佈]  您的 Runbook。

## <a name="configure-input-parameters-in-python-runbooks"></a>在 Python Runbook 中設定輸入參數

Python Runbook 與 PowerShell、PowerShell 工作流程及圖形化 Runbook 不同，其不使用具名參數。
所有輸入參數都會經過剖析，成為引數值的陣列。
若要存取陣列，您可以將 `sys` 模組匯入 Python 指令碼，接著再使用 `sys.argv` 陣列。
請務必注意，陣列的第一個元素 (`sys.argv[0]`) 是指令碼的名稱，所以第一個實際的輸入參數是 `sys.argv[1]`。

如需在 Python Runbook 中使用輸入參數的範例，請參閱[我在 Azure 自動化中的第一個 Python Runbook](automation-first-runbook-textual-python2.md)。

## <a name="assign-values-to-input-parameters-in-runbooks"></a>將值指派給 Runbook 中的輸入參數

在下列情況下，您可以將值傳遞至 Runbook 中的輸入參數：

### <a name="start-a-runbook-and-assign-parameters"></a>啟動 Runbook 並指派參數

Runbook 有多種啟動方式：透過 Azure 入口網站、透過 Webhook、透過 PowerShell Cmdlet、REST API 或 SDK。 以下我們將討論啟動 Runbook 並指派參數的不同方法。

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>使用 Azure 入口網站啟動已發佈的 Runbook，並指派參數

當您[啟動 Runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal) 時，[啟動 Runbook]  刀鋒視窗隨即開啟，而您可以為所建立的參數輸入值。

![使用入口網站啟動](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

在輸入方塊下的標籤中，您可以查看為參數設定的屬性。 這些屬性包括強制或選擇性、類型和預設值。 在參數名稱旁的球形文字說明中，您可以檢視您在進行參數輸入值相關決策時所需的所有金鑰資訊。 此資訊包括參數為強制或選擇性。 也包括類型和預設值 (如果有的話) 和其他有用的注意事項。

> [!NOTE]
> 字串類型參數支援 **空** 字串值。  在輸入參數文字方塊中輸入 **[EmptyString]** ，會傳遞空字串給參數。 此外，字串類型參數不支援傳遞 **Null** 值。 若未傳遞任何值給字串參數，PowerShell 會將其解譯為 Null。

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>使用 PowerShell Cmdlet 啟動已發佈的 Runbook，並指派參數

* **Azure Resource Manager Cmdlet：** 您可以使用 [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) 啟動在資源群組中建立的自動化 Runbook。
  
  **範例：**

  ```powershell
  $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
  ```

* **Azure 傳統部署模型 Cmdlets：** 您可以使用 [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook) 啟動在預設資源群組中建立的自動化 Runbook。
  
  **範例：**

  ```powershell
  $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
  Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
  ```

> [!NOTE]
> 當您使用 PowerShell Cmdlet 啟動 Runbook 時，將建立值為 **PowerShell** 的預設參數 **MicrosoftApplicationManagementStartedBy**。 您可以在 [作業詳細資料]  頁面中檢視此參數。  

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>使用 SDK 啟動 Runbook，並指派參數

* **Azure Resource Manager 方法：** 您可以使用程式設計語言的 SDK 來啟動 Runbook。 以下 C# 程式碼片段用於在您的自動化帳戶中啟動 Runbook。 您可以在我們的 [GitHub 儲存機制](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)中檢視完整的程式碼。  

  ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
  ```

* **Azure 傳統部署模型方法：** 您可以使用程式設計語言的 SDK 來啟動 Runbook。 以下 C# 程式碼片段用於在您的自動化帳戶中啟動 Runbook。 您可以在我們的 [GitHub 儲存機制](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)中檢視完整的程式碼。

  ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
  ```

  若要啟動此方法，請建立字典來儲存 Runbook 參數、**VMName**、**resourceGroupName** 以及其值。 然後啟動 Runbook。 以下 C# 程式碼片段用於呼叫上面定義的方法。

  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook("Get-AzureVMGraphical", RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>使用 REST API 啟動 Runbook，並指派參數

Runbook 作業可透過 Azure 自動化 REST API，使用 **PUT** 方法和下列要求 URI 來建立及啟動：`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`


在要求 URI 中，取代下列參數：

* **subscriptionId：** 您的 Azure 訂用帳戶識別碼。  
* **resourceGroupName：** 自動化帳戶的資源群組名稱。
* **automationAccountName：** 裝載在指定的雲端服務內的自動化帳戶名稱。  
* **jobName：** 作業的 GUID。 您可以使用 **[GUID]::NewGuid().ToString()** 命令建立 PowerShell 中的 GUID。

若要將參數傳遞至 Runbook 作業，請使用要求本文。 它會採用兩個以 JSON 格式提供的屬性：

* **Runbook 名稱：** 必要。 作業要啟動之 Runbook 的名稱。  
* **Runbook 參數：** 選用。 參數清單的字典；清單必須採用 (名稱, 值) 格式，其中的名稱應為字串類型，而值可以是任何有效的 JSON 值。

如果您想要啟動先前以 **VMName** 和 **resourceGroupName** 做為參數建立的 **Get-AzureVMTextual** Runbook，請使用下列 JSON 格式的要求本文。

   ```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
   ```

如果成功建立作業，則會傳回 HTTP 狀態碼 201。 如需關於回應標頭和回應本文的詳細資訊，請參閱有關如何[使用 REST API 建立 Runbook 作業](/rest/api/automation/job/create)

### <a name="test-a-runbook-and-assign-parameters"></a>測試 Runbook 並指派參數

當您使用測試選項[測試 Runbook 的草稿版本](automation-testing-runbook.md)時，將會開啟 [測試]  分頁，您可以在其中為所建立的參數設定值。

![測試並指派參數](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>將排程連結至 Runbook，並指派參數

您可以[將排程連結](automation-schedules.md)至您的 Runbook，以在特定時間啟動 Runbook。 您會在建立排程時指派輸入參數，且 Runbook 經由排程啟動時，會使用這些值。 必須提供所有必要參數值，才能儲存排程。

![排程並指派參數](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>建立 Runbook 的 Webhook，並指派參數

您可以為您的 Runbook 建立 [Webhook](automation-webhooks.md) ，並設定 Runbook 輸入參數。 必須提供所有必要參數值，才能儲存 Webhook。

![建立 Webhook 並指派參數](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

當您使用 Webhook 執行 Runbook 時，會隨著您定義的輸入參數傳送預先定義的輸入參數 **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** 。 您可以按一下 **WebhookData** 參數加以展開，以取得詳細資訊。

![WebhookData 參數](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>將 JSON 物件傳遞至 Runbook

這適用於在 JSON 檔案中儲存您要傳送至 Runbook 的資料。
例如，您可以建立 JSON 檔案，其中包含您要傳送至 Runbook 的所有參數。 若要這樣做，您必須將 JSON 轉換為字串，然後先將字串轉換為 PowerShell 物件，再將它傳遞至 Runbook。

在此範例中，您有一個 PowerShell 指令碼，該指令碼會呼叫 [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) 以啟動 PowerShell Runbook，並將 JSON 內容傳遞至 Runbook。
PowerShell Runbook 會啟動 Azure VM，並從傳入的 JSON 取得 VM 的參數。

### <a name="create-the-json-file"></a>建立 JSON 檔案

在文字檔案中輸入下列測試，然後在本機電腦上的某處將它儲存為 `test.json`。

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>建立 runbook

在 Azure 自動化中建立名為 "Test-Json" 的新 PowerShell Runbook。
若要了解如何建立新的 PowerShell Runbook，請參閱[我的第一個 PowerShell Runbook](automation-first-runbook-textual-powershell.md)。

若要接受 JSON 資料，Runbook 必須採用物件作為輸入參數。

然後 Runbook 可以使用 JSON 中定義的屬性。

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

在您的自動化帳戶中儲存並發佈此 Runbook。

### <a name="call-the-runbook-from-powershell"></a>從 PowerShell 呼叫 Runbook

您現在可以使用 Azure PowerShell，從本機電腦呼叫 Runbook。
執行下列 PowerShell 命令：

1. 登入 Azure：

   ```powershell
   Connect-AzureRmAccount
   ```

   系統會提示您輸入 Azure 認證。

   > [!IMPORTANT]
   > **Add-AzureRmAccount** 現在是 **Connect-AzureRMAccount** 的別名。 搜尋您的程式庫項目時，如果沒有看到 **Connect-AzureRMAccount**，便可以使用 **Add-AzureRmAccount**，或是在自動化帳戶中更新模組。

1. 取得 JSON 檔案的內容，並將它轉換成字串：

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

   `JsonPath` 是您儲存 JSON 檔案的路徑。

1. 將 `$json` 的字串內容轉換為 PowerShell 物件：

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. 建立 `Start-AzureRmAutomationRunbook` 參數的雜湊表：

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   請注意，您要將 `Parameters` 的值設定為 PowerShell 物件，其中包含 JSON 檔案中的值。
1. 啟動 Runbook

   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>後續步驟

* 如需以不同方式啟動 Runbook 的詳細資訊，請參閱 [啟動 Runbook](automation-starting-a-runbook.md)。
* 若要編輯文字 Runbook，請參閱 [編輯文字 Runbook](automation-edit-textual-runbook.md)。
* 若要編輯圖形化 Runbook，請參閱 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)。
