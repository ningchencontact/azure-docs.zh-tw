---
title: 您無法在 Privileged Identity Management Azure Active Directory 中管理的角色 |Microsoft Docs
description: 說明您無法在 Azure AD Privileged Identity Management (PIM) 中管理的角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fac7074cf85a585c93ece60be9eea8ffb9a6345
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895194"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>您無法在 Privileged Identity Management 中管理的角色

Azure Active Directory （Azure AD） Privileged Identity Management （PIM）可讓您管理所有[Azure AD 角色](../users-groups-roles/directory-assign-admin-roles.md)和所有[Azure 資源角色](../../role-based-access-control/built-in-roles.md)。 這些角色也包含連結至管理群組、訂用帳戶、資源群組和資源的自訂角色。 不過，您無法管理某些角色。 本文說明您無法在 Privileged Identity Management 中管理的角色。

## <a name="classic-subscription-administrator-roles"></a>傳統訂用帳戶管理員角色

您無法在 Privileged Identity Management 中管理下列傳統訂用帳戶管理員角色：

- 帳戶管理員
- 服務管理員
- 共同管理員

如需傳統訂用帳戶管理員角色的詳細資訊，請參閱[傳統訂用帳戶管理員角色、Azure RBAC 角色和 Azure AD 管理員角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

## <a name="what-about-office-365-admin-roles"></a>那麼 Office 365 管理員角色呢？

Exchange Online 或 SharePoint Online 內的角色（Exchange 系統管理員和 SharePoint 系統管理員除外）不會在 Azure AD 中顯示，因此無法在 Privileged Identity Management 中進行管理。 如需有關這些 Office 365 服務的詳細資訊，請參閱 [Office 365 管理員角色](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles)。

> [!NOTE]
> SharePoint 管理員具有透過 SharePoint Online 系統管理中心存取 SharePoint Online 的系統管理權限，並可在 SharePoint Online 執行幾乎所有工作。 在 Privileged Identity Management 中啟用之後，符合資格的使用者可能會遇到在 SharePoint 中使用此角色的延遲。

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中指派 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [在 Privileged Identity Management 中指派 Azure 資源角色](pim-resource-roles-assign-roles.md)
