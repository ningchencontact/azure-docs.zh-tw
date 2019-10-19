---
title: 針對 Privileged Identity Management Azure Active Directory 問題進行疑難排解 |Microsoft Docs
description: 瞭解如何使用 Azure AD Privileged Identity Management （PIM）中的角色對系統錯誤進行疑難排解。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b7fc3508f7f672e277577f92218ff1860b676cb
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72559472"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>疑難排解 Privileged Identity Management 的問題

您在 Azure Active Directory （Azure AD）中的 Privileged Identity Management （PIM）有問題嗎？ 後續資訊可協助您重新運作項目。

## <a name="access-to-azure-resources-denied"></a>拒絕存取 Azure 資源

### <a name="problem"></a>問題

當您嘗試讓使用者符合 Azure AD 系統管理員角色的資格，而且您無法在 Privileged Identity Management 下存取 Azure 資源時，就會收到授權錯誤。 即使您是全域管理員和訂用帳戶的擁有者，您仍無法在 Privileged Identity Management 下存取 Azure 資源。

### <a name="cause"></a>原因

當 PIM 服務主體的使用者存取系統管理員角色不小心從訂用帳戶中移除時，就會發生此問題。 若要讓 Privileged Identity Management 服務能夠存取 Azure 資源，應一律透過 Azure 訂用帳戶將「[使用者存取系統管理員」角色](../../role-based-access-control/built-in-roles.md#user-access-administrator)指派給「MS-PIM 服務主體」。

### <a name="resolution"></a>解析度

將使用者存取系統管理員角色指派給訂用帳戶層級的特殊許可權身分識別管理服務主體名稱（MS-PIM）。 此指派應允許特殊許可權身分識別管理服務存取 Azure 資源。 角色可以在管理群組層級或訂用帳戶層級上指派，視您的需求而定。 如需服務主體的詳細資訊，請參閱[將應用程式指派給角色](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role)。

## <a name="next-steps"></a>後續步驟

- [使用 Privileged Identity Management 的授權需求](subscription-requirements.md)
- [在 Azure AD 中保護混合式部署和雲端部署的特殊權限存取](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [部署 Privileged Identity Management](pim-deployment-plan.md)
