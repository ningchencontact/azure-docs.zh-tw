---
title: 使用 Azure RBAC 和 Azure CLI 列出角色指派
description: 瞭解如何使用 Azure 角色型存取控制（RBAC）和 Azure CLI，判斷使用者、群組、服務主體或受控識別可以存取哪些資源。
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1a58d2170ec1107222f0e37e432063af23743e42
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710434"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>使用 Azure RBAC 和 Azure CLI 列出角色指派

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] 本文說明如何使用 Azure CLI 列出角色指派。

## <a name="prerequisites"></a>必要條件

- Azure Cloud Shell 或[Azure CLI](/cli/azure) [中的 Bash](/azure/cloud-shell/overview)

## <a name="list-role-assignments-for-a-user"></a>列出使用者的角色指派

若要列出特定使用者的角色指派，請使用 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)：

```azurecli
az role assignment list --assignee <assignee>
```

根據預設，只會顯示範圍為訂用帳戶的直接指派。 若要查看資源或群組範圍內的指派，請使用 `--all` 並使用 `--include-inherited`來查看繼承的指派。

下列範例會列出直接指派給*patlong\@contoso.com*使用者的角色指派：

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

## <a name="list-role-assignments-for-a-resource-group"></a>列出資源群組的角色指派

若要列出存在於資源群組範圍的角色指派，請使用[az role 指派 list](/cli/azure/role/assignment#az-role-assignment-list)：

```azurecli
az role assignment list --resource-group <resource_group>
```

下列範例會列出*醫藥-sales*資源群組的角色指派：

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
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

...
```

## <a name="list-role-assignments-for-a-subscription"></a>列出訂用帳戶的角色指派

若要列出訂用帳戶範圍中的所有角色指派，請使用[az role 指派 list](/cli/azure/role/assignment#az-role-assignment-list)。 若要取得訂用帳戶識別碼，您可以在 Azure 入口網站的 [**訂閱**] 分頁上找到它，或者您可以使用[az account list](/cli/azure/account#az-account-list)。

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>列出管理群組的角色指派

若要列出管理群組範圍的所有角色指派，請使用[az role 指派 list](/cli/azure/role/assignment#az-role-assignment-list)。 若要取得管理群組識別碼，您可以在 **管理群組**] 分頁的 [Azure 入口網站中找到，也可以使用[az 帳戶管理-群組清單](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list)。

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="next-steps"></a>後續步驟

- [使用 Azure RBAC 和 Azure CLI 新增或移除角色指派](role-assignments-cli.md)
