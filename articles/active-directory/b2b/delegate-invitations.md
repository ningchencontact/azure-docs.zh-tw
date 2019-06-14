---
title: 啟用 B2B 外部共同作業設定 Azure Active Directory |Microsoft Docs
description: 了解如何啟用 Active Directory B2B 外部共同作業和管理誰可以邀請來賓使用者。 若要將邀請委派中使用來賓邀請者角色。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11dda7fc3760f468c094fb4cf4484a27895f83b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65812678"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>啟用 B2B 外部共同作業和管理誰可以邀請來賓

本文說明如何啟用 Azure Active Directory (Azure AD) B2B 共同作業，並且決定誰可以邀請來賓。 根據預設，所有使用者和您的目錄中的來賓可都邀請來賓，即使它們不指派給系統管理員角色。 外部共同作業設定可讓您將在您的組織中的不同類型的使用者開啟或關閉的來賓邀請。 您也可以藉由指派角色，讓他們能夠邀請來賓委派給個別使用者的邀請。

## <a name="configure-b2b-external-collaboration-settings"></a>設定 B2B 外部共同作業設定

使用 Azure AD B2B 共同作業，租用戶系統管理員可以設定下列邀請原則：

- 關閉邀請
- 只有系統管理員與「來賓邀請者」角色中使用者可以邀請
- 系統管理員、來賓邀請者角色與成員可以邀請
- 所有使用者 (包括來賓) 都可以邀請

根據預設，所有的使用者，包括來賓可邀請來賓使用者。

### <a name="to-configure-external-collaboration-settings"></a>若要設定外部共同作業設定：

1. 登入[Azure 入口網站](https://portal.azure.com)身為租用戶系統管理員。
2. 選取 [Azure Active Directory]   > [使用者]   > [使用者設定]  。
3. 在 [外部使用者]  下，選取 [管理外部共同作業設定]  。
   > [!NOTE]
   > [外部共用作業設定]  也在 [組織關係]  頁面上。 在 Azure Active Directory 中，[管理]  下方，移至 [組織關係]   >  [設定]  。
4. 在 **外部共同作業設定**頁面上，選擇您想要啟用的原則。

   ![外部共同作業設定](./media/delegate-invitations/control-who-to-invite.png)

  - **來賓使用者權限只限於**:此原則會決定您的目錄中的來賓權限。 選取 **是**來封鎖從特定的目錄工作，例如列舉使用者、 群組或其他目錄資源的來賓。 選取  **No**讓來賓相同存取目錄資料以一般使用者在目錄中。
   - **系統管理員 」 與 「 來賓邀請者角色中的使用者可以邀請**:若要讓系統管理員和使用者角色中的 來賓邀請者 」 邀請來賓，將此原則設定為**是**。
   - **成員可邀請**:若要允許非系統管理員的您的目錄中的成員來邀請來賓，請將此原則設定為**是**。
   - **來賓可邀請**:若要允許來賓邀請其他來賓，請將此原則設定為**是**。
   - **啟用來賓 （預覽） 的電子郵件的單次密碼**:如需詳細的單次密碼功能的詳細資訊，請參閱[電子郵件 （預覽） 的單次密碼驗證](one-time-passcode.md)。
   - **共同作業限制**:如需允許或封鎖對特定網域的邀請的詳細資訊，請參閱[允許或封鎖邀請 B2B 使用者從特定組織](allow-deny-list.md)。

## <a name="assign-the-guest-inviter-role-to-a-user"></a>來賓邀請者角色指派給使用者

使用來賓邀請者 」 角色時，您可以讓個別使用者邀請來賓，而不將其指派的全域管理員或其他系統管理員角色的能力。 將來賓邀請者角色指派給個人。 請確定您設定**系統管理員 」 與 「 來賓邀請者角色中的使用者可以邀請**要**是**。

以下範例顯示如何使用 PowerShell 將使用者新增到「來賓邀請者」角色：

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>後續步驟

請參閱下列有關 Azure AD B2B 共同作業的文章：

- [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
- [在沒有邀請的情況下新增 B2B 共同作業來賓使用者](add-user-without-invite.md)
- [將 B2B 共同作業使用者新增至角色](add-guest-to-role.md)


