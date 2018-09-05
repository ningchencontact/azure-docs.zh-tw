---
title: 在 PIM 中核准或拒絕 Azure AD 目錄角色的要求 | Microsoft Docs
description: 如何在 Azure AD Privileged Identity Management (PIM) 中核准或拒絕 Azure AD 目錄角色的要求。
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
ms.component: pim
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7bf1e437e97fdb4d929af23bd7b2a9abb49268df
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189153"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>在 PIM 中核准或拒絕 Azure AD 目錄角色的要求

透過 Privileged Identity Management，您可以將角色設定為需要核准才能啟用，並選擇一或多個使用者或群組作為委派核准者。

## <a name="view-pending-approvals-requests"></a>檢視待決的核准 (要求)

身為委派核准者，當要求正在等待您的核准時，您將會收到電子郵件通知。 若要在 PIM 入口網站中檢視這些要求，可從儀表板 (在新的導覽中) 選取左側導覽列中的 [等待核准要求] 索引標籤。

![](media/azure-ad-pim-approval-workflow/image023.png)

您將在該處看到一份等待核准的要求清單：

![](media/azure-ad-pim-approval-workflow/image024.png)

## <a name="approve-or-deny-requests-for-role-elevation-single-andor-bulk"></a>核准或拒絕提高角色權限 (單一和/或大量) 的要求

選取您想要核准或拒絕的要求，然後按一下與您的決策相對應之動作列上的按鈕：

![](media/azure-ad-pim-approval-workflow/image025.png)

## <a name="provide-justification-for-my-approvaldenial"></a>提供我的核准/拒絕理由

這將開啟新的刀鋒視窗，一次核准或拒絕多個要求。 輸入您的決策理由，然後按一下底端或刀鋒視窗中的 [核准] \(或 [拒絕])：

![](media/azure-ad-pim-approval-workflow/image029.png)

當要求程序完成時，狀態符號將會反映您所做的決策 (在此範例中，決策是核准)：

![](media/azure-ad-pim-approval-workflow/image031.png)

## <a name="next-steps"></a>後續步驟

- [在 PIM 中核准或拒絕 Azure 資源角色的要求](pim-resource-roles-approval-workflow.md)
- [PIM 中的電子郵件通知](pim-email-notifications.md)
