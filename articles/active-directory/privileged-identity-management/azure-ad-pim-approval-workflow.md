---
title: 在 PIM 中核准或拒絕 Azure AD 角色的要求-Azure Active Directory |Microsoft Docs
description: 瞭解如何在 Azure AD Privileged Identity Management （PIM）中核准或拒絕 Azure AD 角色的要求。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3245f7343a48d3e54795c14dcb23b836c8d9d988
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756435"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>核准或拒絕 Privileged Identity Management 中 Azure AD 角色的要求

使用 Azure Active Directory （Azure AD） Privileged Identity Management （PIM），您可以將角色設定為需要核准才能啟用，並選擇一或多個使用者或群組作為委派的核准者。 委派核准者會有 24 小時的時間來核准要求。 如果未在 24 小時內核准要求，符合資格的使用者就必須重新提交新要求。 24 小時核准時間範圍是不可設定的。

請遵循這篇文章中的步驟，核准或拒絕 Azure AD 角色的要求。

## <a name="view-pending-requests"></a>檢視擱置的要求

身為委派的核准者，當 Azure AD 的角色要求等待您的核准時，您會收到電子郵件通知。 您可以在 Privileged Identity Management 中查看這些擱置中的要求。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure AD 角色]。

1. 按一下 [核准要求]。

    ![Azure AD 角色-核准要求](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    您將看到一份等待您核准的要求清單。

## <a name="approve-requests"></a>核准要求

1. 選取您要核准的要求，然後按一下 [核准]，以開啟 [核准選取要求] 窗格。

    ![已反白顯示核准選項的核准要求清單](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. 在 [核准原因] 方塊中輸入原因。

    ![核准選取的要求窗格與核准原因](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. 按一下 [核准]。

    狀態符號會隨著您的核准而更新。

    ![按一下 [核准] 按鈕之後核准選取的要求窗格](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>拒絕要求

1. 選取您要拒絕的要求，然後按一下 [拒絕]，以開啟 [拒絕選取要求] 窗格。

    ![已反白顯示拒絕選項的核准要求清單](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. 在 [拒絕原因] 方塊中輸入原因。

    ![拒絕所選要求窗格的拒絕原因](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. 按一下 [拒絕]。

    狀態符號會隨著您的拒絕而更新。

## <a name="next-steps"></a>後續步驟

- [Privileged Identity Management 中的電子郵件通知](pim-email-notifications.md)
- [在 Privileged Identity Management 中核准或拒絕 Azure 資源角色的要求](pim-resource-roles-approval-workflow.md)
