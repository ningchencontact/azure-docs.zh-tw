---
title: 在 Azure AD 以角色為基礎的存取控制中建立自訂角色定義-Azure Active Directory |Microsoft Docs
description: 建立具有 Azure Active Directory 資源資源範圍的自訂 Azure AD 角色。
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae773bc1a4e1831dbe462149bb827c26b7e74b96
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722262"
---
# <a name="create-a-custom-role-and-assign-a-resource-scope-in-azure-active-directory"></a>在 Azure Active Directory 中建立自訂角色並指派資源範圍

本文說明如何在 Azure Active Directory (Azure AD) 中建立新的自訂角色。 您可以在 [Azure AD 總覽] 頁面或[[應用程式註冊] 頁面](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)上的 [**角色和系統管理員**] 索引標籤中建立自訂角色。 角色可以在目錄層級範圍指派, 或僅限於應用程式註冊。

如需詳細資訊, 請參閱[自訂角色總覽](roles-custom-overview.md), 以取得自訂角色的基本概念。

## <a name="using-the-azure-ad-portal"></a>使用 Azure AD 入口網站

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>建立新的自訂角色, 以授與管理應用程式註冊的存取權

1. 使用特殊權限角色管理員或全域管理員 Azure AD 組織中的許可權登入 [Azure AD 系統管理中心。](https://aad.portal.azure.com)  
1. 選取 [ **Azure Active Directory**  > **角色和系統管理員** >] **新的自訂角色**。

   ![從 [角色和系統管理員] 頁面建立或編輯角色](./media/roles-create-custom/new-custom-role.png)

1. 在 [**基本**] 索引標籤上, 提供角色的名稱和描述。

   ![在 [基本] 索引標籤上提供自訂角色的名稱和描述](./media/roles-create-custom/basics-tab.png)

1. 若要選取管理應用程式註冊認證和基本屬性 (例如名稱) 所需的許可權:
   1. 在搜尋列中輸入「認證」, 然後選取`microsoft.directory/applications/credentials/update`許可權。

      ![在 [許可權] 索引標籤上選取自訂角色的許可權](./media/roles-create-custom/permissions-tab.png)

   1. 在搜尋列中輸入「基本」, 選取`microsoft.directory/applications/basic/update`許可權, 然後按 **[下一步]** 。
1. 在 [**審查 + 建立**] 索引標籤上, 檢查許可權, 然後選取 [**建立**]。

您的自訂角色會顯示在可用角色的清單中, 以供指派。

## <a name="assign-a-role-scoped-to-a-resource"></a>指派範圍限於某個資源的角色

就像內建角色一樣, 您可以在預設的組織範圍指派自訂角色, 以授與所有應用程式註冊的存取權。 但是, 您也可以在物件範圍指派自訂角色。 這可讓您為受託人提供更新單一應用程式認證和基本屬性的許可權, 而不需要建立第二個自訂角色。

1. 使用 Azure AD 組織中應用程式開發人員許可權登入 [Azure AD 系統管理中心](https://aad.portal.azure.com)。
1. 選取 [應用程式註冊]。
1. 選取您要授與 [管理] 存取權的應用程式註冊。 您可能必須選取 [**所有應用程式**], 才能在您的 Azure AD 組織中查看應用程式註冊的完整清單。

    ![選取 [應用程式註冊] 做為角色指派的資源範圍](./media/roles-create-custom/appreg-all-apps.png)

1. 在應用程式註冊中, 選取 [**角色和系統管理員**]。 如果您尚未建立, 請在[上述](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)程式中取得指示。

    如果您從開啟的應用程式註冊內容指派此角色, 則該「受託人」具有該單一應用程式註冊的許可權。 您所指派的角色會顯示在每個應用程式註冊的清單中。 此存取模型 (其中擁有者可以依角色指派特定 Azure AD 資源的許可權) 類似于 azure [RBAC](../../role-based-access-control/overview.md)中用於 azure 資源存取控制的模型。

1. 選取角色以開啟 [**指派**] 頁面。
1. 選取 [**新增指派**] 以新增使用者。 除了選取的任何應用程式註冊之外, 不會對使用者授與任何許可權。

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>使用 Azure AD PowerShell 建立自訂角色

### <a name="prepare-powershell"></a>準備 PowerShell

首先，您必須 [下載 Azure AD PowerShell 模組](https://www.powershellgallery.com/packages/AzureAD/)。

若要安裝 AzureAD PowerShell 模組，請使用下列命令︰

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

若要確認此模組已可使用，請使用下列命令︰

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>建立自訂角色

使用下列 PowerShell 腳本建立新的角色:

``` PowerShell
# Basic role information
$description = "Application Support Administrator"
$displayName = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/allProperties/read",
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customAdmin = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

## <a name="create-a-custom-role-using-microsoft-graph-api"></a>使用 Microsoft Graph API 建立自訂角色

1. 建立角色定義。

    建立自訂角色定義的 HTTP 要求。

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    本文

    ``` HTTP
    {
    "description":"Can manage basic aspects of application registrations.",
    "displayName":"Application Support Administrator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "allowedResourceActions": 
            [
                "microsoft.directory/applications/basic/read",
                "microsoft.directory/applications/credentials/update"
            ]
        }
    ]
    }
    ```

1. 建立角色指派。

    建立自訂角色定義的 HTTP 要求。

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions/roleAssignments
    ```

    本文

    ``` HTTP
    {
    "principalId":"<GUID OF USER>",
    "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
    "resourceScope":["/<GUID OF APPLICATION REGISTRATION>"]
    }
    ```

## <a name="next-steps"></a>後續步驟

- 歡迎在 [Azure AD 系統管理角色論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上與我們共用資訊。
- 如需角色和管理員角色指派的詳細資訊，請參閱[指派管理員角色](directory-assign-admin-roles.md)。
- 如需預設使用者權限，請參閱[預設來賓和成員使用者權限的比較](../fundamentals/users-default-permissions.md)。
