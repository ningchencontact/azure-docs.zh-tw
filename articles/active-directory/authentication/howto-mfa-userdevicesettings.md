---
title: Manage users and devices Azure MFA - Azure Active Directory
description: How can administrators change user settings such as forcing the users to do the proof-up process again.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fd03ea807e48f6f0e287bb4497e4d20268995db
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404173"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>管理雲端中 Azure Multi-Factor Authentication 的使用者設定

身為管理員，您可以管理下列使用者和裝置設定：

* 要求使用者再次提供連絡方法
* 刪除應用程式密碼
* 要求在所有受信任的裝置上使用 MFA

## <a name="manage-authentication-methods"></a>Manage authentication methods

As an administrator assigned the Authentication Administrator role you can require users to reset their password, re-register for MFA, or revoke existing MFA sessions from their user object.

![Manage authentication methods from the Azure portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取左邊的 [Azure Active Directory] > [使用者] > [所有使用者]。
1. Choose the user you wish to perform an action on and select **Authentication methods**.
   - **Reset Password** will reset the user's password and assign a temporary password that must be changed on the next sign in.
   - **Require Re-register MFA** will make it so that when the user signs in next time, they will be requested to setup a new MFA authentication method.
   - **Revoke MFA Sessions** clears the user's remembered MFA sessions and requires them to perform MFA the next time it is required by the policy on the device.

## <a name="delete-users-existing-app-passwords"></a>刪除使用者現有的應用程式密碼

此設定會刪除使用者建立的所有應用程式密碼。 與這些應用程式密碼相關的非瀏覽器應用程式會停止運作，直到建立新應用程式密碼為止。 Global administrator permissions are required to perform this action.

### <a name="how-to-delete-users-existing-app-passwords"></a>如何刪除使用者的現有應用程式密碼

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取左邊的 [Azure Active Directory] > [使用者] > [所有使用者]。
3. 選取右邊工具列上的 [Multi-Factor Authentication]。 多重要素驗證頁面隨即開啟。
4. 勾選您想要管理之使用者旁邊的方塊。 A list of quick step options appears on the right.
5. 選取 [管理使用者設定]。
6. 勾選 [刪除選定使用者產生的所有現有應用程式密碼] 方塊。
   ![Delete all existing app passwords](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. 按一下 [儲存]。
8. 按一下 [關閉]。

## <a name="next-steps"></a>後續步驟

- 取得如何[設定 Azure Multi-Factor Authentication 設定](howto-mfa-mfasettings.md)的詳細資訊
- 如果您的使用者需要協助，請將他們指向[雙步驟驗證的使用者指南](../user-help/multi-factor-authentication-end-user.md)
