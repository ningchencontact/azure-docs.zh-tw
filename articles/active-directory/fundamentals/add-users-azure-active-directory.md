---
title: 新增或刪除使用者 - Azure Active Directory | Microsoft Docs
description: 關於如何使用 Azure Active Directory 新增使用者或刪除現有使用者的指示。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1bac4d2c0f236b8fca611c7391846abdb782796
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60247721"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>使用 Azure Active Directory 新增或刪除使用者
在 Azure Active Directory (Azure AD) 组织中添加新用户或删除现有用户。

## <a name="add-a-new-user"></a>新增使用者
您可以使用 Azure Active Directory 入口網站建立新使用者。

### <a name="to-add-a-new-user"></a>新增使用者
1. 以组织的用户管理员身份登录到 [Azure 门户](https://portal.azure.com/)。

2. 選取 [Azure Active Directory] 並選取 [使用者]，然後選取 [新增使用者]。

    ![使用者 - 已反白顯示 [新增使用者] 的 [所有使用者] 頁面](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. 在 [使用者] 頁面上，填妥必要資訊。

    ![新增使用者，包含使用者資訊的 [使用者] 頁面](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **名稱 (必要)。** 新使用者的姓氏與名字。 例如，Mary Parker。

   - **使用者名稱 (必要)。** 新使用者的使用者名稱。 例如： mary@contoso.com。
    
       使用者名稱的網域部分必須使用初始預設網域名稱 (<_yourdomainname_>.onmicrosoft.com)，或自訂網域名稱，例如 contoso.com。 如需如何建立自訂網域名稱的詳細資訊，請參閱[如何將自訂網域名稱新增到 Azure Active Directory](add-custom-domain.md)。

   - **設定檔。** (選擇性) 您可以新增更多有關使用者的資訊。 您也可以稍後新增使用者資訊。 如需新增使用者資訊的詳細資訊，請參閱[如何新增或變更使用者設定檔資訊](active-directory-users-profile-azure-portal.md)。

   - **群組。** (選擇性) 您可以將使用者新增到一或多個現有的群組。 您也可以稍後再將使用者新增到群組。 如需有關將使用者新增到群組的詳細資訊，請參閱[如何建立基本的群組及新增成員](active-directory-groups-create-azure-portal.md)。

   - **目錄角色。** （可选）你可以将用户添加到 Azure AD 管理员角色。 可以将用户分配为全局管理员，或者分配为 Azure AD 中有限的管理员角色中的一个或多个。 如需有關指派角色的詳細資訊，請參閱[如何將角色指派給使用者](active-directory-users-assign-role-azure-portal.md)。

4. 複製在 [密碼] 方塊中提供的自動產生密碼。 您必須將此密碼提供給使用者，讓其進行初始登入程序。

5. 選取 [建立] 。

    使用者已建立並新增至您的 Azure AD 租用戶。

## <a name="add-a-new-user-within-a-hybrid-environment"></a>在混合式環境內新增使用者
若您的環境同時具有 Azure Active Directory (雲端) 與 Windows Server Active Directory (內部部署)，您可以透過同步現有的使用者帳戶資料來新增使用者。 如需有關混合式環境與使用者的詳細資訊，請參閱[整合您的內部部署目錄與 Azure Active Directory](../hybrid/whatis-hybrid-identity.md)。

## <a name="delete-a-user"></a>刪除使用者
您可以使用 Azure Active Directory 入口網站刪除現有的使用者。

### <a name="to-delete-a-user"></a>刪除使用者
1. 使用组织的用户管理员帐户登录到 [Azure 门户](https://portal.azure.com/)。

2. 選取 [Azure Active Directory] 並選取 [使用者]，然後搜尋並選取您要從您的 Azure AD 租用戶刪除的使用者。 例如，_Mary Parker_。

3. 選取 [刪除使用者]。

    ![使用者 - 已反白顯示 [刪除使用者] 的 [所有使用者] 頁面](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    使用者會被刪除，而且再也不會出現在 [使用者 - 所有使用者] 頁面上。 在接下來的 30 天，您可以在 [刪除的使用者] 頁面上看到該使用者，而且在此期間內可將該使用者還原。 如需有關還原使用者的詳細資訊，請參閱[如何還原或永久移除最近刪除的使用者](active-directory-users-restore.md)。 删除某个用户后，该用户使用的任何许可证将可供其他用户使用。

    >[!Note]
    >您必須使用 Windows Server Active Directory 來更新使用者(其授權來源是 Windows Server Active Directory) 的身分識別、連絡資訊或工作資訊。 完成更新之後，您必須等候下一次同步處理循環完成，才能看到您所做的變更。

## <a name="next-steps"></a>後續步驟

新增使用者之後，您可以執行下列基本程序：

- [新增或變更設定檔資訊](active-directory-users-profile-azure-portal.md)

- [將角色指派給使用者](active-directory-users-assign-role-azure-portal.md)

- [建立基本群組並新增成員](active-directory-groups-create-azure-portal.md)

- [使用動態群組與使用者](../users-groups-roles/groups-create-rule.md)

或者，您可以執行其他使用者管理工作，例如[從另一個目錄新增來賓使用者](../b2b/what-is-b2b.md)或[還原已刪除的使用者](active-directory-users-restore.md)。 如需有關其他可用動作的詳細資訊，請參閱 [Azure Active Directory 使用者管理文件](../users-groups-roles/index.yml)。
