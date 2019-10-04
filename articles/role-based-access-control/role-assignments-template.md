---
title: 使用 RBAC 和 Azure Resource Manager 範本管理對 Azure 資源的存取 | Microsoft Docs
description: 了解如何使用角色型存取控制 (RBAC) 和 Azure Resource Manager 範本來管理使用者、群組和應用程式對 Azure 資源的存取。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b4eebf7dac4d388411f570b1546c96e3b82b2a98
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950068"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>使用 RBAC 和 Azure Resource Manager 範本管理對 Azure 資源的存取

[角色型存取控制 (RBAC)](overview.md) 是您管理對 Azure 資源存取的機制。 除了使用 Azure PowerShell 或 Azure CLI 之外，您還可以使用[Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)來管理 Azure 資源的存取權。 如果您需要以一致性和重複的方式來部署資源，範本可以派上用場。 本文描述如何使用 RBAC 和範本來管理存取權。

## <a name="create-a-role-assignment-at-a-resource-group-scope-without-parameters"></a>在資源群組範圍建立角色指派（不含參數）

在 RBAC 中，若要授與存取權，您可以建立角色指派。 下列範本顯示建立角色指派的基本方式。 某些值會在範本中指定。 下列範本會示範：

-  如何將[讀取](built-in-roles.md#reader)者角色指派給資源群組範圍中的使用者、群組或應用程式

若要使用範本，您必須執行下列動作：

- 建立新的 JSON 檔案並複製範本
- 以要指派角色的使用者、群組或應用程式的唯一識別碼取代 `<your-principal-id>`。 此識別碼的格式：`11111111-1111-1111-1111-111111111111`

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

以下是如何在名為 ExampleGroup 的資源群組中啟動部署的範例[new-azresourcegroupdeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)和[az group deployment create](/cli/azure/group/deployment#az-group-deployment-create)命令。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

以下顯示部署範本之後，對資源群組的使用者進行讀取者角色指派的範例。

![資源群組範圍的角色指派](./media/role-assignments-template/role-assignment-template.png)

## <a name="create-a-role-assignment-at-a-resource-group-or-subscription-scope"></a>在資源群組或訂用帳戶範圍建立角色指派

先前的範本並不具彈性。 下列範本會使用參數，而且可以在不同的範圍中使用。 下列範本會示範：

- 如何在資源群組或訂用帳戶範圍將角色指派給使用者、群組或應用程式
- 如何將擁有者、參與者和讀者角色指定為參數

若要使用範本，您必須指定下列輸入：

- 要為其指派角色之使用者、群組或應用程式讀唯一識別碼
- 要指派的角色
- 將用於角色指派的唯一識別碼，或者您可以使用預設識別碼

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

若要取得使用者的唯一識別碼來指派角色給，您可以使用[AzADUser](/powershell/module/az.resources/get-azaduser)或[az ad user show](/cli/azure/ad/user#az-ad-user-show)命令。

```azurepowershell
$userid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
```

角色指派的範圍是從部署層級決定。 以下是如何在資源群組範圍內啟動部署的[new-azresourcegroupdeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)和[az group deployment](/cli/azure/group/deployment#az-group-deployment-create)命令範例。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

以下是如何在訂用帳戶範圍啟動部署並指定位置的[new-azdeployment](/powershell/module/az.resources/new-azdeployment)和[az 部署 create](/cli/azure/deployment#az-deployment-create)命令範例。

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

> [!NOTE]
> 除非為範本的每個部署提供相同的 `roleNameGuid` 值做為參數，否則這個範本不具等冪。 如果未提供 `roleNameGuid`，則根據預設會在每個部署上產生新的 GUID，而後續部署將會失敗，併發生 `Conflict: RoleAssignmentExists` 錯誤。

## <a name="create-a-role-assignment-at-a-resource-scope"></a>在資源範圍中建立角色指派

如果您需要在資源層級建立角色指派，角色指派的格式會不同。 您會提供資源提供者的命名空間和資源類型，以指派角色給該資源。 您也可以將資源的名稱包含在角色指派的名稱中。

針對角色指派的類型和名稱，請使用下列格式：

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

下列範本會示範：

- 如何建立新的儲存體帳戶
- 如何將角色指派給儲存體帳戶範圍的使用者、群組或應用程式
- 如何將擁有者、參與者和讀者角色指定為參數

若要使用範本，您必須指定下列輸入：

- 要為其指派角色之使用者、群組或應用程式讀唯一識別碼
- 要指派的角色

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', guid(uniqueString(parameters('storageName'))))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

若要部署先前的範本，您可以使用資源群組命令。 以下是如何在資源範圍啟動部署的範例[new-azresourcegroupdeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)和[az group deployment create](/cli/azure/group/deployment#az-group-deployment-create)命令。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Contributor
```

以下顯示部署範本之後，將儲存體帳戶的參與者角色指派給使用者的範例。

![資源範圍的角色指派](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="create-a-role-assignment-for-a-new-service-principal"></a>為新的服務主體建立角色指派

如果您建立新的服務主體，並立即嘗試將角色指派給該服務主體，在某些情況下，該角色指派可能會失敗。 例如，如果您建立新的受控識別，然後嘗試在相同的 Azure Resource Manager 範本中將角色指派給該服務主體，則角色指派可能會失敗。 此失敗的原因可能是複寫延遲。 服務主體會建立在一個區域中;不過，角色指派可能會發生在另一個尚未複寫服務主體的區域中。 若要解決這種情況，您應該在建立角色指派時，將 `principalType` 屬性設定為 `ServicePrincipal`。

下列範本會示範：

- 如何建立新的受控識別服務主體
- 如何指定 `principalType`
- 如何將參與者角色指派給資源群組範圍中的服務主體

若要使用範本，您必須指定下列輸入：

- 受控識別的基底名稱，或者您可以使用預設字串

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "scope": "[resourceGroup().id]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

以下是如何在資源群組範圍內啟動部署的[new-azresourcegroupdeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)和[az group deployment](/cli/azure/group/deployment#az-group-deployment-create)命令範例。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

以下顯示部署範本之後，將參與者角色指派給新的受控識別服務主體的範例。

![新受控識別服務主體的角色指派](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>後續步驟

- [快速入門：使用 Azure 入口網站建立及部署 Azure Resource Manager 範本](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [了解 Azure Resource Manager 範本的的結構和語法](../azure-resource-manager/resource-group-authoring-templates.md)
- [在訂用帳戶層級建立資源群組和資源](../azure-resource-manager/deploy-to-subscription.md)
- [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?term=rbac)
