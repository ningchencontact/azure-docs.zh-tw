---
title: 如何建立來賓設定原則
description: 瞭解如何建立適用于 Windows 或 Linux Vm 的 Azure 原則來賓設定原則。
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 235ad37c5cf5f8ac7e801a6d25e961d32c1b7aad
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164910"
---
# <a name="how-to-create-guest-configuration-policies"></a>如何建立來賓設定原則

「來賓設定」會使用[Desired State Configuration](/powershell/dsc) (DSC) 資源模組來建立 Azure 機器的審核設定。 DSC 設定會定義機器應處於的條件。 如果設定的評估失敗, 則會觸發原則效果**auditIfNotExists** , 並將機器視為**不相容**。

[Azure 原則來賓](/azure/governance/policy/concepts/guest-configuration)設定只能用來審查電腦內的設定。 尚未提供機器內設定的補救功能。

使用下列動作來建立您自己的設定, 以驗證 Azure 機器的狀態。

> [!IMPORTANT]
> 具有「來賓設定」的自訂原則是一項預覽功能。

## <a name="add-the-guestconfiguration-resource-module"></a>新增 GuestConfiguration 資源模組

若要建立來賓設定原則, 必須新增資源模組。 此資源模組可以搭配使用本機安裝的 PowerShell、 [Azure Cloud Shell](https://shell.azure.com)或[Azure PowerShell Docker 映射](https://hub.docker.com/rsdk-powershell/)。

### <a name="base-requirements"></a>基底需求

來賓設定資源模組需要下列軟體:

- PowerShell. 如果尚未安裝，請依照[這些指示](/powershell/scripting/install/installing-powershell)操作。
- Azure PowerShell 1.5.0 或更高版本。 如果尚未安裝，請依照[這些指示](/powershell/azure/install-az-ps)操作。

### <a name="install-the-module"></a>安裝模組

「來賓設定」會使用**GuestConfiguration**資源模組來建立 DSC 設定, 並將其發佈至 Azure 原則:

1. 從 PowerShell 提示字元中執行下列命令：

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. 驗證模組是否已匯入:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="create-custom-guest-configuration-configuration"></a>建立自訂來賓設定設定

建立來賓設定之自訂原則的第一個步驟是建立 DSC 設定。 如需 DSC 概念和術語的總覽, 請參閱[POWERSHELL Dsc 總覽](/powershell/dsc/overview/overview)。

### <a name="custom-guest-configuration-configuration-on-linux"></a>Linux 上的自訂來賓設定設定

Linux 上的「來賓設定」 DSC 設定會`ChefInSpecResource`使用資源為引擎提供[Chef InSpec](https://www.chef.io/inspec/)定義的名稱。 [**名稱**] 是唯一必要的資源屬性。

下列範例會建立名為**基準**的設定、匯入**GuestConfiguration**資源模組, `ChefInSpecResource`並使用資源將 InSpec 定義的名稱設定為**linux-patch-基準**:

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration baseline
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    ChefInSpecResource 'Audit Linux patch baseline'
    {
        Name = 'linux-patch-baseline'
    }
}

# Compile the configuration to create the MOF files
baseline
```

如需詳細資訊, 請參閱[撰寫、編譯及](/powershell/dsc/configurations/write-compile-apply-configuration)套用設定。

### <a name="custom-guest-configuration-configuration-on-windows"></a>Windows 上的自訂來賓設定設定

只有來賓設定代理程式會使用 Azure 原則來賓設定的 DSC 設定, 它不會與 Windows PowerShell Desired State Configuration 衝突。

下列範例會建立名為**AuditBitLocker**的設定、匯入**GuestConfiguration**資源模組, `Service`並使用資源來審查執行中的服務:

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Service 'Ensure BitLocker service is present and running'
    {
        Name = 'BDESVC'
        Ensure = 'Present'
        State = 'Running'
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker
```

如需詳細資訊, 請參閱[撰寫、編譯及](/powershell/dsc/configurations/write-compile-apply-configuration)套用設定。

## <a name="create-guest-configuration-custom-policy-package"></a>建立來賓設定自訂原則封裝

一旦完成 MOF 編譯, 就必須將支援的檔案封裝在一起。 「來賓設定」會使用完成的封裝來建立 Azure 原則定義。 套件包含:

- 以 MOF 形式編譯的 DSC 設定
- Modules 資料夾
  - GuestConfiguration 模組
  - DscNativeResources 模組
  - 廠商具有 Chef InSpec 定義和其他內容的資料夾
  - 時段未內建的 DSC 資源模組

`New-GuestConfigurationPackage` Cmdlet 會建立封裝。 下列格式是用來建立自訂套件:

```azurepowershell-interactive
New-GuestConfigurationPackage -Name '{PackageName}' -Configuration '{PathToMOF}' `
    -Path '{OutputFolder}' -Verbose
```

`New-GuestConfigurationPackage` Cmdlet 的參數:

- **名稱**：來賓設定套件名稱。
- **設定**：已編譯的 DSC 設定檔完整路徑。
- **路徑**：輸出檔案夾路徑。 這個參數是選擇性的。 如果未指定, 則會在目前的目錄中建立封裝。
- **ChefProfilePath**:InSpec 設定檔的完整路徑。 只有在建立內容以 audit Linux 時, 才支援此參數。

完成的封裝必須儲存在受管理的虛擬機器可存取的位置。 範例包括 GitHub 存放庫、Azure 儲存機制或 Azure 儲存體。 如果您不想讓套件公開, 您可以在 URL 中包含[SAS 權杖](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md)。 雖然這項設定只適用于存取封裝, 而不是與服務通訊, 但您也可以在私人網路中執行機器的[服務端點](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)。

### <a name="working-with-secrets-in-guest-configuration-packages"></a>使用來賓設定套件中的秘密

在 Azure 原則來賓設定中, 管理在執行時間使用之秘密的最佳方式是將它們儲存在 Azure Key Vault 中。 這種設計會在自訂 DSC 資源內執行。

首先, 在 Azure 中建立使用者指派的受控識別。 電腦會使用此身分識別來存取儲存在 Key Vault 中的秘密。 如需詳細步驟, 請參閱[使用 Azure PowerShell 建立、列出或刪除使用者指派的受控識別](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)。

接下來, 建立 Key Vault 實例。 如需詳細步驟, 請參閱[設定和取出秘密-PowerShell](../../../key-vault/quick-create-powershell.md)。
指派許可權給實例, 以授與使用者指派的身分識別, 存取儲存在 Key Vault 中的秘密。 如需詳細步驟, 請參閱[設定和取出密碼-.net](../../../key-vault/quick-create-net.md#give-the-service-principal-access-to-your-key-vault)。

然後, 將使用者指派的身分識別指派給您的電腦。 如需詳細步驟, 請參閱[使用 PowerShell 在 AZURE VM 上設定 azure 資源的受控](../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md#user-assigned-managed-identity)識別。
請大規模使用 Azure Resource Manager 透過 Azure 原則指派此身分識別。 如需詳細步驟, 請參閱[使用範本在 AZURE VM 上設定 azure 資源的受控](../../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#assign-a-user-assigned-managed-identity-to-an-azure-vm)識別。

最後, 在您的自訂資源內使用上述產生的用戶端識別碼, 以使用機器提供的權杖來存取 Key Vault。 Key Vault `client_id`實例的和 url 可以當做[屬性](/powershell/dsc/resources/authoringresourcemof#creating-the-mof-schema)傳遞至資源, 因此不需要更新多個環境的資源, 或如果需要變更這些值。

下列程式碼範例可用於自訂資源, 以使用使用者指派的身分識別從 Key Vault 取出秘密。 從要求傳回到 Key Vault 的值是純文字。 最佳做法是將它儲存在 credential 物件中。

```azurepowershell-interactive
# the following values should be input as properties
$client_id = 'e3a78c9b-4dd2-46e1-8bfa-88c0574697ce'
$keyvault_url = 'https://keyvaultname.vault.azure.net/secrets/mysecret'

$access_token = ((Invoke-WebRequest -Uri "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=$client_id&resource=https%3A%2F%2Fvault.azure.net" -Method GET -Headers @{Metadata='true'}).Content | ConvertFrom-Json).access_token

$value = ((Invoke-WebRequest -Uri $($keyvault_url+'?api-version=2016-10-01') -Method GET -Headers @{Authorization="Bearer $access_token"}).content | convertfrom-json).value |  ConvertTo-SecureString -asplaintext -force

$credential = New-Object System.Management.Automation.PSCredential('secret',$value)
```

## <a name="test-a-guest-configuration-package"></a>測試來賓設定套件

建立設定套件之後, 但在將它發佈至 Azure 之前, 您可以從您的工作站或 CI/CD 環境測試套件的功能。 GuestConfiguration 模組包含一個 Cmdlet `Test-GuestConfigurationPackage` , 它會在您的開發環境中載入與 Azure 機器內部使用的相同代理程式。 使用此解決方案, 您可以在發行至計費的測試/QA/生產環境之前, 在本機執行整合測試。

```azurepowershell-interactive
Test-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Verbose
```

`Test-GuestConfigurationPackage` Cmdlet 的參數:

- **名稱**：來賓設定原則名稱。
- **參數**:以 hashtable 格式提供的原則參數。
- **路徑**：來賓設定套件的完整路徑。

此 Cmdlet 也支援來自 PowerShell 管線的輸入。 將`New-GuestConfigurationPackage` Cmdlet 的輸出傳送`Test-GuestConfigurationPackage`至 Cmdlet。

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditWindowsService -Configuration .\DSCConfig\localhost.mof -Path .\package -Verbose | Test-GuestConfigurationPackage -Verbose
```

如需如何使用參數進行測試的詳細資訊, 請參閱下面的[使用自訂來賓設定原則中的參數](/azure/governance/policy/how-to/guest-configuration-create#using-parameters-in-custom-guest-configuration-policies)一節。

## <a name="create-the-azure-policy-definition-and-initiative-deployment-files"></a>建立 Azure 原則定義和計畫部署檔案

建立來賓設定自訂原則封裝並上傳至電腦可存取的位置之後, 請建立 Azure 原則的來賓設定原則定義。 此`New-GuestConfigurationPolicy` Cmdlet 會使用可公開存取的來賓設定自訂原則套件, 並建立**auditIfNotExists**和**deployIfNotExists**原則定義。 同時也會建立同時包含這兩個原則定義的原則計畫定義。

下列範例會從適用于 Windows 的來賓設定自訂原則套件, 在指定的路徑中建立原則和計畫定義, 並提供名稱、描述和版本:

```azurepowershell-interactive
New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

`New-GuestConfigurationPolicy` Cmdlet 的參數:

- **ContentUri**:來賓設定內容套件的公用 HTTP (s) uri。
- **DisplayName**:原則顯示名稱。
- **描述**：原則描述。
- **參數**:以 hashtable 格式提供的原則參數。
- **版本**：原則版本。
- **路徑**：建立原則定義的目的地路徑。
- **平臺**:適用于來賓設定原則和內容套件的目標平臺 (Windows/Linux)。

下列檔案是由`New-GuestConfigurationPolicy`所建立:

- **auditIfNotExists json**
- **deployIfNotExists json**
- **方案. json**

Cmdlet 輸出會傳回物件, 其中包含原則檔的計畫顯示名稱和路徑。

如果您想要使用此命令來 scaffold 自訂原則專案, 您可以對這些檔案進行變更。 其中一個範例會修改 ' If ' 區段, 以評估機器是否有特定標記。 如需建立原則的詳細資訊, 請參閱以程式設計[方式建立原則](./programmatically-create.md)。

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>在自訂來賓設定原則中使用參數

「來賓設定」支援在執行時間覆寫設定的屬性。 這項功能表示封裝中 MOF 檔案中的值不一定要被視為靜態。 覆寫值是透過 Azure 原則提供, 而且不會影響撰寫或編譯設定的方式。

Cmdlet `New-GuestConfigurationPolicy`和`Test-GuestConfigurationPolicyPackage`包含名為**Parameters**的參數。
這個參數會採用 hashtable 定義, 包括每個參數的所有詳細資料, 並自動建立用來建立每個 Azure 原則定義之檔案的所有必要區段。

下列範例會建立 Azure 原則來審核服務, 而使用者會在原則指派時從服務清單中選取。

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameters $PolicyParameterInfo `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

針對 Linux 原則, 請在您`AttributesYmlContent`的設定中包含屬性, 並據以覆寫值。 來賓設定代理程式會自動建立 InSpec 用來儲存屬性的 YaML 檔案。 請參閱下方的範例。

```azurepowershell-interactive
Configuration FirewalldEnabled {

    Import-DscResource -ModuleName 'GuestConfiguration'

    Node FirewalldEnabled {

        ChefInSpecResource FirewalldEnabled {
            Name = 'FirewalldEnabled'
            AttributesYmlContent = "DefaultFirewalldProfile: [public]"
        }
    }
}
```

針對每個額外的參數, 將 hashtable 加入至陣列。 在原則檔案中, 您會看到新增至 [來賓設定] configurationName 的屬性, 可識別資源類型、名稱、屬性和值。

```json
{
    "apiVersion": "2018-11-20",
    "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
    "name": "[concat(parameters('vmName'), '/Microsoft.GuestConfiguration/', parameters('configurationName'))]",
    "location": "[parameters('location')]",
    "properties": {
        "guestConfiguration": {
            "name": "[parameters('configurationName')]",
            "version": "1.*",
            "configurationParameter": [{
                "name": "[Service]windowsService;Name",
                "value": "[parameters('ServiceName')]"
            }]
        }
    }
}
```

## <a name="publish-to-azure-policy"></a>發行至 Azure 原則

**GuestConfiguration** resource 模組可讓您透過`Publish-GuestConfigurationPolicy` Cmdlet, 在 Azure 中建立原則定義和計畫定義。
Cmdlet 只有**Path**參數, 它會指向所建立`New-GuestConfigurationPolicy`之三個 JSON 檔案的位置。

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions' -Verbose
```

此`Publish-GuestConfigurationPolicy` Cmdlet 會接受來自 PowerShell 管線的路徑。 這項功能表示您可以建立原則檔案, 並在一組輸送的命令中發佈它們。

```azurepowershell-interactive
New-GuestConfigurationPolicy -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' -DisplayName 'Audit BitLocker service.' -Description 'Audit if the BitLocker service is not enabled on Windows machine.' -Path '.\policyDefinitions' -Platform 'Windows' -Version 1.2.3.4 -Verbose | ForEach-Object {$_.Path} | Publish-GuestConfigurationPolicy -Verbose
```

透過在 Azure 中建立的原則和計畫定義, 最後一步就是指派方案。 瞭解如何使用[入口網站](../assign-policy-portal.md)、 [Azure CLI](../assign-policy-azurecli.md)和[Azure PowerShell](../assign-policy-powershell.md)來指派方案。

> [!IMPORTANT]
> 來賓設定原則必須**一律**使用結合_AuditIfNotExists_和_DeployIfNotExists_原則的計畫來指派。 如果只指派_AuditIfNotExists_原則, 則不會部署必要條件, 原則一律會顯示「0」伺服器符合規範。

## <a name="policy-lifecycle"></a>原則生命週期

當您使用自訂內容套件發佈自訂 Azure 原則之後, 如果您想要發佈新的版本, 則必須更新兩個欄位。

- **版本**：當您執行`New-GuestConfigurationPolicy` Cmdlet 時, 您必須指定大於目前發行的版本號碼。  屬性會更新新原則檔案中的來賓設定指派版本, 讓擴充功能能夠辨識封裝已更新。
- **contentHash**:`New-GuestConfigurationPolicy` Cmdlet 會自動更新此屬性。  這是所建立`New-GuestConfigurationPackage`之封裝的雜湊值。  屬性對於您發行的`.zip`檔案而言必須是正確的。  如果只`contentUri`更新屬性 (例如, 在有人可以從入口網站手動變更原則定義的情況下), 延伸模組就不會接受內容套件。

釋放更新套件的最簡單方式是重複本文中所述的程式, 並提供更新的版本號碼。
該進程可確保所有屬性都已正確更新。

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>將 Windows 群組原則內容轉換成 Azure 原則來賓設定

「來賓設定」在「Windows 機器」執行時, 是 PowerShell Desired State Configuration 語法的實施。
DSC 社區已發佈工具, 可將匯出的群組原則範本轉換成 DSC 格式。
將此工具與上述的來賓設定 Cmdlet 搭配使用, 您就可以轉換 Windows 群組原則內容, 並將其封裝/發佈以供 Azure 原則進行審核。
如需使用此工具的詳細資訊, 請[參閱快速入門:將群組原則轉換成](/powershell/dsc/quickstarts/gpo-quickstart)DSC。
內容轉換後, 上述步驟會建立套件, 並將其發佈為 Azure 原則將與任何 DSC 內容相同。

## <a name="optional-signing-guest-configuration-packages"></a>選擇性：簽署來賓設定套件

根據預設, 來賓設定自訂原則會使用 SHA256 雜湊來驗證原則封裝是否已從發行到正在進行審核的伺服器讀取時, 不會變更。
(選擇性) 客戶也可以使用憑證來簽署套件, 並強制來賓設定延伸模組只允許已簽署的內容。

若要啟用此案例, 您需要完成兩個步驟。 執行 Cmdlet 來簽署內容套件, 並將標記附加至需要簽署程式碼的電腦上。

若要使用簽章驗證功能, 請`Protect-GuestConfigurationPackage`執行 Cmdlet 來簽署封裝, 然後再發佈。 此 Cmdlet 需要「程式碼簽署」憑證。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

`Protect-GuestConfigurationPackage` Cmdlet 的參數:

- **路徑**：來賓設定套件的完整路徑。
- **憑證**:用來簽署封裝的程式碼簽署憑證。 只有在簽署 Windows 的內容時, 才支援這個參數。
- **PrivateGpgKeyPath**:私用 GPG 機碼路徑。 只有在簽署適用于 Linux 的內容時, 才支援此參數。
- **PublicGpgKeyPath**:公用 GPG 金鑰路徑。 只有在簽署適用于 Linux 的內容時, 才支援此參數。

GuestConfiguration 代理程式預期憑證公開金鑰會出現在 Windows 電腦上的「受信任的根憑證授權」中, 以及`/usr/local/share/ca-certificates/extra` Linux 電腦上的路徑中。 若要讓節點驗證已簽署的內容, 請先在電腦上安裝憑證公開金鑰, 再套用自訂原則。 此程式可以使用 VM 內的任何技術來完成, 或使用 Azure 原則。 [這裡提供](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)範例範本。
Key Vault 存取原則必須允許計算資源提供者在部署期間存取憑證。 如需詳細步驟, 請參閱[在 Azure Resource Manager 中設定虛擬機器的 Key Vault](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)。

以下是從簽署憑證匯出公開金鑰以匯入到電腦的範例。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

建立 GPG 金鑰以搭配 Linux 機器使用的良好參考, 是由 GitHub 上的文章所提供,[產生新的 GPG 金鑰](https://help.github.com/en/articles/generating-a-new-gpg-key)。

發佈內容之後, 請將具有名稱`GuestConfigPolicyCertificateValidation`和值`enabled`的標籤, 附加至需要程式碼簽署的所有虛擬機器。 您可以使用 Azure 原則, 大規模地傳遞這個標記。 請參閱[Apply 標記及其預設值](../samples/apply-tag-default-value.md)範例。
一旦此標記已就緒, 使用`New-GuestConfigurationPolicy` Cmdlet 產生的原則定義就會透過「來賓設定」延伸模組來啟用需求。

## <a name="next-steps"></a>後續步驟

- 瞭解如何使用[來賓](../concepts/guest-configuration.md)設定來進行 vm 的審核。
- 瞭解如何以程式設計[方式建立原則](programmatically-create.md)。
- 瞭解如何[取得合規性資料](getting-compliance-data.md)。
