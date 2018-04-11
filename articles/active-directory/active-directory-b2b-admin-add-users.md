---
title: 在 Azure 入口網站中新增 B2B 共同作業使用者 - Azure Active Directory | Microsoft Docs
description: 說明系統管理員如何使用 Azure Active Directory (Azure AD) B2B 共同作業，將來自夥伴組織的來賓使用者新增到其目錄。
services: active-directory
documentationcenter: ''
author: twooley
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/02/2018
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 34bd5b51089045c4cd20f29d179bb230e5e3fac2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>在 Azure 入口網站中新增 Azure Active Directory B2B 共同作業使用者

身為全域管理員或是獲派任何受限管理員目錄角色的使用者，您可以使用 Azure 入口網站來邀請 B2B 共同作業使用者。 您可以將來賓使用者邀請到目錄、群組或應用程式。 透過上述任一方法邀請使用者之後，受邀使用者的帳戶就會新增至 Azure Active Directory (Azure AD)，且使用者類型為「來賓」。 來賓使用者接著必須兌換其邀請才能存取資源。

## <a name="add-guest-users-to-the-directory"></a>將來賓使用者新增到目錄

若要將 B2B 共同作業使用者新增至目錄，請遵循下列步驟：

1. 以 Azure AD 系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 在導覽窗格中，選取 [Azure Active Directory]。
3. 在 [管理] 下選取 [使用者和群組] > [所有使用者]。
4. 選取 [新增來賓使用者]。

   ![顯示 [新增來賓使用者] 在 UI 中的位置](./media/active-directory-b2b-admin-add-users/NewGuestUser-Directory.png) 
 
7. 在 [邀請來賓] 下，輸入外部使用者的電子郵件地址。 您也可以選擇納入歡迎訊息。 例如︰

   ![顯示 [新增來賓使用者] 在 UI 中的位置](./media/active-directory-b2b-admin-add-users/InviteGuest.png) 

8. 選取 [邀請] 即可自動對來賓使用者傳送邀請。 在 [通知] 區域中尋找**已成功邀請使用者**的訊息。 
 
在傳送邀請之後，系統就會自動將使用者帳戶以來賓身分新增至目錄。


![顯示 B2B 使用者與「來賓」使用者類型](./media/active-directory-b2b-admin-add-users/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>將來賓使用者新增到群組
如果您需要透過手動方式，以 Azure AD 系統管理員身分將 B2B 共同作業使用者新增到群組，請遵循下列步驟：

1. 以 Azure AD 系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 在導覽窗格中，選取 [Azure Active Directory]。
3. 在 [管理] 下選取 [使用者和群組] > [所有群組]。
4. 選取群組 (或按一下 [新增群組] 以建立新的群組)。 您不妨納入群組描述，說明該群組包含 B2B 來賓使用者。
5. 選取 [成員] > [新增成員]。 
6. 執行下列其中一項：
   - 如果目錄中已有來賓使用者，請搜尋 B2B 使用者。 選取使用者 > 按一下 [選取] 將使用者新增至群組。
   - 如果目錄中還沒有來賓使用者，請選取 [邀請]。
   ![用來新增來賓成員的新增邀請按鈕](./media/active-directory-b2b-admin-add-users/GroupInvite.png)
   
      在 [邀請來賓] 下輸入電子郵件地址，並選擇是否輸入個人訊息 > 選取 [邀請]。 按一下 [選取] 將使用者新增至群組。

      邀請會自動傳送給受邀的使用者。 在 [通知] 區域中尋找**已邀請使用者**的成功訊息。 

您也可以搭配使用動態群組與 Azure AD B2B 共同作業。 如需詳細資訊，請參閱[動態群組與 Azure Active Directory B2B 共同作業](active-directory-b2b-dynamic-groups.md)。

## <a name="add-guest-users-to-an-application"></a>將來賓使用者新增到應用程式

若要以 Azure AD 系統管理員身分將 B2B 共同作業使用者新增到應用程式，請遵循下列步驟：

1. 以 Azure AD 系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 在導覽窗格中，選取 [Azure Active Directory]。
3. 在 [管理] 下選取 [企業應用程式] > [所有應用程式]。
4. 選取要對其新增來賓使用者的應用程式。
5. 在 [管理] 下選取 [使用者和群組]。
6. 選取 [新增使用者]。
7. 在 [新增指派] 下，選取 [使用者和群組]。
8. 執行下列其中一項：
   - 如果目錄中已有來賓使用者，請搜尋 B2B 使用者。 選取使用者，然後按一下 [選取] 將使用者新增至應用程式。
   - 如果目錄中還沒有來賓使用者，請選取 [邀請]。
   ![用來新增來賓成員的新增邀請按鈕](./media/active-directory-b2b-admin-add-users/AppInviteUsers.png)
   
      在 [邀請來賓] 下輸入電子郵件地址，並選擇是否輸入個人訊息 > 選取 [邀請]。 按一下 [選取] 將使用者新增至應用程式。

      邀請會自動傳送給受邀的使用者。 在 [通知] 區域中尋找**已邀請使用者**的成功訊息。

9. 在 [新增指派] 下，按一下 [選取角色] > 選取要套用至所選使用者的角色 (如適用) > 選取 [確定]。
10. 按一下 [指派]。
 
## <a name="resend-invitations-to-guest-users"></a>將邀請重新傳送給來賓使用者

如果來賓使用者尚未兌換其邀請，您可以重新傳送邀請。

1. 以 Azure AD 系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 在導覽窗格中，選取 [Azure Active Directory]。
3. 在 [管理] 下選取 [使用者和群組]。
4. 選取 [所有使用者]。
5. 選取使用者帳戶。
6. 在 [管理] 底下選取 [設定檔]。
7. 如果使用者尚未接受邀請，則 [重新傳送邀請] 選項會是可供使用的狀態。 選取此按鈕即可重新傳送。

   ![使用者設定檔中的 [重新傳送邀請] 選項](./media/active-directory-b2b-admin-add-users/Resend-Invitation.png)

> [!NOTE]
> 如果您重新傳送的邀請原本會將使用者導向特定應用程式，請務必了解，新邀請中的連結會改為將使用者導向最上層的存取面板。

## <a name="next-steps"></a>後續步驟

- 若要了解非 Azure AD 系統管理員要如何新增 B2B 來賓使用者，請參閱[資訊工作者如何新增 B2B 共同作業使用者？](active-directory-b2b-iw-add-users.md)
- 如需關於邀請電子郵件的資訊，請參閱[B2B 共同作業邀請電子郵件的元素](active-directory-b2b-invitation-email.md)。
- 如需關於邀請兌換程序的資訊，請參閱[B2B 共同作業邀請兌換](active-directory-b2b-redemption-experience.md)。


