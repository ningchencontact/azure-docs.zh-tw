---
title: 以來賓使用者的身分離開組織 - Azure Active Directory | Microsoft Docs
description: 說明 Azure AD B2B 來賓使用者如何使用存取面板離開組織。
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 942439da3e116415c77a28950df69d44744b2dd8
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077703"
---
# <a name="leave-an-organization-as-a-guest-user"></a>以來賓使用者的身分離開組織

Azure Active Directory (Azure AD) B2B 使用者如果不再需要組織的應用程式，或不需要與組織保持關聯，隨時皆可決定離開組織。 使用者可以自行離開組織，不需要聯絡系統管理員。

## <a name="leave-an-organization"></a>離開組織

若要離開組織，請以使用者身分登入 [存取面板][](https://myapps.microsoft.com)，接著執行下列動作：

1. 如果您尚未登入打算離開的組織，請在右上角選取您的名稱，然後按一下要離開的組織。
2. 在右上角選取您的名稱。
3. 選取 [組織] 旁邊的設定圖示 (齒輪)。
 
   ![顯示 [存取面板] 中的使用者設定的螢幕截圖](media/active-directory-b2b-leave-the-organization/UserSettings.png) 

3. 在 [組織] 下方找到要離開的組織，然後選取 [離開組織]。

   ![顯示使用者介面中的 [離開組織] 選項的螢幕截圖](media/active-directory-b2b-leave-the-organization/LeaveOrg.png)

4. 要求確認時，請選取 [離開]。 

## <a name="account-removal"></a>帳戶移除

使用者離開組織後，其帳戶將在目錄中「虛刪除」。 預設情況下，使用者物件將移至 Azure AD 中的 [刪除的使用者] 區域，但在 30 天內不會永久刪除。 透過虛刪除的作法，若使用者在 30 天內提出要求，系統管理員便可還原使用者帳戶 (包括群組和權限)。

如有需要，租用戶系統管理員在 30 天內隨時可永久刪除帳戶。 作法：

1. 在 [Azure 入口網站][](https://portal.azure.com)中，選取 [Azure Active Directory]。
2. 在 [管理] 底下選取 [使用者]。
3. 選取 [刪除的使用者]。
4. 選取已刪除使用者旁邊的核取方塊，然後選取 [永久刪除]。

若永久刪除使用者，此動作將無法回復。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>後續步驟

- 如需 Azure AD B2B 的概觀，請參閱[何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)



