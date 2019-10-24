---
title: 使用 RBAC 與 Azure PowerShell 管理 Azure 資源的存取權 | Microsoft Docs
description: 瞭解如何使用角色型存取控制（RBAC）和 Azure PowerShell，來管理使用者、群組和應用程式對 Azure 資源的存取權。 這包括如何列出存取權、授與存取權以及移除存取權。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5a6ed66efa0f73f957c3acb048136a5328f9c264
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750172"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-powershell"></a>使用 RBAC 與 Azure PowerShell 管理 Azure 資源的存取權

[角色型存取控制 (RBAC)](overview.md) 是您管理對 Azure 資源存取的機制。 本文將描述如何使用 RBAC 和 Azure PowerShell 來管理使用者、群組和應用程式的存取權。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必要條件

若要管理存取權，您需要下列其中一項：

* [Azure Cloud Shell 中的 PowerShell](/azure/cloud-shell/overview)
* [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-roles"></a>列出角色

### <a name="list-all-available-roles"></a>列出所有可用的角色

若要列出可供指派的 RBAC 角色，以及檢查它們授與存取的作業，使用 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)。

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-specific-role"></a>列出特定角色

若要列出特定角色，使用 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)。

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

## <a name="list-a-role-definition"></a>列出角色定義

### <a name="list-a-role-definition-in-json-format"></a>以 JSON 格式列出角色定義

若要以 JSON 格式列出角色定義，請使用[get-azroledefinition](/powershell/module/az.resources/get-azroledefinition)。

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-actions-of-a-role"></a>列出角色的動作

若要列出特定角色的動作，使用 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)。

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="list-access"></a>列出存取權

在 RBAC 中，若要列出存取權，您可以列出角色指派。

### <a name="list-role-assignments-for-a-user"></a>列出使用者的角色指派

若要列出指派給指定使用者的所有角色，使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)。

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

若要列出指派給指定使用者的所有角色，以及指派給該使用者所屬群組的角色，使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)。

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

### <a name="list-role-assignments-at-a-resource-group-scope"></a>列出資源群組範圍的角色指派

若要列出資源群組範圍中的所有角色指派，請使用[new-azroleassignment](/powershell/module/az.resources/get-azroleassignment)。

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

### <a name="list-role-assignments-at-a-subscription-scope"></a>列出訂用帳戶範圍的角色指派

若要列出訂用帳戶範圍中的所有角色指派，請使用[new-azroleassignment](/powershell/module/az.resources/get-azroleassignment)。 若要取得訂用帳戶識別碼，您可以在 Azure 入口網站的 [**訂閱**] 分頁上找到它，或者您可以使用[get-azsubscription](/powershell/module/Az.Accounts/Get-AzSubscription)。

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>列出管理群組範圍的角色指派

若要列出管理群組範圍的所有角色指派，請使用[new-azroleassignment](/powershell/module/az.resources/get-azroleassignment)。 若要取得管理群組識別碼，您可以在 **管理群組**] 分頁的 [Azure 入口網站中找到，也可以使用[AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)。

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>列出傳統服務管理員和共同管理員的角色指派

若要列出傳統訂用帳戶管理員和共同管理員的角色指派，請使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)。

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="grant-access"></a>授與存取權

在 RBAC 中，若要授與存取權，您可以建立角色指派。

### <a name="search-for-object-ids"></a>搜尋物件識別碼

若要指派角色，您必須識別物件 (使用者、群組或應用程式) 和範圍。

若要取得訂用帳戶識別碼，您可以在 Azure 入口網站的 [**訂閱**] 分頁上找到它，或者您可以使用[get-azsubscription](/powershell/module/Az.Accounts/Get-AzSubscription)。

若要取得 Azure AD 使用者的物件識別碼，請使用[AzADUser](/powershell/module/az.resources/get-azaduser)。

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
```

若要取得 Azure AD 群組的物件識別碼，請使用[AzADGroup](/powershell/module/az.resources/get-azadgroup)。

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
```

若要取得 Azure AD 服務主體或應用程式的物件識別碼，請使用 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)。

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
```

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>為資源群組範圍的使用者建立角色指派

若要將存取權授與資源群組範圍中的使用者，請使用[new-azroleassignment](/powershell/module/az.resources/new-azroleassignment)。

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>使用唯一角色識別碼建立角色指派

有一些時間可能會變更角色名稱，例如：

- 您會使用自己的自訂角色，並決定變更名稱。
- 您在名稱中使用的預覽角色具有 **（預覽）** 。 釋放角色時，會重新命名角色。

> [!IMPORTANT]
> 預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

即使角色已重新命名，角色識別碼也不會變更。 如果您使用腳本或自動化來建立角色指派，最佳做法是使用唯一角色識別碼，而不是角色名稱。 因此，如果重新命名角色，您的腳本比較有可能會使用。

若要使用唯一角色識別碼（而非角色名稱）來建立角色指派，請使用[new-azroleassignment](/powershell/module/az.resources/new-azroleassignment)。

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

下列範例會將「[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)」角色指派給*醫藥-sales*資源群組範圍的 *alain@example.com* 使用者。 若要取得唯一角色識別碼，您可以使用[get-azroledefinition](/powershell/module/az.resources/get-azroledefinition)或查看[適用于 Azure 資源的內建角色](built-in-roles.md)。

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="create-a-role-assignment-for-a-group-at-a-resource-scope"></a>為資源範圍的群組建立角色指派

若要將存取權授與資源範圍中的群組，請使用[new-azroleassignment](/powershell/module/az.resources/new-azroleassignment)。

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

### <a name="create-a-role-assignment-for-an-application-at-a-subscription-scope"></a>為訂用帳戶範圍的應用程式建立角色指派

若要在訂用帳戶範圍授與應用程式的存取權，請使用[new-azroleassignment](/powershell/module/az.resources/new-azroleassignment)。

```azurepowershell
New-AzRoleAssignment -ObjectId <application_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>為管理群組範圍的使用者建立角色指派

若要在管理群組範圍授與使用者存取權，請使用[new-azroleassignment](/powershell/module/az.resources/new-azroleassignment)。 若要取得管理群組識別碼，您可以在 **管理群組**] 分頁的 [Azure 入口網站中找到，也可以使用[AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)。

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Billing Reader" -Scope /providers/Microsoft.Management/managementGroups/marketing-group

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-access"></a>移除存取

在 RBAC 中，若要移除存取權，您需使用 [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) 來移除角色指派。

下列範例會從*醫藥-sales*資源群組上的*alain \@example .com*使用者移除「*虛擬機器參與者*」角色指派：

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

下列範例會從訂用帳戶範圍中的 < object_id > 移除 < role_name > 角色。

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

下列範例會從管理群組範圍的 < object_id > 移除 < role_name > 角色。

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

如果您收到錯誤訊息：「提供的資訊未對應至角色指派」，請確定您也指定了 `-Scope` 或 `-ResourceGroupName` 參數。 如需詳細資訊，請參閱[針對 Azure 資源的 RBAC 進行疑難排解](troubleshooting.md#role-assignments-with-unknown-security-principal)。

## <a name="next-steps"></a>後續步驟

- [教學課程：使用 RBAC 和 Azure PowerShell 為群組授與 Azure 資源的存取權](tutorial-role-assignments-group-powershell.md)
- [教學課程：使用 Azure PowerShell 建立適用于 Azure 資源的自訂角色](tutorial-custom-role-powershell.md)
- [使用 Azure PowerShell 管理資源](../azure-resource-manager/manage-resources-powershell.md)
