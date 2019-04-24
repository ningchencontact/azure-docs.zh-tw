---
title: 您無法管理 PIM-Azure Active Directory 中的角色 |Microsoft Docs
description: 說明您無法在 Azure AD Privileged Identity Management (PIM) 中管理的角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/18/2019
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa5fb632ee5fd9c18bde7443e81fe2ef6e5335e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60437268"
---
# <a name="roles-you-cannot-manage-in-pim"></a>您無法在 PIM 中管理的角色

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 可讓您管理所有[Azure AD 角色](../users-groups-roles/directory-assign-admin-roles.md)和 全部[Azure 資源角色](../../role-based-access-control/built-in-roles.md)。 這些角色也包含連結至管理群組、訂用帳戶、資源群組和資源的自訂角色。 不過，您無法管理某些角色。 本文將說明您無法在 PIM 中管理的角色。

## <a name="classic-subscription-administrator-roles"></a>傳統訂用帳戶管理員角色

您無法在 PIM 中管理下列傳統訂用帳戶管理員角色：

- 帳戶管理員
- 服務管理員
- 共同管理員

如需傳統訂用帳戶管理員角色的詳細資訊，請參閱[傳統訂用帳戶管理員角色、Azure RBAC 角色和 Azure AD 管理員角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

## <a name="what-about-office-365-admin-roles"></a>那麼 Office 365 管理員角色呢？

Exchange Online 或 SharePoint Online 內的角色 (除了 Exchange 管理員和 SharePoint 管理員) 不會出現在 Azure AD 中，因此無法在 PIM 中管理。 如需有關這些 Office 365 服務的詳細資訊，請參閱 [Office 365 管理員角色](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles)。

> [!NOTE]
> SharePoint 管理員具有透過 SharePoint Online 系統管理中心存取 SharePoint Online 的系統管理權限，並可在 SharePoint Online 執行幾乎所有工作。 在 PIM 中啟用此角色之後，合格的使用者在 SharePoint 內使用此角色時可能會有延遲。

## <a name="next-steps"></a>後續步驟

- [將 Azure AD PIM 中的角色指派](pim-how-to-add-role-to-user.md)
- [在 PIM 中指派 Azure 資源角色](pim-resource-roles-assign-roles.md)
