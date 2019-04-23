---
title: 如何在 Azure Active Directory Identity Protection 中設定多重要素驗證註冊原則 | Microsoft Docs
description: 了解如何設定 Azure AD Identity Protection 多重要素驗證註冊原則。
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, 管理應用程式, 安全性, 風險, 風險層級, 弱點, 安全性原則
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: abd1049551d7dbc4823636dfdc00f64afab72cdf
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60008591"
---
# <a name="how-to-configure-the-multi-factor-authentication-registration-policy"></a>作法：設定多重要素驗證註冊原則

Azure AD Identity Protection 可協助您藉由設定要求 MFA 註冊原則管理導入的 multi-factor authentication (MFA) 註冊。 這篇文章會說明哪些原則可用於和如何加以設定。

## <a name="what-is-the-multi-factor-authentication-registration-policy"></a>什麼是多重要素驗證註冊原則？

Azure Multi-Factor Authentication 是除了使用使用者名稱與密碼之外，需要再利用其他方法驗證身份的驗證方法。 它可以為使用者登入和交易提供第二層安全性。 為了讓使用者能夠回應 MFA 提示，他們必須先註冊 mfa。 

建議您要求對使用者登入進行 Azure Multi-Factor Authentication，因為它：

- 提供增強式驗證與一系列簡單的驗證選項

- 扮演著舉足輕重的角色，可讓您的組織防護帳戶遭竊及從中復原


如需 MFA 的詳細資訊，請參閱[什麼是 Azure Multi-factor Authentication？](../authentication/multi-factor-authentication.md)


## <a name="how-do-i-access-the-mfa-registration-policy"></a>如何存取 MFA 註冊原則？
   
MFA 註冊原則位於 [Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy) 頁面的 [設定] 區段中。
   
![MFA 原則](./media/howto-mfa-policy/1014.png)




## <a name="policy-settings"></a>原則設定

當您設定 MFA 註冊原則時，您需要設定：

- 要套用原則的使用者和群組：

    ![使用者和群組](./media/howto-mfa-policy/11.png)

- 您想要強制執行需求的 MFA 註冊的控制項：  

    ![Access](./media/howto-mfa-policy/12.png)

- 原則的狀態：

    ![強制執行原則](./media/howto-mfa-policy/14.png)


原則組態對話方塊會提供一個選項，以供您評估您的組態影響。

![預估的影響](./media/howto-mfa-policy/15.png)




## <a name="user-experience"></a>使用者體驗


如需相關的使用者經驗概觀，請參閱︰

* [Multi-Factor Authentication 註冊流程](flows.md#multi-factor-authentication-registration)。  
* [使用 Azure AD Identity Protection 時的登入體驗](flows.md)。  



## <a name="next-steps"></a>後續步驟

若要取得 Azure AD Identity Protection 的概觀，請參閱 [Azure AD Identity Protection 概觀](overview.md)。
