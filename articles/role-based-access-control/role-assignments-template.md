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
ms.date: 07/19/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e6511ff84c251577a5ff483f892387ab7d3d4d41
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360489"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>使用 RBAC 和 Azure Resource Manager 範本管理對 Azure 資源的存取

[角色型存取控制 (RBAC)](overview.md) 是您管理對 Azure 資源存取的機制。 除了使用 Azure PowerShell 或 Azure CLI 外，您可以使用 RBAC 和 [Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)來管理 Azure 資源的存取權。 如果您需要以一致性和重複的方式來部署資源，範本可以派上用場。 本文描述如何使用 RBAC 和範本來管理存取權。

## <a name="assign-role-to-resource-group-or-subscription"></a>將角色指派給資源群組或訂用帳戶

在 RBAC 中，若要授與存取權，您可以建立角色指派。 下列範本會示範：
- 如何在資源群組或訂用帳戶範圍將角色指派給使用者、群組或應用程式
- 如何將擁有者、參與者和讀者角色指定為參數

若要使用範本，您必須指定下列輸入：
- 要為其指派角色之使用者、群組或應用程式讀唯一識別碼
- 要指派的角色
- 將用於角色指派的唯一識別碼, 或者您可以使用預設識別碼

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

以下顯示在部署範本之後, 對資源群組的使用者進行讀者角色指派的範例。

![使用範本的角色指派](./media/role-assignments-template/role-assignment-template.png)

角色指派的範圍是從部署層級決定。 這篇文章中會顯示資源群組和訂用帳戶層級的部署命令。

## <a name="assign-role-to-resource"></a>將角色指派給資源

如果您需要在資源層級建立角色指派, 角色指派的格式會不同。 您會提供資源提供者的命名空間和資源類型, 以指派角色給該資源。 您也可以將資源的名稱包含在角色指派的名稱中。

針對角色指派的類型和名稱, 請使用下列格式:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

下列範本會部署儲存體帳戶, 並將角色指派給它。 您可以使用資源群組命令來部署它。

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
      "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleNameGuid'))]",
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

以下顯示部署範本之後, 對儲存體帳戶的使用者進行參與者角色指派的範例。

![使用範本的角色指派](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="deploy-template-using-azure-powershell"></a>使用 Azure PowerShell 來部署範本

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

若要使用 Azure PowerShell 將先前的範本部署至資源群組或訂用帳戶, 請遵循下列步驟。

1. 建立名為 rbac-rg.json 的新檔案並複製先前的範本。

1. 登入 [Azure PowerShell](/powershell/azure/authenticate-azureps)。

1. 取得使用者、群組或應用程式的唯一識別碼。 例如，您可以使用 [Get-AzADUser](/powershell/module/az.resources/get-azaduser) 命令來列出 Azure AD 使用者。

    ```azurepowershell
    $userid = (Get-AzADUser -DisplayName "{name}").id
    ```

1. 此範本會為用來識別角色指派的 GUID 產生預設值。 如果您需要指定特定的 GUID, 請針對 roleNameGuid 參數傳遞中的該值。 此識別碼的格式：`11111111-1111-1111-1111-111111111111`

若要在資源或資源群組層級指派角色, 請遵循下列步驟:

1. 建立範例 Azure 資源群組。

    ```azurepowershell
    New-AzResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. 使用 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 命令來開始部署。

    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    ```

    以下顯示輸出的範例。

    ```Output
    PS /home/user> New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    
    DeploymentName          : rbac-rg
    ResourceGroupName       : ExampleGroup
    ProvisioningState       : Succeeded
    Timestamp               : 7/17/2018 7:46:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              principalId      String                     22222222-2222-2222-2222-222222222222
                              builtInRoleType  String                     Reader
                              roleNameGuid     String                     11111111-1111-1111-1111-111111111111
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

若要在訂用帳戶層級指派角色, 請使用[new-azdeployment](/powershell/module/az.resources/new-azdeployment)命令, 並指定部署的位置。

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
```

它與資源群組的部署命令具有類似的輸出。

## <a name="deploy-template-using-the-azure-cli"></a>使用 Azure CLI 部署範本

若要使用 Azure CLI 將先前的範本部署至資源群組或訂用帳戶, 請遵循下列步驟。

1. 建立名為 rbac-rg.json 的新檔案並複製先前的範本。

1. 登入 [Azure CLI](/cli/azure/authenticate-azure-cli)。

1. 取得使用者、群組或應用程式的唯一識別碼。 例如, 您可以使用[az ad user show](/cli/azure/ad/user#az-ad-user-show)命令來顯示 Azure AD 使用者。

    ```azurecli
    userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
    ```

1. 此範本會為用來識別角色指派的 GUID 產生預設值。 如果您需要指定特定的 GUID, 請針對 roleNameGuid 參數傳遞中的該值。 此識別碼的格式：`11111111-1111-1111-1111-111111111111`

若要在資源或資源群組層級指派角色, 請遵循下列步驟:

1. 建立範例 Azure 資源群組。

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. 使用 [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) 命令來開始部署。

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    ```

    以下顯示輸出的範例。

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/rbac-rg",
      "name": "rbac-rg",
      "properties": {
        "additionalProperties": {
          "duration": "PT9.5323924S",
          "outputResources": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
              "resourceGroup": "ExampleGroup"
            }
          ],
          "templateHash": "0000000000000000000"
        },
        "correlationId": "33333333-3333-3333-3333-333333333333",
        "debugSetting": null,
        "dependencies": [],
        "mode": "Incremental",
        "outputs": null,
        "parameters": {
          "builtInRoleType": {
            "type": "String",
            "value": "Reader"
          },
          "principalId": {
            "type": "String",
            "value": "22222222-2222-2222-2222-222222222222"
          },
          "roleNameGuid": {
            "type": "String",
            "value": "11111111-1111-1111-1111-111111111111"
          }
        },
        "parametersLink": null,
        "providers": [
          {
            "id": null,
            "namespace": "Microsoft.Authorization",
            "registrationState": null,
            "resourceTypes": [
              {
                "aliases": null,
                "apiVersions": null,
                "locations": [
                  null
                ],
                "properties": null,
                "resourceType": "roleAssignments"
              }
            ]
          }
        ],
        "provisioningState": "Succeeded",
        "template": null,
        "templateLink": null,
        "timestamp": "2018-07-17T19:00:31.830904+00:00"
      },
      "resourceGroup": "ExampleGroup"
    }
    ```

若要在訂用帳戶層級指派角色, 請使用[az deployment create](/cli/azure/deployment#az-deployment-create)命令, 並指定部署的位置。

```azurecli
az deployment create --location centralus --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
```

它與資源群組的部署命令具有類似的輸出。

## <a name="next-steps"></a>後續步驟

- [快速入門：使用 Azure 入口網站建立及部署 Azure Resource Manager 範本](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [了解 Azure Resource Manager 範本的的結構和語法](../azure-resource-manager/resource-group-authoring-templates.md)
- [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?term=rbac)
