---
title: 在 PIM 中核准或拒絕 Azure 資源角色的要求 | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中核准或拒絕 Azure 資源角色的要求。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e5cda31af5ac95e7ebe2b858b1d7355ea3f2a6bb
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189799"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>在 PIM 中核准或拒絕 Azure 資源角色的要求

若要核准或拒絕要求，您必須是核准者清單的成員。 

1. 在 PIM 中，從左側功能表的索引標籤選取 [核准要求]，並找到該要求。

   ![[核准要求] 窗格](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. 選取要求、提供做出此決定的理由，然後選取 [核准] 或 [拒絕]。 接著就會解決該要求。

   ![含有詳細資訊的已選取要求](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>工作流程通知

以下是一些關於工作流程通知的事項：

- 當有角色要求正在等待他們檢閱時，核准者清單中的所有成員都會收到電子郵件通知。 電子郵件通知包含可供核准者核准或拒絕要求的直接連結。
- 清單內第一個核准或拒絕的成員會解決要求。 
- 當核准者回應要求時，核准者清單的所有成員都會得知該動作。 
- 已核准的成員變成有效角色時，資源管理員會收到通知。 

>[!Note]
>當資源系統管理員認為獲得核准的成員不應該成為有效角色時，可在 PIM 中移除作用中角色指派。 雖然資源系統管理員必須是核准者清單的成員才會收到待核准要求的通知，但他們可以透過檢視 PIM 中的待核准要求，來檢視和取消所有使用者的待核准要求。 

## <a name="next-steps"></a>後續步驟

- [在 PIM 中核准或拒絕 Azure AD 目錄角色的要求](azure-ad-pim-approval-workflow.md)
- [PIM 中的電子郵件通知](pim-email-notifications.md)
