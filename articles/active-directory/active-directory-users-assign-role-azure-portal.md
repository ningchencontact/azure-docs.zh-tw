---
title: 在 Azure Active Directory 中將使用者指派給系統管理員角色 | Microsoft Docs
description: 說明如何在 Azure Active Directory 中變更使用者系統管理資訊
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: 61662acb10a6f2085d297eae473e1330c619d48d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>在 Azure Active Directory 中將使用者指派給系統管理員角色
本文說明如何在 Azure Active Directory (Azure AD) 中將系統管理角色指派給使用者。 如需有關在您組織中新增新使用者的資訊，請參閱[將新的使用者新增到 Azure Active Directory](active-directory-users-create-azure-portal.md)。 新增的使用者預設不會有系統管理員權限，但是您可以隨時指派角色給他們。

## <a name="assign-a-role-to-a-user"></a>將角色指派給使用者
1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure AD 管理中心](https://aad.portal.azure.com)。
2. 選取 [使用者和群組]。

   ![開啟使用者管理](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. 選取 [所有使用者]。
  
  ![開啟所有使用者群組](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
4. 從清單中選取使用者。
5. 針對選取的使用者，選取 [目錄角色]，然後將使用者指派給 [目錄角色] 清單中的角色。 如需有關使用者和系統管理員角色的詳細資訊，請參閱 [在 Azure AD 中指派系統管理員角色](active-directory-assign-admin-roles-azure-portal.md)。

      ![將使用者指派給角色](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. 選取 [ **儲存**]。

## <a name="next-steps"></a>後續步驟
* [快速入門：在 Azure Active Directory 中新增或刪除使用者](add-users-azure-active-directory.md)
* [管理使用者設定檔](active-directory-users-profile-azure-portal.md)
* [從另一個目錄中新增來賓使用者](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [在 Azure AD 中將使用者指派給角色](active-directory-users-assign-role-azure-portal.md)
* [還原已刪除的使用者](active-directory-users-restore.md)
