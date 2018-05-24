---
title: 提高 Azure Active Directory 中全域管理員的存取權 | Microsoft Docs
description: 描述如何使用 Azure 入口網站或 REST API 提高 Azure Active Directory 中全域管理員的存取權。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2018
ms.author: rolyon
ms.openlocfilehash: b671ff6b473093e59bce18c7bf98b32e9849bbb0
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077202"
---
# <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>提高 Azure Active Directory 中全域管理員的存取權

如果您是 Azure Active Directory (Azure AD) 中的[全域管理員](../active-directory/active-directory-assign-admin-roles-azure-portal.md#global-administrator)，有時可能需要執行下列作業：

- 當使用者失去存取權時，重新取得 Azure 訂用帳戶的存取權
- 授權其他使用者或您自己存取 Azure 訂用帳戶
- 查看組織中的所有 Azure 訂用帳戶
- 允許自動化應用程式 (例如發票處理或稽核應用程式) 存取所有 Azure 訂用帳戶

根據預設，Azure AD 系統管理員角色和 Azure 角色型存取控制 (RBAC) 角色不會跨越 Azure AD 與 Azure。 不過，如果您是Azure AD 中的全域管理員，您可以提高存取權來管理 Azure 訂用帳戶與管理群組。 當您提高存取權時，您會獲得特定租用戶之所有訂用帳戶的[使用者存取系統管理員](built-in-roles.md#user-access-administrator)角色 (RBAC 角色)。 使用者存取系統管理員角色可讓您授權其他使用者存取根目錄範圍 (`/`) 的 Azure 資源。

提高權限應該是暫時的，而且只有在需要時才會進行。

## <a name="elevate-access-for-a-global-administrator-using-the-azure-portal"></a>使用 Azure 入口網站提高全域管理員的存取權

1. 登入 [Azure 入口網站](https://portal.azure.com)或 [Azure Active Directory 系統管理中心](https://aad.portal.azure.com)。

1. 在瀏覽清單中，按一下 [Azure Active Directory]，然後按一下 [屬性]。

   ![Azure AD 屬性 - 螢幕擷取畫面](./media/elevate-access-global-admin/aad-properties.png)

1. 在 [全域管理員可以管理 Azure 訂用帳戶與管理群組] 下，將開關設定為 [是]。

   ![全域管理員可以管理 Azure 訂用帳戶與管理群組 - 螢幕擷取畫面](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   當您將開關設定為 [是] 時，您的全域管理員帳戶 (目前登入的使用者) 會新增至根目錄範圍 (`/`) 之 Azure RBAC 中的使用者存取系統管理員角色，這會授權您存取與您的 Azure AD 租用戶建立關聯之所有 Azure 訂用帳戶中的檢視和報表。

   當您將開關設定為 [否] 時，您的全域管理員帳戶 (目前登入的使用者) 會從 Azure RBAC 的使用者存取系統管理員角色中移除。 您無法看到與 Azure AD 租用戶建立關聯的所有 Azure 訂用帳戶，而且您只能檢視及管理已獲得存取權的 Azure 訂用帳戶。

1. 按一下 [儲存] 儲存您的設定。

   這個設定不是全域屬性，而且只會套用至目前登入的使用者。

1. 執行您需要以更高存取權完成的工作。 當您完成時，將開關設回 [否]。

## <a name="list-role-assignment-at-the-root-scope--using-powershell"></a>使用 PowerShell 列出根目錄範圍 (/) 的角色指派

若要列出某位使用者在根目錄範圍 (`/`) 的使用者存取系統管理員角色指派，請使用 [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) 命令。

```azurepowershell
Get-AzureRmRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc
ObjectType         : User
```

## <a name="delete-a-role-assignment-at-the-root-scope--using-powershell"></a>使用 PowerShell 刪除根目錄範圍 (/) 的角色指派

若要刪除某位使用者在根目錄範圍 (`/`) 的使用者存取系統管理員角色指派，請使用 [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) 命令。

```azurepowershell
Remove-AzureRmRoleAssignment -SignInName <username@example.com> `
  -RoleDefinitionName "User Access Administrator" -Scope "/"
```

## <a name="elevate-access-for-a-global-administrator-using-the-rest-api"></a>使用 REST API 提高全域管理員的存取權

若要使用 REST API 提高全域管理員的存取權，請使用下列基本步驟。

1. 使用 REST，呼叫 `elevateAccess`，這會授與您在根目錄範圍 (`/`) 的使用者存取系統管理員角色。

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. 建立[角色指派](/rest/api/authorization/roleassignments)以在任何範圍的指派任何角色。 下列範例顯示在根目錄範圍 (`/`) 指派 {roleDefinitionID} 角色的屬性︰

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
   }
   ```

1. 若為使用者存取系統管理員，您也可以在根目錄範圍 (`/`) 刪除角色指派。

1. 撤銷您的使用者存取系統管理員權限，直到再次需要這些權限。


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>如何使用 REST API 復原 elevateAccess 動作

當您呼叫 `elevateAccess` 時，您會建立自己的角色指派，因此您需要刪除指派才能撤銷這些權限。

1. 呼叫 [GET roleDefinitions](/rest/api/authorization/roledefinitions/get)，其中 `roleName` 等於使用者存取系統管理員，以判斷使用者存取系統管理員角色的名稱識別碼。

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
        "roleName": "User Access Administrator",
        "type": "BuiltInRole",
        "description": "Lets you manage user access to Azure resources.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Authorization/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedOn": "2016-05-31T23:14:04.6964687Z",
        "createdBy": null,
        "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    儲存 `name` 參數中的識別碼，在本例中為 `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`。

2. 您也必須列出租用戶系統管理員在租用戶範圍的角色指派。 請列出發出提高存取權呼叫之租用戶系統管理員的 `principalId` 在租用戶範圍的所有指派。 這會列出 objectid 之租用戶中的所有指派。

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >租用戶系統管理員不應該有許多指派，如果先前的查詢傳回太多指派，您也可以查詢只在租用戶範圍層級的所有指派，然後篩選結果：`GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
    2. 先前的呼叫會傳回角色指派清單。 請尋找範圍是 "/"、`roleDefinitionId` 結尾是您在步驟 1 中找到的角色名稱識別碼且 `principalId` 與租用戶系統管理員之 objectId 相符的角色指派。 
    
    範例角色指派：

        ```json
        {
          "value": [
            {
              "properties": {
                "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                "principalId": "{objectID}",
                "scope": "/",
                "createdOn": "2016-08-17T19:21:16.3422480Z",
                "updatedOn": "2016-08-17T19:21:16.3422480Z",
                "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
                "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
              },
              "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
              "type": "Microsoft.Authorization/roleAssignments",
              "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
            }
          ],
          "nextLink": null
        }
        ```
        
    同樣地，儲存 `name` 參數中的識別碼，在本例中為 e7dd75bc-06f6-4e71-9014-ee96a929d099。

    3. 最後，使用角色指派識別碼來刪除 `elevateAccess` 所新增的指派：

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>後續步驟

- [使用 REST 的角色型存取控制](role-assignments-rest.md)
- [管理存取權指派](role-assignments-users.md)
