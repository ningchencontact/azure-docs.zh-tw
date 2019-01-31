---
title: 使用 PIM 的授權需求 - Azure | Microsoft Docs
description: 說明要使用 Azure AD Privileged Identity Management (PIM) 的授權需求。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/16/2019
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: ad64fba0288aa5663e008484d6f0d2cbdff0bca6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187278"
---
# <a name="license-requirements-to-use-pim"></a>使用 PIM 的授權需求

若要使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，目錄必須具有有效的授權。 此外，也必須指派授權給管理員和相關的使用者。 本文說明使用 PIM 的授權需求。

## <a name="prerequisites"></a>必要條件

若要使用 PIM，目錄必須具有下列其中一個付費或試用授權：

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

如需詳細資訊，請參閱[什麼是 Azure Active Directory？](../fundamentals/active-directory-whatis.md)。

## <a name="which-users-must-have-licenses"></a>哪些使用者必須有授權？

會與 PIM 互動或可從 PIM 獲益的每個管理員或使用者都必須具有授權。 範例包括：

- 有使用 PIM 所管理 Azure AD 角色的管理員
- 有使用 PIM 所管理 Azure 資源角色的管理員
- 指派給「特殊權限角色管理員」角色的管理員
- 指派為有資格成為目錄角色 (可使用 PIM 來管理) 的使用者
- 能夠在 PIM 中核准/拒絕要求的使用者
- 指派給 Azure 資源角色 (透過及時或直接 (以時間為基礎) 的方式指派) 的使用者  
- 指派給存取權檢閱的使用者
- 執行存取權檢閱的使用者

如需如何指派授權給使用者的相關資訊，請參閱[使用 Azure Active Directory 入口網站指派或移除授權](../fundamentals/license-users-groups.md)。

## <a name="what-happens-when-a-license-expires"></a>授權過期會發生什麼狀況？

如果 Azure AD Premium P2、EMS E5 或試用版授權過期，目錄中就無法再使用 PIM 功能：

- Azure AD 角色的永久角色指派不會受到影響。
- 使用者無法再利用 Azure 入口網站中的 PIM 服務以及 PIM 的 Graph API Cmdlet 和 PowerShell 介面，來啟動特殊權限的角色、管理特殊權限的存取權，或執行特殊權限角色的存取檢閱。
- Azure AD 角色的合格角色指派將會遭到移除，因為使用者將無法再啟動特殊權限的角色。
- Azure AD 角色的任何進行中存取檢閱將會結束，而且 PIM 組態設定將會遭到移除。
- PIM 將不再傳送有關角色指派變更的電子郵件。

## <a name="next-steps"></a>後續步驟

- [開始使用 PIM](pim-getting-started.md)
- [您無法在 PIM 中管理的角色](pim-roles.md)
