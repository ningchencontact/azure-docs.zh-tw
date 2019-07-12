---
title: 在 Azure Active Directory 中將使用者或群組指派給企業應用程式 | Microsoft Docs
description: 如何在 Azure Active Directory 中選取企業應用程式以將使用者或群組指派給它
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 249dfeeb231c61b05af2e89f0dc02822cc18e627
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702181"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>在 Azure Active Directory 中將使用者或群組指派給企業應用程式

若要將使用者或群組指派給企業應用程式，您必須具備適當的權限，才能管理企業應用程式，而且必須是目錄的全域管理員。 如果是 Microsoft 應用程式 (例如 Office 365 應用程式)，請使用 PowerShell 將使用者指派給企業應用程式。

> [!NOTE]
> 如需本文所討論功能的授權需求，請參閱 [Azure Active Directory 價格頁面](https://azure.microsoft.com/pricing/details/active-directory)。

## <a name="assign-a-user-to-an-app---portal"></a>將使用者指派給應用程式 - 入口網站

1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com) 。
1. 選取 [所有服務]  、在文字方塊中輸入 Azure Active Directory，然後選取 **Enter** 鍵。
1. 選取 [企業應用程式]  。
1. 在 [**企業應用程式-所有應用程式**] 窗格中，您會看到一份可以管理的應用程式。 選取應用程式。
1. 在  ***appname***窗格 （也就是窗格的標題中所選的應用程式名稱），選取**使用者和群組**。
1. 在  ***appname*** **-使用者和群組**窗格中，選取**新增使用者**。
1. 在 **將作業加入**窗格中，選取**使用者和群組**。

   ![將使用者或群組指派給應用程式](./media/assign-user-or-group-access-portal/assign-users.png)

1. 在上**使用者和群組**窗格中，從清單中選取一或多個使用者或群組，然後選擇**選取**窗格底部的按鈕。
1. 在 **將作業加入**窗格中，選取**角色**。 然後，在**選取的角色** 窗格中選取角色，才能套用至選取的使用者或群組，然後選取**確定**窗格的底部。
1. 在 **將作業加入**窗格中，選取**指派**窗格底部的按鈕。 受指派的使用者或群組將會具備此企業應用程式的所選角色所定義的權限。

## <a name="allow-all-users-to-access-an-app---portal"></a>允許所有使用者存取應用程式 - 入口網站

1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com) 。
1. 選取 [所有服務]  、在文字方塊中輸入 Azure Active Directory，然後選取 **Enter** 鍵。
1. 選取 [企業應用程式]  。
1. 在 [企業應用程式]  窗格上，選取 [所有應用程式]  。 此動作會列出您可以管理的應用程式。
1. 在 [企業應用程式 - 所有應用程式]  窗格上，選取一個應用程式。
1. 在  ***appname***窗格中，選取**屬性**。
1. 上 ***appname* -屬性**窗格中，將**需要使用者指派嗎？** 設定為**否**。

[需要使用者指派?]  選項：

- 不會影響出現在應用程式存取面板上的應用程式。 若要在存取面板上顯示應用程式，您需要將適當的使用者或群組指派給應用程式。
- 只有函式具有已針對 SAML 單一登入設定的雲端應用程式，以及使用應用程式 Proxy 設定的內部部署應用程式。 請參閱[單一登入應用程式](what-is-single-sign-on.md)。
- 要求使用者同意應用程式。 系統管理員可對所有使用者授與同意。  請參閱[設定使用者同意應用程式的方式](configure-user-consent.md)。

## <a name="assign-a-user-to-an-app---powershell"></a>將使用者指派給應用程式 - PowerShell

1. 開啟已提高權限的 Windows PowerShell 命令提示字元。

   > [!NOTE]
   > 您必須安裝 AzureAD 模組 (使用命令 `Install-Module -Name AzureAD`)。 如果系統提示您安裝 NuGet 模組或新的 Azure Active Directory V2 PowerShell 模組，請輸入 Y 然後按 ENTER。

1. 執行 `Connect-AzureAD` 並以全域管理員的使用者帳戶登入。
1. 您可以使用下列指令碼，將使用者和角色指派給應用程式：

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

如需有關如何將使用者指派給應用程式角色的詳細資訊，請瀏覽 [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0) 的文件

若要將群組指派給企業應用程式，您必須將 `Get-AzureADUser` 取代為 `Get-AzureADGroup`。

### <a name="example"></a>範例

此範例會使用 PowerShell 將使用者 Britta Simon 指派至 [Microsoft 工作場所分析](https://products.office.com/business/workplace-analytics)應用程式。

1. 在 PowerShell 中，將對應值指派給變數 $username、$app_name 和 $app_role_name。

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. 在此範例中，我們不清楚要指派給 Britta Simon 的應用程式角色是什麼名稱。 執行下列命令，以利用使用者 UPN 和服務主體顯示名稱來取得使用者 ($user) 和服務主體 ($sp)。

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. 執行命令 `$sp.AppRoles`，顯示工作場所分析應用程式可用的角色。 在此範例中，我們要指派「分析師」(存取權有限) 角色給 Britta Simon。

   ![若要使用工作場所分析角色會顯示可用的角色](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. 指派角色名稱給 `$app_role_name` 變數。

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. 執行下列命令，將使用者指派給應用程式角色：

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>後續步驟

- [查看我的所有群組](../fundamentals/active-directory-groups-view-azure-portal.md)
- [從企業應用程式中移除使用者或群組指派](remove-user-or-group-access-portal.md)
- [停用企業應用程式的使用者登入](disable-user-sign-in-portal.md)
- [變更企業應用程式的名稱或標誌](change-name-or-logo-portal.md)
