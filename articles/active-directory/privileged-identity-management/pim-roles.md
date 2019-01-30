---
title: 您無法在 PIM 中管理的角色 - Azure | Microsoft Docs
description: 說明您無法在 Azure AD Privileged Identity Management (PIM) 中管理的角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 01/18/2019
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: e4c60c6ecdc586ba97a01fa42cd80b8ed541fa61
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434933"
---
# <a name="roles-you-cannot-manage-in-pim"></a>您無法在 PIM 中管理的角色

Azure AD Privileged Identity Management (PIM) 可讓您管理所有 [Azure AD 目錄角色](../users-groups-roles/directory-assign-admin-roles.md)和所有 [Azure 資源角色](../../role-based-access-control/built-in-roles.md)。 這些角色也包含連結至管理群組、訂用帳戶、資源群組和資源的自訂角色。 不過，您無法管理某些角色。 本文將說明您無法在 PIM 中管理的角色。

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

- [開始使用 PIM](pim-getting-started.md)
- [在 PIM 中指派 Azure AD 目錄角色](pim-how-to-add-role-to-user.md)
- [在 PIM 中指派 Azure 資源角色](pim-resource-roles-assign-roles.md)
