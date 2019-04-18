---
title: 核准或拒絕要求的 Azure AD PIM-Azure Active Directory 中的角色 |Microsoft Docs
description: 了解如何核准或拒絕要求，在 Azure AD Privileged Identity Management (PIM) 中的 Azure AD 角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47e9033b33c70a72a1685696f75a442e88eed033
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59493564"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-pim"></a>核准或拒絕要求的 Azure AD PIM 中的角色

使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，您可以將角色設定為需要核准才能啟用，並選擇一或多個使用者或群組做為委派核准者。 委派核准者會有 24 小時的時間來核准要求。 如果未在 24 小時內核准要求，符合資格的使用者就必須重新提交新要求。 24 小時核准時間範圍是不可設定的。

請遵循這篇文章，核准或拒絕要求的 Azure AD 角色中的步驟。

## <a name="view-pending-requests"></a>檢視擱置的要求

身為委派核准者，您會在 Azure AD 角色要求正在等待您核准時收到電子郵件通知。 您可以在 PIM 中檢視這些擱置的要求。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure AD 角色]。

1. 按一下 [核准要求]。

    ![PIM 的 Azure AD 角色的角色](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    您將看到一份等待您核准的要求清單。

## <a name="approve-requests"></a>核准要求

1. 選取您要核准的要求，然後按一下 [核准]，以開啟 [核准選取要求] 窗格。

    ![PIM 核准要求清單](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. 在 [核准原因] 方塊中輸入原因。

    ![PIM 核准所選要求](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. 按一下 [核准]。

    狀態符號會隨著您的核准而更新。

    ![PIM 核准所選要求](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>拒絕要求

1. 選取您要拒絕的要求，然後按一下 [拒絕]，以開啟 [拒絕選取要求] 窗格。

    ![PIM 核准要求清單](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. 在 [拒絕原因] 方塊中輸入原因。

    ![PIM 拒絕所選要求](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. 按一下 [拒絕]。

    狀態符號會隨著您的拒絕而更新。

## <a name="next-steps"></a>後續步驟

- [PIM 中的電子郵件通知](pim-email-notifications.md)
- [在 PIM 中核准或拒絕 Azure 資源角色的要求](pim-resource-roles-approval-workflow.md)
