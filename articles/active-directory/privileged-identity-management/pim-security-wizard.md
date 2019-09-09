---
title: PIM 中的 Azure AD 角色安全性嚮導-Azure Active Directory |Microsoft Docs
description: 說明您可以透過 Azure AD Privileged Identity Management (PIM) 用來將永久特殊權限 Azure AD 角色轉換為合格角色的安全性精靈。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d6d94df29ba16ecee06d70f5edac15a96a4299d
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804008"
---
# <a name="azure-ad-roles-security-wizard-in-pim"></a>PIM 中的 Azure AD 角色安全性精靈

如果您是您組織的第一個執行 Azure Active Directory （Azure AD） Privileged Identity Management （PIM），您將會看到一個 wizard。 精靈會協助您了解特殊權限身分識別的安全性風險，以及如何使用 PIM 來降低這些風險。 如果您想要稍後再進行變更，就不需要在精靈中對現有的角色指派進行任何變更。

## <a name="wizard-overview"></a>精靈概觀

在貴組織開始使用 PIM 之前，所有的角色指派都是永久的︰即使使用者目前不需要其權限，他們都一直保有這些角色。 精靈的第一個步驟會顯示高特殊權限角色的清單，以及這些角色中目前有多少使用者。 如果不清楚一或多個角色，您可以向內切入某個特定角色來深入了解使用者。

精靈的第二個步驟會讓您有機會變更系統管理員的角色指派。  

> [!WARNING]
> 您必須至少有一個全域管理員，以及多個具備組織帳戶 (而非 Microsoft 帳戶) 的特殊權限角色管理員。 如果只有一個特殊權限角色管理員，若該帳戶遭到刪除，組織就無法管理 PIM。
> 此外，如果使用者擁有 Microsoft 帳戶 (他們用來登入 Skype 和 Outlook.com 這類 Microsoft 服務的帳戶)，請將角色指派設為永久。 如果您打算要求必須執行 MFA 才能啟用該角色，該使用者將會遭到封鎖。

## <a name="run-the-wizard"></a>執行精靈

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure AD 角色]，然後按一下 [精靈]。

    ![[Azure AD 角色]-[Wizard] 頁面，顯示執行嚮導的3個步驟](./media/pim-security-wizard/wizard-start.png)

1. 按一下 [1 探索特殊權限角色]。

1. 檢閱特殊權限角色清單，以查看哪些使用者為永久或合格。

    ![探索特殊許可權角色-顯示永久和合格成員的角色窗格](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. 按一下 [下一步] 以選取您要設為合格的成員。

    ![將成員轉換為合格頁面，並提供選項來選取您想要讓角色符合資格的成員](./media/pim-security-wizard/convert-members-eligible.png)

1. 選取成員之後，按一下 [下一步]。

    ![[查看變更] 頁面，顯示具有將轉換之永久角色指派的成員](./media/pim-security-wizard/review-changes.png)

1. 按一下 [確定] 將永久指派轉換為合格。

    轉換完成時，您會看到通知。

    ![顯示轉換狀態的通知](./media/pim-security-wizard/notification-completion.png)

如果您需要將其他特殊權限角色指派轉換為合格，您可以再次執行此精靈。 如果您想要使用 PIM 介面，而不是 wizard，請參閱[在 pim 中指派 Azure AD 角色](pim-how-to-add-role-to-user.md)。

## <a name="next-steps"></a>後續步驟

- [在 PIM 中指派 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [授與其他系統管理員存取權以管理 PIM](pim-how-to-give-access-to-pim.md)
