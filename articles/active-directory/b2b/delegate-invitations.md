---
title: 委派邀請 B2B 共同作業-Azure Active Directory |Microsoft Docs
description: Azure Active Directory B2B 共同作業的使用者屬性可進行設定
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18d40397f30b471699f42878a38c88efebcc6305
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674499"
---
# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>委派 Azure Active Directory B2B 共同作業邀請

使用 Azure Active Directory (Azure AD) 企業對企業 (B2B) 共同作業時，您不需要是全域系統管理員即可傳送邀請。 相反地，您可以使用相關原則並將邀請委派給其角色允許他們傳送邀請的使用者。 委派來賓使用者邀請的一個重要新方式是透過「來賓邀請者」角色。

## <a name="guest-inviter-role"></a>來賓邀請者角色
我們可以將使用者指派到「來賓邀請者」角色以傳送邀請。 您不需要是全域系統管理員角色的成員即可傳送邀請。 根據預設，一般使用者也可叫用邀請 API，除非全域系統管理員已停用一般使用者的邀請功能。 使用者也可以使用 Azure 入口網站或 PowerShell 來叫用 API。

以下範例顯示如何使用 PowerShell 將使用者新增到「來賓邀請者」角色：

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="control-who-can-invite"></a>控制誰可以邀請

在 Azure Active Directory 中，選取 [使用者設定]。 在 [外部使用者] 下，選取 [管理外部共同作業設定]。

> [!NOTE]
> [外部共用作業設定] 也在 [組織關係] 頁面上。 在 Azure Active Directory 中，[管理] 下方，移至 [組織關係]  >  [設定]。

![外部共同作業設定](./media/delegate-invitations/control-who-to-invite.png)

使用 Azure AD B2B 共同作業，租用戶系統管理員可以設定下列邀請原則：

- 關閉邀請
- 只有系統管理員與「來賓邀請者」角色中使用者可以邀請
- 系統管理員、來賓邀請者角色與成員可以邀請
- 所有使用者 (包括來賓) 都可以邀請

根據預設，租用戶會設定為 #4。 (所有使用者 (包括來賓) 都可以邀請 B2B 使用者。)

## <a name="next-steps"></a>後續步驟

請參閱下列有關 Azure AD B2B 共同作業的文章：

- [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
- [在沒有邀請的情況下新增 B2B 共同作業來賓使用者](add-user-without-invite.md)
- [將 B2B 共同作業使用者新增至角色](add-guest-to-role.md)


