---
title: 清單會拒絕使用 Azure PowerShell 的 Azure 資源的指派 |Microsoft Docs
description: 了解如何列出使用者、 群組、 服務主體和已拒絕存取特定 Azure 資源動作，在使用 Azure PowerShell 的特定範圍的受管理身分識別。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: c1ea26fdb4d60262f89ea6ab0f87220a08c01e68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110481"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>清單會拒絕使用 Azure PowerShell 的 Azure 資源的指派

[拒絕指派](deny-assignments.md)會封鎖使用者執行特定的 Azure 資源動作，即使角色指派授予他們存取權也一樣。 本文說明如何列出拒絕使用 Azure PowerShell 的指派。

> [!NOTE]
> 您無法直接建立您自己拒絕指派。 如有關拒絕建立指派，請參閱 <<c0> [ 拒絕指派](deny-assignments.md)。

## <a name="prerequisites"></a>必要條件

若要取得拒絕指派的相關資訊，您必須具備：

- `Microsoft.Authorization/denyAssignments/read` 權限，隨附於大部分[適用於 Azure 資源的內建角色](built-in-roles.md)
- [Azure Cloud Shell 中的 PowerShell](/azure/cloud-shell/overview)或[Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>清單會拒絕指派

### <a name="list-all-deny-assignments"></a>列出所有拒絕指派

若要列出所有拒絕目前訂用帳戶，使用指派[Get AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)。

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>清單會拒絕在資源群組範圍的指派

若要列出所有拒絕的資源群組範圍內，使用指派[Get AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)。

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>清單會拒絕在訂用帳戶範圍的指派

若要列出所有拒絕訂用帳戶範圍內，使用指派[Get AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)。 若要取得訂用帳戶識別碼，您可以找到它**訂用帳戶**刀鋒視窗中，Azure 入口網站，或者您可以使用[Get AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription)。

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>後續步驟

- [了解 Azure 資源的拒絕指派](deny-assignments.md)
- [清單會拒絕使用 Azure 入口網站的 Azure 資源的指派](deny-assignments-portal.md)
- [使用 REST API 列出 Azure 資源的拒絕指派](deny-assignments-rest.md)