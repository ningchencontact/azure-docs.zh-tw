---
title: 在 PIM 中啟用我的 Azure 資源角色-Azure Active Directory |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中啟用 Azure 資源角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e2bf1253f1c2f53b839a73034fcdb33f3aab463
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804345"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>在 PIM 中啟用我的 Azure 資源角色

使用 Azure Active Directory （Azure AD） Privileged Identity Management （PIM），適用于 Azure 資源的合格角色成員可以排程未來的日期和時間啟用。 他們也可以在最大範圍內選取特定的啟用期間 (由管理員設定)。

本文適用於需要在 PIM 中啟用其 Azure 資源角色的成員。

## <a name="activate-a-role"></a>啟用角色

當您需要擔任某個 Azure 資源角色時，您可以在 PIM 中使用 [我的角色] 導覽選項來要求啟用。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟 **Azure AD Privileged Identity Management**。 如需如何將 [PIM] 圖格新增至儀表板的資訊，請參閱[開始使用 PIM](pim-getting-started.md)。

1. 按一下 [我的角色]。

    ![顯示您可以啟用之角色的 [我的角色] 頁面](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. 按一下 [ **Azure 資源角色**] 以查看符合資格的 Azure 資源角色清單。

   ![我的角色-Azure 資源角色頁面](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. 在 [Azure 資源角色] 清單中，尋找您要啟用的角色。

    ![Azure 資源角色-我的合格角色清單](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. 按一下 [啟用] 以開啟 [啟用] 窗格。

1. 如果您的角色需要多重要素驗證 (MFA)，請按一下 [先驗證您的身分識別後再繼續]。 您只需在每個工作階段驗證一次。

    ![在啟用角色之前，使用 MFA 驗證我的身分識別](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. 按一下 [驗證我的身分識別]，並遵循指示來提供其他安全性驗證。

    ![提供安全性驗證（例如 PIN 碼）的畫面](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 如果您想要指定縮小的範圍，請按一下 [範圍] 開啟 [資源] 篩選窗格。

    最佳做法是僅要求存取您需要的資源。 在 [資源] 篩選窗格中，您可以指定資源群組或您需要存取的群組。

    ![啟用-資源篩選窗格以指定範圍](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. 如有必要，請指定自訂啟用開始時間。 成員會在選取的時間後啟用。

1. 在 [原因] 方塊中輸入此啟用要求的原因。

    ![已完成的啟用窗格，範圍、開始時間、持續時間和原因](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. 按一下 **[啟用]** 。

    如果角色不需核准，就會啟動並新增至使用中角色的清單。 如果您想要使用角色，請遵循下一節中的步驟。

    如果[角色需要核准](pim-resource-roles-approval-workflow.md)才能啟用，通知會出現在瀏覽器右上角，通知您要求正在等待核准。

    ![啟用要求正在等待核准通知](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>啟用後可立即使用角色

如果啟用後出現任何延遲，請在啟用之後遵循下列步驟，立即使用您的 Azure 資源角色。

1. 開啟 Azure AD Privileged Identity Management。

1. 按一下 [**我的角色**] 以查看符合資格的 Azure AD 角色和 Azure 資源角色的清單。

1. 按一下 [ **Azure 資源角色**]。

1. 按一下 [**作用中角色**] 索引標籤。

1. 角色啟用後，請登出入口網站並重新登入。

    角色現在應該可供使用。

## <a name="view-the-status-of-your-requests"></a>檢視要求狀態

您可以檢視要啟用的擱置要求狀態。

1. 開啟 Azure AD Privileged Identity Management。

1. 按一下 [**我的要求**] 以查看您的 Azure AD 角色和 Azure 資源角色要求清單。

    ![我的要求-顯示您待決要求的 Azure 資源頁面](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. 捲動至右側可檢視 [要求狀態] 欄。

## <a name="cancel-a-pending-request"></a>取消擱置要求

如果您不需要啟用需要核准的角色，您可以隨時取消擱置要求。

1. 開啟 Azure AD Privileged Identity Management。

1. 按一下 [我的要求]。

1. 針對您想要取消的角色，按一下 [取消] 連結。

    當您按一下 [取消] 時，將會取消要求。 若要再次啟用角色，您必須提交新的啟用要求。

   ![已反白顯示取消動作的我的要求清單](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>疑難排解

### <a name="permissions-are-not-granted-after-activating-a-role"></a>啟用角色之後未授與權限

當您在 PIM 中啟用角色時，啟用可能不會立即傳播至所有需要特殊許可權角色的入口網站。 有時候，即使該變更已傳遞，入口網站中的 Web 快取也可能導致變更無法立即生效。 如果您的啟用延遲，以下是您應該執行的動作。

1. 登出 Azure 入口網站，然後重新登入。

    當您啟動 Azure 資源角色時，您會看到啟用的階段。 當所有階段完成後，您會看到 [登出] 連結。 您可以使用此連結來登出。這將會解決大部分啟用延遲的情況。

1. 在 PIM 中，確認您已列為角色的成員。

## <a name="next-steps"></a>後續步驟

- [在 PIM 中延長或更新 Azure 資源角色](pim-resource-roles-renew-extend.md)
- [在 PIM 中啟用 Azure AD 角色](pim-how-to-activate-role.md)
