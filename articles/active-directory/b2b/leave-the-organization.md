---
title: 以來賓使用者的身分從組織離職 - Azure Active Directory | Microsoft Docs
description: 說明 Azure AD B2B 來賓使用者如何使用 [存取面板] 從組織離職。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f75f91c037a2f05c999d388ce7bb16ad2d0c9cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60412642"
---
# <a name="leave-an-organization-as-a-guest-user"></a>以來賓使用者的身分從組織離職

Azure Active Directory (Azure AD) B2B 使用者如果不再需要組織的應用程式，或不需要與組織保持關聯，隨時皆可決定從組織離職。 使用者可以自行從組織離職，不需要聯絡系統管理員。

## <a name="leave-an-organization"></a>從組織離職

若要離開組織，請遵循下列步驟。

1. 請移至您的存取面板設定檔頁面，執行下列其中一項：
   
   - 在  [Azure 入口網站](https://portal.azure.com)，按一下您右上方的名稱，然後選取**檢視帳戶**。
   - 開啟您[存取面板](https://myapps.microsoft.com)，按一下您的名稱，右和下一步 的左上方**組織**，選取 [設定] 圖示 （齒輪）。
 
   ![顯示 [存取面板] 中使用者設定的螢幕擷取畫面](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > 如果您在尚未登入，組織您想要離開，底下**組織**，按一下**登入以離開組織**組織的名稱旁邊的連結。 您已登入之後，按一下您的名稱，一次在右上方，接下來**組織**，選取 [設定] 圖示 （齒輪）。

3. 在 [組織] 下方找到要離開的組織，然後選取 [從組織離職]。

   ![顯示使用者介面中 [從組織離職] 選項的螢幕擷取畫面](media/leave-the-organization/LeaveOrg.png)

4. 系統要求您確認時，請選取 [離開]。 

## <a name="account-removal"></a>帳戶移除

使用者從組織離職後，其帳戶將在目錄中「虛刪除」。 預設情況下，使用者物件將移至 Azure AD 中的 [刪除的使用者] 區域，但在 30 天內不會永久刪除。 透過虛刪除的作法，若使用者在 30 天內提出要求，系統管理員便可還原使用者帳戶 (包括群組和權限)。

如有需要，租用戶系統管理員在 30 天內隨時可永久刪除帳戶。 作法：

1. 在 [[Azure 入口網站]](https://portal.azure.com) 中，選取 **[Azure Active Directory]**。
2. 在 [管理] 底下選取 [使用者]。
3. 選取 [刪除的使用者]。
4. 選取已刪除使用者旁邊的核取方塊，然後選取 [永久刪除]。

若永久刪除使用者，此動作將無法回復。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>後續步驟

- 如需 Azure AD B2B 的概觀，請參閱[何謂 Azure AD B2B 共同作業？](what-is-b2b.md)



