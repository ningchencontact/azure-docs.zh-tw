---
title: 檢視 Azure Active Directory 中的系統管理員角色成員 | Microsoft Docs
description: 您現在可以查看和管理入口網站中的 Azure AD 系統管理員角色成員。 針對經常管理角色指派的人員。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/02/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 39f7d69482845af48b1dce8c2e51f4acf77bf4fb
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448055"
---
# <a name="view-all-members-of-an-administrator-role-in-azure-active-directory"></a>檢視 Azure Active Directory 中的所有系統管理員角色成員

您現在可以查看和管理 Azure Active Directory 入口網站中的所有系統管理員角色成員。 如果您經常管理角色指派，則可能會偏好這項體驗。 而且，如果您曾經想過「這些角色的實際作用到底為何？」，則可以查看每個 Azure AD 系統管理員角色的詳細權限清單。

也很容易就可以看到您自己的權限。 按一下 [您的角色] 快速存取您的使用者頁面，以取得所有作用中獲指派角色的清單。 按一下每個資料列右側的省略符號，以開啟角色的詳細描述。

![Azure AD 入口網站中的角色清單](./media/directory-manage-roles-portal/role-list.png)

選取整個資料列，以檢視所指派成員的清單。 您可以選取 [Manage in PIM] \(在 PIM 中管理\)，以取得額外的管理功能。 特殊權限角色管理員可以將「永久」(一律在角色中為作用中) 指派變更為「合格」(只有在提高權限時才在角色中)。 如果您沒有 PIM，則仍然可以選取 [Manage in PIM] \(在 PIM 中管理\) 來註冊試用版。 Privileged Identity Management 需要 [Azure AD Premium P2 授權方案](./privileged-identity-management/subscription-requirements.md)。

![管理員角色成員清單](./media/directory-manage-roles-portal/member-list.png)

如果您是全域管理員或特殊權限角色管理員，則可以輕鬆地新增或移除成員、篩選清單，或選取要移至使用者頁面的成員，以查看其作用中獲指派角色。 

## <a name="role-details-in-the-portal"></a>入口網站中的角色詳細資料

當您檢視角色的成員時，請選取 [描述] 以查看角色指派所授與權限的完整清單。 此頁面包括相關文件的連結，協助引導您管理目錄角色。

![管理員角色權限清單](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>後續步驟

* 歡迎在 [Azure AD 系統管理角色論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上與我們分享資訊。
* 如需角色和管理員角色指派的詳細資訊，請參閱[指派管理員角色](users-groups-roles/directory-assign-admin-roles.md)。
* 如需預設使用者權限，請參閱[預設來賓和成員使用者權限的比較](./fundamentals/users-default-permissions.md)。
