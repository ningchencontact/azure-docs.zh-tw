---
title: 範例 - 套用標籤及其預設值
description: 此原則定義範例會附加指定的標籤名稱與值 (如果未提供該標籤)。
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: a9ad211ac1ef9889fcff9646f30fe306458c3538
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266642"
---
# <a name="sample---apply-tag-and-its-default-value"></a>範例 - 套用標籤及其預設值

此原則會附加指定的標籤名稱和值 (如果未提供該標籤)。 您需指定要套用的標籤名稱和值。

您可以利用下列各項部署此範例原則：

- [Azure 入口網站](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure CLI](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>範例原則

### <a name="policy-definition"></a>原則定義

撰寫好的完整 JSON 原則定義，可供 REST API、[部署至 Azure] 按鈕使用，也可以在入口網站中手動建立。

[!code-json[main](../../../../policy-templates/samples/built-in-policy/apply-default-tag-value/azurepolicy.json "Apply tag and its default value")]

> [!NOTE]
> 如果要在入口網站中手動建立原則，請使用上述的 **properties.parameters** 和 **properties.policyRule** 部分。 以大括號 `{}` 將兩個區段包住，使其成為有效的 JSON。

### <a name="policy-rules"></a>原則規則

定義原則規則的 JSON (由 Azure CLI 和 Azure PowerShell 使用)。

[!code-json[rule](../../../../policy-templates/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>原則參數

定義原則參數的 JSON (由 Azure CLI 和 Azure PowerShell 使用)。

[!code-json[parameters](../../../../policy-templates/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json "Policy parameters (JSON)")]

|Name |類型 |欄位 |說明 |
|---|---|---|---|
|tagName |字串 |tags |標記的名稱，例如 costCenter|
|tagValue |字串 |tags |標記的值，例如 headquarter|

透過 PowerShell 或 Azure CLI 建立指派時，可以使用 `-PolicyParameter` (PowerShell) 或 `--params` (Azure CLI) 在字串中或透過檔案將參數值當作 JSON 傳遞。
PowerShell 也支援 `-PolicyParameterObject` ，其要求將名稱/值雜湊表傳遞給 Cmdlet，其中 [名稱] 是參數名稱，而 [值] 是在指派過程中傳遞的單一值或值陣列。

在此範例參數中，會將 _tagName_ 定義為 **costCenter**，並將 _tagValue_ 定義為 **headquarter**。

```json
{
    "tagName": {
        "value": "costCenter"
    },
    "tagValue": {
        "value": "headquarter"
    }
}
```

## <a name="azure-portal"></a>Azure 入口網站

[![將原則範例部署至 Azure](../media/deploy/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fapply-default-tag-value%2Fazurepolicy.json)
[![將原則範例部署至 Azure Gov](../media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fapply-default-tag-value%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>使用 Azure PowerShell 部署

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'allowed-custom-images' -DisplayName 'Approved VM images' -description 'This policy governs the approved VM images' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'apply-default-tag-value' -DisplayName 'Apply tag and its default value Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
```

### <a name="remove-with-azure-powershell"></a>使用 Azure PowerShell 移除

執行下列命令以移除先前的指派和定義：

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Azure PowerShell 說明

部署和移除指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件：

| 命令 | 注意 |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | 建立新的 Azure 原則定義。 |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | 取得單一資源群組。 |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | 建立新的 Azure 原則指派。 在此範例中，我們會未它提供定義，但它也可以採取主動。 |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | 移除現有的 Azure 原則指派。 |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | 移除現有的 Azure 原則定義。 |

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>使用 Azure CLI 進行部署

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'apply-default-tag-value' --display-name 'Apply tag and its default value' --description 'Applies a required tag and its default value if it is not specified by the user' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a resource, subscription, or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "tagName": { "value": "costCenter" }, "tagValue": { "value": "headquarter" } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'apply-default-tag-value' --display-name 'Apply tag and its default value Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
```

### <a name="remove-with-azure-cli"></a>使用 Azure CLI 移除

執行下列命令以移除先前的指派和定義：

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Azure CLI 說明

| 命令 | 注意 |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | 建立新的 Azure 原則定義。 |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | 取得單一資源群組。 |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | 建立新的 Azure 原則指派。 在此範例中，我們會未它提供定義，但它也可以採取主動。 |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | 移除現有的 Azure 原則指派。 |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | 移除現有的 Azure 原則定義。 |

有數個工具可用來與 Resource Manager REST API 互動，例如 [ARMClient](https://github.com/projectkudu/ARMClient) 或 PowerShell。 在[原則定義結構](../concepts/definition-structure.md#aliases)的 [別名] 區段中，可以找到從 PowerShell 呼叫 REST API 的範例。

## <a name="rest-api"></a>REST API

### <a name="deploy-with-rest-api"></a>使用 REST API 進行部署

- 建立原則定義 (訂用帳戶範圍)。 使用要求本文的[原則定義](#policy-definition) JSON。

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/apply-default-tag-value?api-version=2016-12-01
  ```

- 建立原則指派 (資源群組範圍)

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/apply-default-tag-value-assignment?api-version=2017-06-01-preview
  ```

  使用要求本文的下列 JSON 範例：

  ```json
  {
      "properties": {
          "displayName": "Apply tag and its default value Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/apply-default-tag-value",
          "parameters": {
              "tagName": {
                  "value": "costCenter"
              },
              "tagValue": {
                  "value": "headquarter"
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>使用 REST API 移除

- 移除原則指派

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/apply-default-tag-value-assignment?api-version=2017-06-01-preview
  ```

- 移除原則定義

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/apply-default-tag-value?api-version=2016-12-01
  ```

### <a name="rest-api-explanation"></a>REST API 說明

| 服務 | 群組 | 作業 | 注意 |
|---|---|---|---|
| 資源管理 | 原則定義 | [建立](/rest/api/resources/policydefinitions/createorupdate) | 在訂用帳戶建立新的 Azure 原則定義。 替代方法：[在管理群組建立](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| 資源管理 | 原則指派 | [建立](/rest/api/resources/policyassignments/create) | 建立新的 Azure 原則指派。 在此範例中，我們會未它提供定義，但它也可以採取主動。 |
| 資源管理 | 原則指派 | [刪除](/rest/api/resources/policyassignments/delete) | 移除現有的 Azure 原則指派。 |
| 資源管理 | 原則定義 | [刪除](/rest/api/resources/policydefinitions/delete) | 移除現有的 Azure 原則定義。 替代方法：[在管理群組刪除](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>後續步驟

- 檢閱其他 [Azure 原則範例](index.md)
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)