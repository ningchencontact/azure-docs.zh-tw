---
title: 如何為 Azure Stack 建立註冊角色
description: 如何建立自訂角色，才能避免使用全域管理員進行註冊。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2019
ms.author: patricka
ms.reviewer: rtiberiu
ms.openlocfilehash: 89fe0889e08da2365523b27262e912ff5403f7f1
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2019
ms.locfileid: "54233321"
---
# <a name="create-a-registration-role-for-azure-stack"></a>為 Azure Stack 建立註冊角色

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

您不想要授予 Azure 訂用帳戶的擁有者權限時，您可以建立自訂角色，將權限指派給使用者帳戶來註冊 Azure Stack。

> [!WARNING]
> 這不是安全性狀態功能。 請在您想要條件約束以避免不小心變更 Azure 訂用帳戶時使用。 使用者被委派這個自訂角色的權限時，使用者有權編輯權限和提高權限。 只能將您信任的使用者指派給自訂角色。

註冊 Azure Stack 時，註冊帳戶需要下列 Azure Active Directory 權限和 Azure 訂用帳戶的權限：

* **Azure Active Directory 租用戶中的應用程式註冊權限：** 管理員擁有應用程式註冊權限。 使用者的權限是租用戶中的所有使用者的全域設定。 若要檢視或變更設定，請參閱[使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)。

    *使用者可以註冊應用程式*設定必須設為**是**，您才能啟用使用者帳戶來註冊 Azure Stack。 如果應用程式註冊設定設為**否**，則您無法使用使用者帳戶，而必須使用全域管理員帳戶來註冊 Azure Stack。

* **一組足夠的 Azure 訂用帳戶權限：** 擁有者群組中的使用者有足夠的權限。 對於其他帳戶，您可以指派自訂角色來指派權限，如下列各節所述。

## <a name="create-a-custom-role-using-powershell"></a>使用 PowerShell 建立自訂角色

若要建立自訂角色，您必須擁有所有 `AssignableScopes` 的 `Microsoft.Authorization/roleDefinitions/write` 權限，例如[擁有者](../role-based-access-control/built-in-roles.md#owner)或[使用者存取系統管理員](../role-based-access-control/built-in-roles.md#user-access-administrator)。 使用下列的 JSON 範本來簡化定義自訂角色。 此範本會建立自訂角色，以便允許 Azure Stack 註冊的必要讀取和寫入權限。

1. 建立 JSON 檔案。 例如，`C:\CustomRoles\registrationrole.json`
2. 將下列 JSON 新增至檔案。 使用您的 Azure 訂用帳戶識別碼來取代 <SubscriptionID> 。

    ```json
    {
      "Name": "Azure Stack registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read"
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. 在 PowerShell 中，連接到 Azure 使用 Azure Resource Manager。 出現提示時，驗證使用的帳戶是否擁有足夠權限，例如[擁有者](../role-based-access-control/built-in-roles.md#owner)或是[使用者存取管理員](../role-based-access-control/built-in-roles.md#user-access-administrator)。

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. 若要將角色新增至訂用帳戶，請使用 **New-AzureRmRoleDefinition** 指定 JSON 範本檔案。

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>將使用者指派給登錄角色

建立註冊的自訂角色之後，指派使用者註冊 Azure Stack 的角色。

1. 使用對於 Azure 訂用帳戶擁有足夠權限的帳戶登入，以便委派[擁有者](../role-based-access-control/built-in-roles.md#owner)或[使用者存取管理員](../role-based-access-control/built-in-roles.md#user-access-administrator)的權限。
2. 在 [訂用帳戶] 中，選取 [存取控制 (IAM)] > [新增角色指派]。
3. 在 [角色] 中，選擇您建立 *Azure Stack 註冊角色*的自訂角色。
4. 選取您要指派給角色的使用者。
5. 選取 [儲存] 將選取的使用者指派至角色。

    ![選取要指派給角色的使用者](media/azure-stack-registration-role/assign-role.png)

如需使用自訂角色的詳細資訊，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>後續步驟

[向 Azure 註冊 Azure Stack](azure-stack-registration.md)
