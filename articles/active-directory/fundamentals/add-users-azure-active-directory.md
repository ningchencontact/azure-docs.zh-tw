---
title: 新增或刪除使用者 - Azure Active Directory | Microsoft Docs
description: 關於如何使用 Azure Active Directory 新增使用者或刪除現有使用者的指示。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b01eb7730290fbf7340fc0a6d8cac8157498f64a
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013623"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>使用 Azure Active Directory 新增或刪除使用者

從您的 Azure Active Directory （Azure AD）組織新增使用者或刪除現有的使用者。 若要新增或刪除使用者，您必須是使用者系統管理員或全域管理員。

## <a name="add-a-new-user"></a>新增使用者

您可以使用 Azure Active Directory 入口網站建立新使用者。

### <a name="to-add-a-new-user"></a>新增使用者

1. 以組織的使用者系統管理員身分登入[Azure 入口網站](https://portal.azure.com/)。

2. 選取 [Azure Active Directory] 並選取 [使用者]，然後選取 [新增使用者]。

    ![使用者 - 已醒目提示 [新增使用者] 的 [所有使用者] 頁面](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. 在 [**新增使用者**] 頁面上，選取 [**建立使用者**]，然後新增使用者的資訊。

    ![新增使用者，包含使用者資訊的 [使用者] 頁面](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **名稱（必要）** ：新使用者的名字和姓氏。 例如，Chris 綠色。

   - **使用者名稱（必要）** ：新使用者的使用者名稱。 例如， chris@contoso.com。

     使用者名稱的網域部分必須使用初始預設功能變數名稱，<_您功能變數名稱_>. onmicrosoft .com，或 Azure AD 組織中的自訂功能變數名稱，例如 contoso.com。 您可以從可用的網域清單中選取。 您也可以輸入功能變數名稱的一部分來篩選清單。 如需如何建立自訂網域名稱的詳細資訊，請參閱[如何將自訂網域名稱新增到 Azure Active Directory](add-custom-domain.md)。

   - **群組**：您可以將使用者新增至一或多個現有的群組，也可以稍後再進行。 如需有關將使用者新增到群組的詳細資訊，請參閱[如何建立基本的群組及新增成員](active-directory-groups-create-azure-portal.md)。

   - **目錄角色**：如果您需要使用者 Azure AD 系統管理許可權，您可以將他們新增至 Azure AD 角色。 您可以將使用者指派為全域管理員或 Azure AD 中的一或多個受限系統管理員角色。 如需有關指派角色的詳細資訊，請參閱[如何將角色指派給使用者](active-directory-users-assign-role-azure-portal.md)。

   - **作業資訊**：您可以在此新增更多關於使用者的資訊，或稍後再執行。 如需新增使用者資訊的詳細資訊，請參閱[如何新增或變更使用者設定檔資訊](active-directory-users-profile-azure-portal.md)。

4. 複製在 [密碼] 方塊中提供的自動產生密碼。 您可以選擇使用 [密碼] 方塊中提供的自動產生密碼，或建立自訂密碼。 您必須將此密碼提供給使用者，讓其進行初始登入程序。

5. 選取 [建立]。

系統會建立使用者，並將其新增至您的 Azure AD 組織。

## <a name="add-a-new-guest-user"></a>新增來賓使用者

您也可以從 [**新增使用者**] 頁面選取 [**邀請使用者**]，邀請新的來賓使用者與您的組織共同作業。 如果您組織的外部共同作業設定已設定為允許您邀請來賓，則使用者會收到電子郵件給他們必須接受才能開始共同作業的邀請。 如需有關邀請 B2B 共同作業使用者的詳細資訊，請參閱[邀請 b2b 使用者 Azure Active Directory](../b2b/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>新增取用者使用者

在某些情況下，您可能會想要在您的 Azure Active Directory B2C （Azure AD B2C）目錄中手動建立取用者帳戶。 如需建立取用者帳戶的詳細資訊，請參閱[在 Azure AD B2C 中建立和刪除取用者使用者](../../active-directory-b2c/manage-users-portal.md)。

## <a name="add-a-new-user-within-a-hybrid-environment"></a>在混合式環境內新增使用者

若您的環境同時具有 Azure Active Directory (雲端) 與 Windows Server Active Directory (內部部署)，您可以透過同步現有的使用者帳戶資料來新增使用者。 如需有關混合式環境與使用者的詳細資訊，請參閱[整合您的內部部署目錄與 Azure Active Directory](../hybrid/whatis-hybrid-identity.md)。

## <a name="delete-a-user"></a>刪除使用者

您可以使用 Azure Active Directory 入口網站刪除現有的使用者。

### <a name="to-delete-a-user"></a>刪除使用者

1. 使用組織的使用者系統管理員帳戶登入[Azure 入口網站](https://portal.azure.com/)。

1. 選取 [Azure Active Directory] 並選取 [使用者]，然後搜尋並選取您要從您的 Azure AD 租用戶刪除的使用者。 例如 _Mary Parker_。

1. 選取 [刪除使用者]。

    ![使用者 - 已反白顯示 [刪除使用者] 的 [所有使用者] 頁面](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    使用者會被刪除，而且再也不會出現在 [使用者 - 所有使用者] 頁面上。 在接下來的 30 天，您可以在 [刪除的使用者] 頁面上看到該使用者，而且在此期間內可將該使用者還原。 如需有關還原使用者的詳細資訊，請參閱[如何還原或永久移除最近刪除的使用者](active-directory-users-restore.md)。 刪除使用者時，使用者所取用的任何授權都可供其他使用者使用。

    >[!Note]
    >您必須使用 Windows Server Active Directory 來更新使用者(其授權來源是 Windows Server Active Directory) 的身分識別、連絡資訊或工作資訊。 完成更新之後，您必須等候下一次同步處理循環完成，才能看到您所做的變更。

## <a name="next-steps"></a>後續步驟

新增使用者之後，您可以執行下列基本程序：

- [新增或變更設定檔資訊](active-directory-users-profile-azure-portal.md)

- [將角色指派給使用者](active-directory-users-assign-role-azure-portal.md)

- [建立基本群組並新增成員](active-directory-groups-create-azure-portal.md)

- [使用動態群組與使用者](../users-groups-roles/groups-create-rule.md)

或者，您也可以執行其他使用者管理工作，例如[從另一個 Azure AD 組織新增來賓使用者](../b2b/what-is-b2b.md)，或[還原已刪除的使用者](active-directory-users-restore.md)。 如需其他可用動作的詳細資訊，請參閱 [Azure Active Directory 使用者管理文件](../users-groups-roles/index.yml)。
