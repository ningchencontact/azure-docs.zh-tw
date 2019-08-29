---
title: 如何在 Azure Active Directory Identity Protection 中設定多重要素驗證註冊原則 | Microsoft Docs
description: 了解如何設定 Azure AD Identity Protection 多重要素驗證註冊原則。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 939f08fd16cf27e641cf6436a00396ad2db8e6c3
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126381"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>如何：設定 Azure 多重要素驗證註冊原則

Azure AD Identity Protection 藉由設定條件式存取原則來要求進行 MFA 註冊, 而不論您登入的新式驗證應用程式為何, 都能協助您管理「多重要素驗證」 (MFA) 註冊的推出。 本文說明原則可用於哪些內容, 以及如何進行設定。



## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>什麼是 Azure 多重要素驗證註冊原則？

Azure 多因素驗證提供一種方法來驗證您使用的不只是使用者名稱和密碼。 它提供使用者登入的第二層安全性。為了讓使用者能夠回應 MFA 提示, 他們必須先註冊 Azure 多重要素驗證。

我們建議您針對使用者登入要求 Azure 多重要素驗證, 因為它:

- 提供增強式驗證與一系列簡單的驗證選項
- 在準備您的組織保護和復原 Identity Protection 中的風險偵測時, 扮演著重要的角色

如需 MFA 的詳細資訊, 請參閱[什麼是 Azure 多重要素驗證？](../authentication/howto-mfa-getstarted.md)

## <a name="how-do-i-access-the-registration-policy"></a>如何? 存取註冊原則？

MFA 註冊原則位於 [Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy) 頁面的 [設定] 區段中。

![MFA 原則](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>原則設定

當您設定 MFA 註冊原則時, 您需要進行下列設定變更:

- 套用原則的使用者和群組。 請記得排除貴組織的[緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)。

    ![使用者和群組](./media/howto-mfa-policy/11.png)

- 您想要強制執行的控制項-**需要 AZURE MFA 註冊**

    ![存取權](./media/howto-mfa-policy/12.png)

- [強制執行原則] 應該設定為 [**開啟**]。

    ![強制執行原則](./media/howto-mfa-policy/14.png)

- **儲存**原則

## <a name="user-experience"></a>使用者體驗

Azure Active Directory Identity Protection 將會提示使用者在下一次以互動方式登入時進行註冊。

如需相關的使用者經驗概觀，請參閱︰

- [Multi-Factor Authentication 註冊流程](flows.md#multi-factor-authentication-registration)。  
- [使用 Azure AD Identity Protection 時的登入體驗](flows.md)。  

## <a name="next-steps"></a>後續步驟

若要取得 Azure AD Identity Protection 的概觀，請參閱 [Azure AD Identity Protection 概觀](overview.md)。
