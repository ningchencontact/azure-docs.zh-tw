---
title: 啟用我 PIM-Azure Active Directory 中的 Azure 資源角色 |Microsoft Docs
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
ms.date: 06/28/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdce060006f65f2b0fb08023066ee504578bc552
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501656"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>在 PIM 中啟用我的 Azure 資源角色

使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，適用於 Azure 資源的合格角色成員可以排程未來的日期和時間啟動。 他們也可以在最大範圍內選取特定的啟用期間 (由管理員設定)。

本文適用於需要在 PIM 中啟用其 Azure 資源角色的成員。

## <a name="activate-a-role"></a>啟用角色

當您需要擔任某個 Azure 資源角色時，您可以在 PIM 中使用 [我的角色]  導覽選項來要求啟用。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟 **Azure AD Privileged Identity Management**。 如需如何將 [PIM] 圖格新增至儀表板的資訊，請參閱[開始使用 PIM](pim-getting-started.md)。

1. 按一下 [我的角色]  。

    ![您可以啟用我顯示角色的角色頁面](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. 按一下  **Azure 資源角色**以查看您的合格的 Azure 資源角色的清單。

   ![我的角色-Azure 資源角色 頁面](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. 在 [Azure 資源角色]  清單中，尋找您要啟用的角色。

    ![Azure 資源角色-我的合格角色清單](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. 按一下 [啟用]  以開啟 [啟用] 窗格。

1. 如果您的角色需要多重要素驗證 (MFA)，請按一下 [先驗證您的身分識別後再繼續]  。 您只需在每個工作階段驗證一次。

    ![我使用 MFA 的身分啟用角色之前先確認](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. 按一下 [驗證我的身分識別]  ，並遵循指示來提供其他安全性驗證。

    ![提供例如 PIN 碼的安全性驗證 畫面](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 如果您想要指定縮小的範圍，請按一下 [範圍]  開啟 [資源] 篩選窗格。

    最佳做法是僅要求存取您需要的資源。 在 [資源] 篩選窗格中，您可以指定資源群組或您需要存取的群組。

    ![啟用-指定範圍的資源篩選窗格](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. 如有必要，請指定自訂啟用開始時間。 成員會在選取的時間後啟用。

1. 在 [原因]  方塊中輸入此啟用要求的原因。

    ![已完成的啟動窗格範圍、 開始時間、 持續時間和原因](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. 按一下 **[啟用]** 。

    如果角色不需核准，就會啟動並新增至使用中角色的清單。 如果您想要使用的角色，請遵循下一節中的步驟。

    如果[角色需要核准](pim-resource-roles-approval-workflow.md)才能啟用，通知會出現在瀏覽器右上角，通知您要求正在等待核准。

    ![啟用要求正在等待核准通知](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>啟用後可立即使用角色

如果在啟用後的任何延遲，請遵循下列步驟之後您啟用即可立即使用您的 Azure 資源角色。

1. 開啟 Azure AD Privileged Identity Management。

1. 按一下 **我的角色**若要查看您符合資格的 Azure AD 角色和 Azure 資源角色。

1. 按一下  **Azure 資源角色**。

1. 按一下 [**作用中的角色**] 索引標籤。

1. 作用中的角色之後，請登出入口網站，再重新登入。

    角色現在應該可供使用。

## <a name="view-the-status-of-your-requests"></a>檢視要求狀態

您可以檢視要啟用的擱置要求狀態。

1. 開啟 Azure AD Privileged Identity Management。

1. 按一下 **我的要求**若要查看您的 Azure AD 角色和 Azure 資源角色的要求。

    ![我的要求-Azure 資源 畫面，顯示暫止要求](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. 捲動至右側可檢視 [要求狀態]  欄。

## <a name="cancel-a-pending-request"></a>取消擱置要求

如果您不需要啟用需要核准的角色，您可以隨時取消擱置要求。

1. 開啟 Azure AD Privileged Identity Management。

1. 按一下 [我的要求]  。

1. 針對您想要取消的角色，按一下 [取消]  連結。

    當您按一下 [取消] 時，將會取消要求。 若要再次啟用角色，您必須提交新的啟用要求。

   ![我的要求清單，並反白顯示的 [取消] 動作](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>疑難排解

### <a name="permissions-are-not-granted-after-activating-a-role"></a>啟用角色之後不授與權限

當您啟用 PIM 中的角色時，啟動可能無法立即傳遞到需要特殊權限的角色的所有入口網站。 有時候，即使該變更會傳播，web 入口網站中的快取可能會導致不立即生效的變更。 如果您啟用已延遲，以下是該怎麼辦。

1. Azure 入口網站登出，然後重新登入。

    當您啟用的 Azure 資源角色時，您會看到您的啟用階段。 所有階段完成後，您會看到**登出**連結。 若要登出，您可以使用此連結。這將會解決大部分的情況下，啟用延遲。

1. 在 PIM，確認您已列為角色的成員。

## <a name="next-steps"></a>後續步驟

- [在 PIM 中延長或更新 Azure 資源角色](pim-resource-roles-renew-extend.md)
- [啟用我在 PIM 中的 Azure AD 角色](pim-how-to-activate-role.md)
