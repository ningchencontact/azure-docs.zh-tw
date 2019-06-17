---
title: 如何在 Azure Active Directory Identity Protection 中設定多重要素驗證註冊原則 | Microsoft Docs
description: 了解如何設定 Azure AD Identity Protection 多重要素驗證註冊原則。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 434d07163713a139b42a5dbe1664f81dafc2a1ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108948"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>如何：設定 Azure Multi-factor Authentication 註冊原則

Azure AD Identity Protection 可協助您藉由設定條件式存取原則來要求 MFA 註冊，無論您登入哪些應用程式管理導入的 multi-factor authentication (MFA) 註冊。 這篇文章會說明哪些原則可用於和如何加以設定。

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>什麼是 Azure Multi-factor Authentication 註冊原則？

Azure Multi-factor Authentication 提供方法來確認您使用多個只是使用者名稱和密碼。 它提供第二個使用者登入的安全性層級。為了讓使用者能夠回應 MFA 提示，他們必須先註冊 Azure Multi-factor authentication。

我們建議您針對使用者登入需要 Azure Multi-factor Authentication，因為它：

- 提供增強式驗證與一系列簡單的驗證選項
- 扮演關鍵性的角色，以準備您的組織，保護及復原從 Identity Protection 中的風險事件

如需 MFA 的詳細資訊，請參閱[什麼是 Azure Multi-factor Authentication？](../authentication/howto-mfa-getstarted.md)

## <a name="how-do-i-access-the-registration-policy"></a>如何存取登錄原則？

MFA 註冊原則位於 [Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy) 頁面的 [設定]  區段中。

![MFA 原則](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>原則設定

當您設定 MFA 註冊原則時，您需要進行下列組態變更：

- 使用者和群組要套用原則。 排除您的組織，請記得[緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)。

    ![使用者和群組](./media/howto-mfa-policy/11.png)

- 您想要強制執行-控制項**需要 Azure MFA 註冊**

    ![Access](./media/howto-mfa-policy/12.png)

- 強制執行原則應該設為**上**。

    ![強制執行原則](./media/howto-mfa-policy/14.png)

- **儲存**您的原則

## <a name="user-experience"></a>使用者體驗

如需相關的使用者經驗概觀，請參閱︰

- [Multi-Factor Authentication 註冊流程](flows.md#multi-factor-authentication-registration)。  
- [使用 Azure AD Identity Protection 時的登入體驗](flows.md)。  

## <a name="next-steps"></a>後續步驟

若要取得 Azure AD Identity Protection 的概觀，請參閱 [Azure AD Identity Protection 概觀](overview.md)。
