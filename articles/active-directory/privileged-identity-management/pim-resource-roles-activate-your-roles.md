---
title: 在 PIM 中啟用我的 Azure 資源角色 | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中啟用 Azure 資源角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 03/05/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: b574e538c407040518f3905f8fbd9fa91d5dd067
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002047"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>在 PIM 中啟用我的 Azure 資源角色

Azure 資源的合格角色成員可使用 Azure AD Privileged Identity Management (PIM)，排程未來啟用的日期與時間。 他們也可以在最大範圍內選取特定的啟用期間 (由管理員設定)。

本文適用於需要在 PIM 中啟用其 Azure 資源角色的成員。

## <a name="activate-a-role"></a>啟用角色

當您需要擔任某個 Azure 資源角色時，您可以在 PIM 中使用 [我的角色] 導覽選項來要求啟用。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟 **Azure AD Privileged Identity Management**。 如需如何將 [PIM] 圖格新增至儀表板的資訊，請參閱[開始使用 PIM](pim-getting-started.md)。

1. 按一下 [我的角色]。

    ![Azure AD 目錄角色與 Azure 資源角色 - 我的角色](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. 按一下  **Azure 資源角色**以查看您的合格的 Azure 資源角色的清單。

   ![Azure 資源角色](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. 在 [Azure 資源角色] 清單中，尋找您要啟用的角色。

    ![Azure 資源角色 - 我的角色清單](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. 按一下 [啟用] 以開啟 [啟用] 窗格。

1. 如果您的角色需要多重要素驗證 (MFA)，請按一下 [先驗證您的身分識別後再繼續]。 您只需在每個工作階段驗證一次。

    ![在啟用角色之前先以 MFA 驗證](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. 按一下 [驗證我的身分識別]，並遵循指示來提供其他安全性驗證。

    ![其他安全性驗證](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 如果您想要指定縮小的範圍，請按一下 [範圍] 開啟 [資源] 篩選窗格。

    最佳做法是僅要求存取您需要的資源。 在 [資源] 篩選窗格中，您可以指定資源群組或您需要存取的群組。

    ![啟用 - 資源篩選](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. 如有必要，請指定自訂啟用開始時間。 成員會在選取的時間後啟用。

1. 在 [原因] 方塊中輸入此啟用要求的原因。

    ![已完成的啟用窗格](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. 按一下 [啟用]。

    如果角色不需核准，就會啟動並新增至使用中角色的清單。 如果您想要使用的角色，請遵循下一節中的步驟。

    如果[角色需要核准](pim-resource-roles-approval-workflow.md)才能啟用，通知會出現在瀏覽器右上角，通知您要求正在等待核准。

    ![要求擱置通知](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>啟用後可立即使用角色

如果在啟用後的任何延遲，請遵循下列步驟之後您啟用即可立即使用您的 Azure 資源角色。

1. 開啟 Azure AD Privileged Identity Management。

1. 按一下 [我的角色] 可查看符合資格的 Azure AD 目錄角色與 AD 資源角色清單。

1. 按一下  **Azure 資源角色**。

1. 按一下 [**作用中的角色**] 索引標籤。

1. 作用中的角色之後，請登出入口網站，再重新登入。

    角色現在應該可供使用。

## <a name="view-the-status-of-your-requests"></a>檢視要求狀態

您可以檢視要啟用的擱置要求狀態。

1. 開啟 Azure AD Privileged Identity Management。

1. 按一下 [我的要求] 可查看您的 Azure AD 目錄角色與 AD 資源角色要求清單。

    ![Azure AD 目錄角色與 Azure 資源角色 - 我的要求](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. 捲動至右側可檢視 [要求狀態] 欄。

## <a name="cancel-a-pending-request"></a>取消擱置要求

如果您不需要啟用需要核准的角色，您可以隨時取消擱置要求。

1. 開啟 Azure AD Privileged Identity Management。

1. 按一下 [我的要求]。

1. 針對您想要取消的角色，按一下 [取消] 連結。

    當您按一下 [取消] 時，將會取消要求。 若要再次啟用角色，您必須提交新的啟用要求。

   ![取消擱置要求](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>疑難排解

### <a name="permissions-not-granted-after-activating-a-role"></a>啟動角色之後未授與權限

當您在 PIM 中啟動角色之後，至少需等候 10 分鐘才能存取所需的系統管理入口網站，或在特定系統管理工作負載內執行函式。 啟用完成後，Azure 入口網站登出再重新登入若要開始使用新的主動的角色。

如需其他疑難排解步驟，請參閱[針對較高的權限進行疑難排解](https://social.technet.microsoft.com/wiki/contents/articles/37568.troubleshooting-elevated-permissions-with-azure-ad-privileged-identity-management.aspx) \(英文\)。

### <a name="cannot-activate-a-role-due-to-a-resource-lock"></a>由於資源鎖定，因此無法啟動角色

如果您收到一則訊息，顯示出當您嘗試啟動角色時有 Azure 資源遭到鎖定，則可能是因為角色指派範圍內有鎖定的資源。 鎖定可保護資源，以免遭到意外刪除或非預期的變更。 鎖定也可防止 PIM 在啟動期間結束時，移除資源上的角色指派。 由於套用資源鎖定時，PIM 無法正常運作，因此 PIM 會禁止使用者啟動資源上的角色。 解決此問題的方式有兩種：

- 依[鎖定資源以防止非預期的變更](../../azure-resource-manager/resource-group-lock-resources.md)所述，將鎖定刪除。
- 如果您想要保持鎖定，請讓角色指派變成永久，或使用急用帳戶。

## <a name="next-steps"></a>後續步驟

- [在 PIM 中延長或更新 Azure 資源角色](pim-resource-roles-renew-extend.md)
- [在 PIM 中啟用我的 Azure AD 目錄角色](pim-how-to-activate-role.md)
