---
title: 範例 - 稽核應用程式是否未安裝於 Linux VM 內
description: 此範例原則來賓設定方案和定義會稽核指定的應用程式是否未安裝於 Linux 虛擬機器內。
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 03/18/2019
ms.author: dacoulte
ms.openlocfilehash: b432d8557c4244d58c23e7b068874dd747f6249f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256459"
---
# <a name="sample---audit-if-specified-applications-are-not-installed-inside-linux-vms"></a>範例 - 稽核指定的應用程式是否未安裝於 Linux VM 內

此原則來賓設定方案會稽核指定的應用程式安裝於 Linux 虛擬機器內。 此內建方案的識別碼是 `/providers/Microsoft.Authorization/policySetDefinitions/c937dcb4-4398-4b39-8d63-4a6be432252e`。

> [!IMPORTANT]
> 所有來賓設定方案都是由 **audit** 和 **deployIfNotExists** 原則定義所組成的。 只指派其中一個原則定義，會導致來賓設定無法正常運作。

您可以使用下列各項來指派此範例：

- [Azure 入口網站](#azure-portal)
- [Azure PowerShell](#azure-powershell)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="components-of-the-initiative"></a>方案的元件

此[來賓設定](../concepts/guest-configuration.md)方案是由下列原則所組成的：

- [audit](#audit-definition)：稽核應用程式已安裝於 Linux VM 內
  - 識別碼： `/providers/Microsoft.Authorization/policyDefinitions/fee5cb2b-9d9b-410e-afe3-2902d90d0004`
- [deployIfNotExists](#deployIfNotExists-definition)：部署 VM 擴充功能，以稽核應用程式已安裝於 Linux VM 內
  - 識別碼： `/providers/Microsoft.Authorization/policyDefinitions/4d1c04de-2172-403f-901b-90608c35c721`

### <a name="initiative-definition"></a>計畫定義

您可以藉由將 **audit** 和 **deployIfNotExists** 定義聯結在一起，以及[方案參數](#initiative-parameters)，來建立方案。 這是定義的 JSON。

[!code-json[initiative-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/azurepolicyset.json "Initiative definition (JSON)")]

### <a name="initiative-parameters"></a>計畫參數

|名稱 |類型 ||說明 | |---|---||---| |applicationName |字串 |應用程式名稱。 範例：'python'、'powershell' 或以逗號分隔的清單，例如 'python,powershell'。 使用 \* 進行萬用字元比對，例如 'power\*'。|

透過 PowerShell 或 Azure CLI 建立指派時，可以使用 `-PolicyParameter` (PowerShell) 或 `--params` (Azure CLI) 在字串中或透過檔案將參數值當作 JSON 傳遞。
PowerShell 也支援 `-PolicyParameterObject` ，其要求將名稱/值雜湊表傳遞給 Cmdlet，其中 [名稱] 是參數名稱，而 [值] 是在指派過程中傳遞的單一值或值陣列。

在此範例參數中，會稽核應用程式 _python_ 和_powershell_ 的安裝。

```json
{
    "applicationName": {
        "value": "python,powershell"
    }
}
```

只有 **deployIfNotExists** 原則定義會使用方案參數。

### <a name="audit-definition"></a>audit 定義

定義 **audit** 原則定義規則的 JSON。

[!code-json[audit-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json "audit policy rules (JSON)")]

### <a name="deployifnotexists-definition"></a>deployIfNotExists 定義

定義 **deployIfNotExists** 原則定義規則的 JSON。

[!code-json[deployIfNotExists-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json "deployIfNotExists policy rules (JSON)")]

**deployIfNotExists** 原則定義會定義已在下列項目上驗證原則的 Azure 映像：

|發行者 |供應項目 |SKU |
|-|-|-|
|OpenLogic |CentOS\* |6 以外的所有項目\* |
|RedHat |RHEL |6 以外的所有項目\* |
|RedHat |osa | 全部 |
|credativ |Debian | 7 以外的所有項目\* |
|Suse |SLES\* |11 以外的所有項目\* |
|Canonical| UbuntuServer |12 以外的所有項目\* |
|microsoft-dsvm |linux-data-science-vm-ubuntu |全部 |
|microsoft-dsvm |azureml |全部 |
|cloudera |cloudera-centos-os |6 以外的所有項目\* |
|cloudera |cloudera-altus-centos-os |全部 |
|microsoft-ads |linux\* |全部 |
|microsoft-aks |全部 |全部 |
|AzureDatabricks |全部 |全部 |
|qubole-inc |全部 |全部 |
|datastax |全部 |全部 |
|couchbase |全部 |全部 |
|scalegrid |全部 |全部 |
|checkpoint |全部 |全部 |
|paloaltonetworks |全部 |全部 |

規則的**部署**部分會將 _installedApplication_ 參數傳遞至虛擬機器上的來賓設定代理程式。 此設定讓代理程式能夠透過 **audit** 原則定義來執行驗證並回報合規性。

## <a name="azure-portal"></a>Azure 入口網站

在入口網站中建立 **audit** 和 **deployIfNotExists** 定義之後，建議將它們群組至[方案](../concepts/definition-structure.md#initiatives)以進行指派。

### <a name="create-copy-of-audit-definition"></a>建立稽核定義的複本

[![將原則範例部署至 Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)
[![將原則範例部署至 Azure Gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)

透過入口網站使用這些按鈕來部署，即會建立 **audit** 原則定義複本。
如果沒有成對的 **deployIfNotExists** 原則定義，來賓設定將無法正常運作。

### <a name="create-copy-of-deployifnotexists-definition"></a>建立 deployIfNotExists 定義的複本

[![將原則範例部署至 Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)
[![將原則範例部署至 Azure Gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)

透過入口網站使用這些按鈕來部署，即會建立 **deployIfNotExists** 原則定義複本。 如果沒有成對的 **audit** 原則定義，來賓設定將無法正常運作。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>使用 Azure PowerShell 部署

#### <a name="copy-and-assign-the-initiative"></a>複製並指派方案

這些步驟會建立方案複本，其中包含適用於 **audit** 和 **deployIfNotExists** 的內建原則，並將方案指派給資源群組。

```azurepowershell-interactive
# Create the policy initiative (Subscription scope)
$initDef = New-AzPolicySetDefinition -Name 'guestconfig-installed-application-linux' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This initiative will both deploy the policy requirements and audit that the specified application is installed inside Linux virtual machines.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.definitions.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the initiative parameter (JSON format)
$initParam = '{ "applicationName": { "value": "python,powershell" } }'

# Create the initiative assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicySetDefinition $initDef -PolicyParameter $initParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

執行下列命令以移除先前的指派和定義：

```azurepowershell-interactive
# Remove the initiative assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the initiative definition
Remove-AzPolicySetDefinition -Id $initDef
```

#### <a name="copy-and-assign-the-audit-definition"></a>複製和指派 audit 定義

這些步驟會建立 **audit** 定義複本，並將它指派給資源群組。 如果未同時指派成對的 **deployIfNotExists** 定義，此定義將無法正常運作。

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-audit' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This policy audits that the specified application is installed inside Linux virtual machines. This policy should only be used along with its corresponding deploy policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-audit-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicyDefinition $definition
```

執行下列命令以移除先前的指派和定義：

```azurepowershell-interactive
# Remove the policy definition
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

#### <a name="copy-and-assign-the-deployifnotexists-definition"></a>複製和指派 deployIfNotExists 定義

這些步驟會建立 **deployIfNotExists** 定義複本，並將它指派給資源群組。
如果未同時指派成對的 **audit** 定義，此定義將無法正常運作。

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-deployIfNotExists' -DisplayName 'GuestConfig - Deploy VM extension to audit that an application is installed inside Linux VMs' -description 'Include this rule to deploy the VM extension for Microsoft Guest Configuration, the VM extension for Microsoft Azure Managed Service Identity, and the content required to audit that an application is installed inside Linux virtual machines. This policy should only be used along with its corresponding audit policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the definition parameter (JSON format)
$policyParam  = '{ "applicationName": { "value": "python,powershell" } }'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-deployIfNotExists-assignment' -DisplayName 'GuestConfig - Deploy VM extension to audit that Python and PowerShell are installed inside Linux VMs' -Scope $scope.ResourceID -PolicyDefinition $definition -PolicyParameter $policyParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

執行下列命令以移除先前的指派和定義：

```azurepowershell-interactive
# Remove the policy assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

### <a name="azure-powershell-explanation"></a>Azure PowerShell 說明

部署和移除指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件：

| 命令 | 注意 |
|---|---|
| [New-AzPolicySetDefinition](/powershell/module/az.resources/New-AzPolicySetDefinition) | 建立 Azure 原則方案。 |
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-AzPolicyDefinition) | 建立 Azure 原則定義。 |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup) | 取得單一資源群組。 |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-AzPolicyAssignment) | 針對方案或定義建立新的 Azure 原則指派。 |
| [New-AzRoleAssignment](/powershell/module/az.resources/New-AzRoleAssignment) | 將現有的角色指派提供給特定的主體。 |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-AzPolicyAssignment) | 移除現有的 Azure 原則指派。 |
| [Remove-AzPolicySetDefinition](/powershell/module/az.resources/Remove-AzPolicySetDefinition) | 移除方案。 |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-AzPolicyDefinition) | 移除定義。 |

## <a name="next-steps"></a>後續步驟

- 檢閱其他 [Azure 原則範例](index.md)。
- 深入了解 [Azure 原則來賓設定](../concepts/guest-configuration.md)。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
