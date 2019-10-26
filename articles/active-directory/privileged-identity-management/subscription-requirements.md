---
title: 使用 Privileged Identity Management Azure Active Directory 的授權需求 |Microsoft Docs
description: 說明要使用 Azure AD Privileged Identity Management (PIM) 的授權需求。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6e336ec40ba2b9f6d3018e6a4f5b2ac721077aa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895124"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>使用 Privileged Identity Management 的授權需求

若要使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，目錄必須具有有效的授權。 此外，也必須指派授權給管理員和相關的使用者。 本文說明使用 Privileged Identity Management 的授權需求。

## <a name="prerequisites"></a>必要條件

若要使用 Privileged Identity Management，您的目錄必須具有下列其中一個付費或試用版授權：

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

如需詳細資訊，請參閱[什麼是 Azure Active Directory？](../fundamentals/active-directory-whatis.md)。

## <a name="which-users-must-have-licenses"></a>哪些使用者必須有授權？

與 Privileged Identity Management 互動或獲得權益的每位系統管理員或使用者都必須擁有授權。 例如：

- 有使用 PIM 所管理 Azure AD 角色的管理員
- 有使用 PIM 所管理 Azure 資源角色的管理員
- 指派給「特殊權限角色管理員」角色的管理員
- 指派為符合使用 PIM 管理之 Azure AD 角色資格的使用者
- 能夠在 PIM 中核准/拒絕要求的使用者
- 指派給 Azure 資源角色 (透過及時或直接 (以時間為基礎) 的方式指派) 的使用者  
- 指派給存取權檢閱的使用者
- 執行存取權檢閱的使用者

如需如何指派授權給使用者的相關資訊，請參閱[使用 Azure Active Directory 入口網站指派或移除授權](../fundamentals/license-users-groups.md)。

## <a name="what-happens-when-a-license-expires"></a>授權過期會發生什麼狀況？

如果 Azure AD Premium P2、EMS E5 或試用版授權過期，Privileged Identity Management 功能將無法在您的目錄中使用：

- Azure AD 角色的永久角色指派不會受到影響。
- Azure 入口網站中的 Privileged Identity Management 服務，以及 Privileged Identity Management 的圖形 API Cmdlet 和 PowerShell 介面，將無法再供使用者啟用特殊許可權角色、管理特殊許可權存取，或執行存取特殊許可權角色的審查。
- Azure AD 角色的合格角色指派將會遭到移除，因為使用者將無法再啟動特殊權限的角色。
- Azure AD 角色的任何進行中存取檢查將會結束，且 Privileged Identity Management 設定將會移除。
- Privileged Identity Management 將不會再傳送有關角色指派變更的電子郵件。

## <a name="next-steps"></a>後續步驟

- [部署 Privileged Identity Management](pim-deployment-plan.md)
- [開始使用 Privileged Identity Management](pim-getting-started.md)
- [您無法在 Privileged Identity Management 中管理的角色](pim-roles.md)
