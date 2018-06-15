---
title: 使用 Azure PowerShell 管理角色型存取控制 (RBAC) | Microsoft Docs
description: 如何使用 Azure PowerShell 管理 RBAC，包括列出角色、指派角色，以及刪除角色指派。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 00646187da1f93c01c3a57b50905239afd5e2bc8
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266793"
---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>使用 Azure PowerShell 管理角色型存取控制
> [!div class="op_single_selector"]
> * [PowerShell](role-assignments-powershell.md)
> * [Azure CLI](role-assignments-cli.md)
> * [REST API](role-assignments-rest.md)

使用角色型存取控制 (RBAC)，您會藉由在特定範圍指派角色，來定義使用者、群組和服務主體的存取權。 本文說明如何使用 Azure PowerShell 管理存取權。

## <a name="prerequisites"></a>先決條件

使用 PowerShell 管理 RBAC 之前，您必須具備下列其中一項：

* [Azure Cloud Shell 中的 PowerShell](/azure/cloud-shell/overview)
* [Azure PowerShell 5.1.0 或更新版本](/powershell/azure/install-azurerm-ps)

## <a name="list-roles"></a>列出角色

### <a name="list-all-available-roles"></a>列出所有可用的角色

若要列出可供指派的 RBAC 角色，以及檢查它們授與存取的作業，使用 [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition)。

```azurepowershell
Get-AzureRmRoleDefinition | FT Name, Description
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

若要列出特定角色，使用 [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition)。

```azurepowershell
Get-AzureRmRoleDefinition <role name>
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
AssignableScopes : {/}
```

### <a name="list-a-specific-role-in-json-format"></a>使用 JSON 格式列出特定角色

若要使用 JSON 格式列出特定角色，使用 [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition)。

```azurepowershell
Get-AzureRmRoleDefinition <role name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Contributor" | ConvertTo-Json

{
    "Name":  "Contributor",
    "Id":  "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "IsCustom":  false,
    "Description":  "Lets you manage everything except access to resources.",
    "Actions":  [
                    "*"
                ],
    "NotActions":  [
                       "Microsoft.Authorization/*/Delete",
                       "Microsoft.Authorization/*/Write",
                       "Microsoft.Authorization/elevateAccess/Action"
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

### <a name="list-actions-of-a-role"></a>列出角色的動作

若要列出特定角色的動作，使用 [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition)。

```azurepowershell
Get-AzureRmRoleDefinition <role name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action}
```

```azurepowershell
(Get-AzureRmRoleDefinition <role name>).Actions
```

```Example
PS C:\> (Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions

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

## <a name="see-who-has-access"></a>查看誰具有存取權

若要列出 RBAC 存取指派，使用 [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment)。

### <a name="list-role-assignments-at-a-specific-scope"></a>列出特定範圍的角色指派

您可以查看指定訂用帳戶、資源群組或資源的所有角色指派。 例如，若要查看某個資源群組的所有使用中指派，使用 [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment)。

```azurepowershell
Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>
```

```Example
PS C:\> Get-AzureRmRoleAssignment -ResourceGroupName pharma-sales-projectforecast | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
```

### <a name="list-roles-assigned-to-a-user"></a>列出指派給使用者的角色

若要列出指派給指定使用者的所有角色，使用 [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment)。

```azurepowershell
Get-AzureRmRoleAssignment -SignInName <user email>
```

```Example
PS C:\> Get-AzureRmRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
```

若要列出指派給指定使用者的所有角色，以及指派給該使用者所屬群組的角色，使用 [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment)。

```azurepowershell
Get-AzureRmRoleAssignment -SignInName <user email> -ExpandPrincipalGroups
```

```Example
Get-AzureRmRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>列出傳統服務管理員和共同管理員角色指派

若要列出傳統訂用帳戶管理員和共同管理員的存取指派，使用 [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment)：

```azurepowershell
Get-AzureRmRoleAssignment -IncludeClassicAdministrators
```

## <a name="grant-access"></a>授與存取權

### <a name="search-for-object-ids"></a>搜尋物件識別碼

若要指派角色，您必須識別物件 (使用者、群組或應用程式) 和範圍。

如果您不知道訂用帳戶 ID，可以在 Azure 入口網站的 [訂用帳戶] 刀鋒視窗中找到，或者，可以使用 [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription)。

若要取得 Azure AD 群組的物件識別碼，使用 [Get-AzureRmADGroup](/powershell/module/azurerm.resources/get-azurermadgroup)：

```azurepowershell
Get-AzureRmADGroup -SearchString <group name in quotes>
```

若要取得 Azure AD 服務主體或應用程式的物件識別碼，使用 [Get-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal)：

```azurepowershell
Get-AzureRmADServicePrincipal -SearchString <service name in quotes>
```

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>將角色指派給訂用帳戶範圍中的應用程式

若要為訂用帳戶範圍中的應用程式授與存取，使用 [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment)：

```azurepowershell
New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>
```

```Example
PS C:\> New-AzureRmRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

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

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>將角色指派給資源群組範圍中的使用者

若要為資源群組範圍中的使用者授與存取，使用 [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment)：

```azurepowershell
New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>
```

```Example
PS C:\> New-AzureRmRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales-projectforecast


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>將角色指派給資源範圍中的群組

若要為資源範圍中的群組授與存取，使用 [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment)：

```azurepowershell
New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>
```

```Example
PS C:\> Get-AzureRmADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzureRmRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

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

## <a name="remove-access"></a>移除存取

若要移除使用者、群組和應用程式的存取，使用 [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment)：

```azurepowershell
Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>
```

```Example
PS C:\> Remove-AzureRmRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales-projectforecast
```

## <a name="list-custom-roles"></a>列出自訂角色

若要列出範圍中可供指派的角色，使用 [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) 命令。

下列範例會列出選取的訂用帳戶中可供指派的所有角色。

```azurepowershell
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

下列範例只會列出所選取訂用帳戶中可供指派的自訂角色。

```azurepowershell
Get-AzureRmRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

如果選取的訂用帳戶不在角色的 `AssignableScopes` 中，將不會列出自訂角色。

## <a name="create-a-custom-role"></a>建立自訂角色

若要建立自訂角色，使用 [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) 命令。 建構角色有兩種方法：使用 `PSRoleDefinition` 物件或 JSON 範本。 

### <a name="get-operations-for-a-resource-provider"></a>取得資源提供者的作業

當您建立自訂角色時，務必知道所有可能來自資源提供者的作業。
您可以檢視[資源提供者作業](resource-provider-operations.md)的清單，或者，可以使用 [Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) 命令來取得此資訊。
例如，如果您想要檢查虛擬機器的所有可用作業，請使用此命令：

```azurepowershell
Get-AzureRMProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-role-with-psroledefinition-object"></a>使用 PSRoleDefinition 物件建立角色

使用 PowerShell 建立自訂角色時，您可以使用其中一個[內建角色](built-in-roles.md)當作起點，或者可以從頭開始。 本節的範例是以內建角色當作起點，然後使用較高權限來自訂它。 編輯屬性來新增您所需的 `Actions`、`NotActions` 或 `AssignableScopes`，然後將變更儲存為新角色。

下列範例會從[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)內建角色開始，來建立稱為「虛擬機器操作員」(Virtual Machine Operator) 的自訂角色。 新角色會授與對 *Microsoft.Compute*、*Microsoft.Storage* 和 *Microsoft.Network* 資源提供者之所有讀取作業的存取權，以及授與對啟動、重新啟動和監視虛擬機器的存取權。 自訂角色可用於兩個訂用帳戶中。

```azurepowershell
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzureRmRoleDefinition -Role $role
```

下列範例示範建立「虛擬機器操作員」自訂角色的另一種方法。 它一開始會建立新的 PSRoleDefinition 物件。 動作作業會指定於 `perms` 變數中，並設定為 `Actions` 屬性。 從[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)內建角色讀取 `NotActions`，藉以設定 `NotActions` 屬性。 由於[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)沒有任何 `NotActions`，因此不需要這一行，但它會顯示如何從另一個角色擷取資訊。

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read','Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzureRmRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzureRmRoleDefinition -Role $role
```

### <a name="create-role-with-json-template"></a>使用 JSON 範本建立角色

JSON 範本可作為自訂角色的來源定義。 下列範例會建立自訂角色來允許讀取儲存體和計算資源及存取支援，然後將該角色新增至兩個訂用帳戶。 建立含有下列範例的新檔案 `C:\CustomRoles\customrole1.json`。 最初建立角色時，應該將 Id 設為 `null`，因為會自動產生新的識別碼。 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

若要將角色新增至訂用帳戶，請執行下列 PowerShell 命令︰

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="modify-a-custom-role"></a>修改自訂角色

類似於建立自訂角色，您可以使用 `PSRoleDefinition` 物件或 JSON 範本來修改現有的自訂角色。

### <a name="modify-role-with-psroledefinition-object"></a>使用 PSRoleDefinition 物件修改角色

若要修改自訂角色，首先使用 [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) 命令來擷取角色定義。 接著，對角色定義進行想要的變更。 最後，使用 [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) 命令來儲存已修改的角色定義。

下列範例會將 `Microsoft.Insights/diagnosticSettings/*` 作業加入到 *Virtual Machine Operator* 自訂角色。

```azurepowershell
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzureRmRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

下列範例會將 Azure 訂用帳戶新增到 *Virtual Machine Operator* 自訂角色的可指派範圍。

```azurepowershell
Get-AzureRmSubscription -SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzureRmSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzureRmRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

### <a name="modify-role-with-json-template"></a>使用 JSON 範本修改角色

您可以利用先前的 JSON 範本，輕鬆修改現有的自訂角色來新增或移除 Actions。 更新 JSON 範本，並新增網路的讀取動作，如下範例所示。 範本中所列的定義不會累積套用至現有的定義，這表示角色會完全依照您在範本中指定的樣子出現。 您也需要以角色的識別碼來更新 Id 欄位。 如果不確定此值，您可以使用 [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) Cmdlet 來取得這項資訊。

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

若要更新現有的角色，請執行下列 PowerShell 命令︰

```azurepowershell
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>刪除自訂角色

若要刪除自訂角色，使用 [Remove-AzureRmRoleDefinition](/powershell/module/azurerm.resources/remove-azurermroledefinition) 命令。

下列範例會移除 *Virtual Machine Operator* 自訂角色

```azurepowershell
Get-AzureRmRoleDefinition "Virtual Machine Operator"
Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="see-also"></a>另請參閱

* [搭配使用 Azure PowerShell 與 Azure 資源管理員](../azure-resource-manager/powershell-azure-resource-manager.md)

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
