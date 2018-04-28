---
title: 使用 Azure CLI 管理角色型存取控制 (RBAC) | Microsoft Docs
description: 了解如何使用 Azure 命令列介面，藉由列出角色和角色動作，以及藉由將角色指派給訂用帳戶和應用程式範圍，來管理「角色型存取控制」(RBAC)。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/03/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: f783b08b25b7dd00351537f4dd404d9c8d02044d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>使用 Azure 命令列介面管理角色型存取控制

> [!div class="op_single_selector"]
> * [PowerShell](role-assignments-powershell.md)
> * [Azure CLI](role-assignments-cli.md)
> * [REST API](role-assignments-rest.md)


使用角色型存取控制 (RBAC)，您會藉由在特定範圍指派角色，來定義使用者、群組和服務主體的存取權。 本文說明如何使用 Azure 命令列介面 (CLI) 管理角色指派。

## <a name="prerequisites"></a>先決條件

您必須具備以下先決條件，才能使用 Azure CLI 來管理角色指派：

* [Azure CLI 2.0](/cli/azure). 您可以透過 [Azure Cloud Shell](../cloud-shell/overview.md) 在瀏覽器中使用，或者在 macOS、Linux 和 Windows 中[安裝](/cli/azure/install-azure-cli)並從命令列中執行。

## <a name="list-role-definitions"></a>列出角色定義

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

### <a name="list-actions-of-a-role-definition"></a>列出角色定義的動作

若要列出角色定義的動作，請使用 [az role definition list](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

下列範例會列出「參與者」的角色定義：

```azurecli
az role definition list --name "Contributor"
```

```Output
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
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

下列範例會列出「參與者」的 actions 和 notActions：

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

下列範例會列出「虛擬機器參與者」角色的動作：

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

## <a name="list-role-assignments"></a>列出角色指派

### <a name="list-role-assignments-for-a-user"></a>列出使用者的角色指派

若要列出特定使用者的角色指派，請使用 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)：

```azurecli
az role assignment list --assignee <assignee>
```

預設只會顯示訂用帳戶範圍內的指派。 若要檢視資源或群組範圍內的指派，請使用 `--all`。

下列範例會列出直接指派給 patlong@contoso.com 使用者的角色指派：

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>列出資源群組的角色指派

若要列出因資源群組而存在的角色指派，請使用 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)︰

```azurecli
az role assignment list --resource-group <resource_group>
```

下列範例會列出 pharma-sales-projectforecast 資源群組的角色指派：

```azurecli
az role assignment list --resource-group pharma-sales-projectforecast --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}

...
```

## <a name="create-role-assignments"></a>建立角色指派

### <a name="create-a-role-assignment-for-a-user"></a>建立使用者的角色指派

若要為資源群組範圍中的使用者建立角色指派，請使用 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create)：

```azurecli
az role assignment create --role <role> --assignee <assignee> --resource-group <resource_group>
```

下列範例會將「虛擬機器參與者」角色指派給 pharma-sales-projectforecast 資源群組範圍中的 patlong@contoso.com 使用者：

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales-projectforecast
```

### <a name="create-a-role-assignment-for-a-group"></a>建立群組的角色指派

若要為群組建立角色指派，請使用 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create)：

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

下列範例會將「讀者」角色指派給訂用帳戶範圍中識別碼為 22222222-2222-2222-2222-222222222222 的「Ann Mack 小組」群組。 若要取得群組的識別碼，您可以使用 [az ad group list](/cli/azure/ad/group#az-ad-group-list) 或 [az ad group show](/cli/azure/ad/group#az-ad-group-show)。

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

下列範例會將「虛擬機器參與者」角色指派給識別碼為 22222222-2222-2222-2222-222222222222 且在虛擬網路 (名為 pharma-sales-project-network) 資源範圍中的「Ann Mack 小組」群組：

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111/resourcegroups/pharma-sales-projectforecast/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>建立應用程式的角色指派

若要為應用程式建立角色，請使用 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create)：

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

下列範例會將「虛擬機器參與者」角色指派給 pharma-sales-projectforecast 資源群組範圍中物件識別碼為 44444444-4444-4444-4444-444444444444 的應用程式。 若要取得應用程式的物件識別碼，您可以使用 [az ad app list](/cli/azure/ad/app#az-ad-app-list) 或 [az ad app show](/cli/azure/ad/app#az-ad-app-show)。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales-projectforecast
```

## <a name="remove-a-role-assignment"></a>移除角色指派

若要移除角色指派，請使用 [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete)：

```azurecli
az role assignment delete --assignee <assignee> --role <role> --resource-group <resource_group>
```

下列範例會從 pharma-sales-projectforecast 資源群組上的 patlong@contoso.com 使用者移除「虛擬機器參與者」角色指派：

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales-projectforecast
```

下列範例會將「讀者」角色從訂用帳戶範圍中識別碼為 22222222-2222-2222-2222-222222222222 的「Ann Mack 小組」群組中移除。 若要取得群組的識別碼，您可以使用 [az ad group list](/cli/azure/ad/group#az-ad-group-list) 或 [az ad group show](/cli/azure/ad/group#az-ad-group-show)。

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="custom-roles"></a>自訂角色

### <a name="list-custom-roles"></a>列出自訂角色

若要列出範圍中可供指派的角色，請使用 [az role definition list](/cli/azure/role/definition#az-role-definition-list)。

下列兩個範例都會列出目前訂用帳戶中的所有自訂角色：

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Operator Role",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}

...
```

### <a name="create-a-custom-role"></a>建立自訂角色

若要建立自訂角色，請使用 [az role definition create](/cli/azure/role/definition#az-role-definition-create)。 角色定義可以是 JSON 描述或包含 JSON 描述的檔案路徑。

```azurecli
az role definition create --role-definition <role_definition>
```

下列範例會建立一個名為 Virtual Machine Operator 的自訂角色。 這個自訂角色會指派 Microsoft.Compute、Microsoft.Storage 和 Microsoft.Network 資源提供者之所有讀取作業的存取權，以及指派啟動、重新啟動和監視虛擬機器的存取權。 這個自訂角色可用於兩個訂用帳戶中。 這個範例使用 JSON 檔案做為輸入。

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

### <a name="update-a-custom-role"></a>更新自訂角色

若要更新自訂角色，請先使用 [az role definition list](/cli/azure/role/definition#az-role-definition-list) 來擷取角色定義。 接著，對角色定義進行想要的變更。 最後，使用 [az role definition update](/cli/azure/role/definition#az-role-definition-update)儲存更新的角色定義。

```azurecli
az role definition update --role-definition <role_definition>
```

下列範例會將 Microsoft.Insights/diagnosticSettings/ 作業新增到 Virtual Machine Operator 自訂角色的 Actions 中。

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

### <a name="delete-a-custom-role"></a>刪除自訂角色

若要刪除自訂角色，請使用 [az role definition delete](/cli/azure/role/definition#az-role-definition-delete)。 若要指定要刪除的角色，請使用角色名稱或角色識別碼。 若要決定角色識別碼，請使用 [az role definition list](/cli/azure/role/definition#az-role-definition-list)。

```azurecli
az role definition delete --name <role_name or role_id>
```

下列範例會刪除 Virtual Machine Operator 自訂角色：

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>後續步驟

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

