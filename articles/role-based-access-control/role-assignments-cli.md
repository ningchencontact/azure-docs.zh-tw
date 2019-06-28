---
title: 使用 RBAC 和 Azure CLI 管理對 Azure 資源的存取 |Microsoft Docs
description: 了解如何使用角色型存取控制 (RBAC) 和 Azure CLI 來管理使用者、群組和應用程式對 Azure 資源的存取。 這包括如何列出存取權、授與存取權以及移除存取權。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: bc5deb614e2ac6e47ff3bf241943df92d97699b2
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295165"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>使用 RBAC 與 Azure CLI 管理對 Azure 資源的存取

[角色型存取控制 (RBAC)](overview.md) 是您管理對 Azure 資源存取的機制。 本文將描述如何使用 RBAC 和 Azure CLI 來管理使用者、群組和應用程式的存取權。

## <a name="prerequisites"></a>必要條件

若要管理存取權，您需要下列其中一項：

* [Azure Cloud Shell 中的 Bash](/azure/cloud-shell/overview)
* [Azure CLI](/cli/azure)

## <a name="list-roles"></a>列出角色

若要列出所有可用的角色定義，請使用 [az role definition list](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

下列範例會列出所有可用角色定義的名稱和描述：

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

下列範例會列出所有內建角色定義：

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

## <a name="list-a-role-definition"></a>列出角色定義

若要列出角色定義，請使用[az 角色定義清單](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

下列範例會列出「參與者」  的角色定義：

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-actions-of-a-role"></a>列出角色的動作

下列範例會列出剛才*動作*並*notActions*的*參與者*角色：

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

下列範例會列出的動作*虛擬機器參與者*角色：

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-access"></a>列出存取權

在 RBAC 中，若要列出存取權，您可以列出角色指派。

### <a name="list-role-assignments-for-a-user"></a>列出使用者的角色指派

若要列出特定使用者的角色指派，請使用 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)：

```azurecli
az role assignment list --assignee <assignee>
```

根據預設，會顯示只有直接指派到訂用帳戶範圍。 若要檢視範圍內的資源或群組指派，請使用`--all`若要檢視繼承的 asisgnments，請使用`--include-inherited`。

下列範例會列出直接指派給角色指派*patlong\@contoso.com*使用者：

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>列出資源群組的角色指派

若要列出因資源群組而存在的角色指派，請使用 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)︰

```azurecli
az role assignment list --resource-group <resource_group>
```

下列範例會列出的角色指派 *「 銷售*資源群組：

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="grant-access"></a>授與存取權

在 RBAC 中，若要授與存取權，您可以建立角色指派。

### <a name="create-a-role-assignment-for-a-user"></a>建立使用者的角色指派

若要為資源群組範圍中的使用者建立角色指派，請使用 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create)：

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

下列範例會將指派*虛擬機器參與者*角色，才能*patlong\@contoso.com*使用者 *「 銷售*資源群組範圍：

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>建立使用的唯一角色識別碼的角色指派

有好幾次，當角色名稱可能會變更，例如：

- 您使用您自己自訂的角色，而且您決定要變更名稱。
- 您使用的預覽角色 **（預覽）** 名稱中。 當發行角色時，會重新命名角色。

> [!IMPORTANT]
> 預覽版本不提供服務等級協定，且不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

即使某個角色重新命名，則不會變更的角色識別碼。 如果您使用指令碼或自動化來建立角色指派，最好使用的唯一角色識別碼，而不是角色名稱。 因此，如果重新命名角色，您的指令碼會更容易就能運作。

若要建立角色指派，而不角色名稱使用的唯一角色識別碼，請使用[az 角色指派建立](/cli/azure/role/assignment#az-role-assignment-create)。

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

下列範例會將指派[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)角色，才能*patlong\@contoso.com*使用者 *「 銷售*資源群組範圍。 若要取得的唯一角色識別碼，您可以使用[az 角色定義清單](/cli/azure/role/definition#az-role-definition-list)，或參閱[適用於 Azure 資源的內建角色](built-in-roles.md)。

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>建立群組的角色指派

若要為群組建立角色指派，請使用 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create)：

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

下列範例會將「讀者」  角色指派給訂用帳戶範圍中識別碼為 22222222-2222-2222-2222-222222222222 的「Ann Mack 小組」群組  。 若要取得群組的識別碼，您可以使用 [az ad group list](/cli/azure/ad/group#az-ad-group-list) 或 [az ad group show](/cli/azure/ad/group#az-ad-group-show)。

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

下列範例會將「虛擬機器參與者」  角色指派給識別碼為 22222222-2222-2222-2222-222222222222 且在虛擬網路 (名為 pharma-sales-project-network  ) 資源範圍中的「Ann Mack 小組」  群組：

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>建立應用程式的角色指派

若要為應用程式建立角色，請使用 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create)：

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

下列範例會將指派*虛擬機器參與者*角色，才能在物件識別碼 44444444-4444-4444-4444-444444444444 的應用程式 *「 銷售*資源群組範圍。 若要取得應用程式的物件識別碼，您可以使用 [az ad app list](/cli/azure/ad/app#az-ad-app-list) 或 [az ad app show](/cli/azure/ad/app#az-ad-app-show)。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

## <a name="remove-access"></a>移除存取

在 RBAC 中，若要移除存取權，您可以使用 [az 角色指派刪除](/cli/azure/role/assignment#az-role-assignment-delete)來移除角色指派:

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

下列範例會移除*虛擬機器參與者*從角色分派*patlong\@contoso.com*上的使用者 *「 銷售*資源群組：

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

下列範例會將「讀者」  角色從訂用帳戶範圍中識別碼為 22222222-2222-2222-2222-222222222222 的「Ann Mack 小組」  群組中移除。 若要取得群組的識別碼，您可以使用 [az ad group list](/cli/azure/ad/group#az-ad-group-list) 或 [az ad group show](/cli/azure/ad/group#az-ad-group-show)。

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="next-steps"></a>後續步驟

- [教學課程：使用 Azure CLI 為 Azure 資源建立自訂角色](tutorial-custom-role-cli.md)
- [使用 Azure CLI 管理 Azure 資源和資源群組](../azure-resource-manager/cli-azure-resource-manager.md)
