---
title: 如何在 Azure Active Directory Identity Protection (已重新整理) 中設定風險原則 | Microsoft Docs
description: 如何在 Azure Active Directory Identity Protection (已重新整理) 中設定風險原則。
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, 管理應用程式, 安全性, 風險, 風險層級, 弱點, 安全性原則
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbc2a1bd8b751ab1b88e54876227892ea3c257a3
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517533"
---
# <a name="how-to-configure-risk-policies-in-azure-active-directory-identity-protection-refreshed"></a>作法：在 Azure Active Directory Identity Protection (已重新整理) 中設定風險原則


Azure AD detects 風險事件為身分可能遭盜用的指標。 透過設定風險原則，您可以定義對偵測結果的自動回應：

- 使用登入風險原則，您可以設定對在使用者登入時偵測到的即時風險事件的回應。 
- 使用使用者風險原則，您可以設定對在一段時間內為使用者偵測到的所有作用中使用者風險的回應。  

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]


## <a name="what-is-the-sign-in-risk-policy"></a>什麼是登入風險原則？

Azure AD 會分析使用者的每次登入。 分析的目的是要偵測伴隨登入出現的可疑動作。 例如，使用匿名 IP 位址完成登入，或是從不熟悉的位置起始登入？ 在 Azure AD 中，系統可以偵測到的可疑動作也稱為風險事件。 根據在登入期間偵測到的風險事件，Azure AD 會計算一個值。 此值代表不是由合法使用者執行登入的可能性 (低、中、高)。 此可能性稱為**登入風險層級**。

登入風險原則是您可以針對特定登入風險層級設定的自動化回應。 在您的回應中，您可以封鎖對資源的存取，或需要傳遞多重要素驗證 (MFA) 查問以取得存取。

   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>如何存取登入風險原則？
   
登入風險原則位於 [Azure AD Identity Protection] 頁面的 [設定](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy) 區段中。
   
![登入風險原則](./media/howto-configure-risk-policies/1014.png "登入風險原則")


## <a name="sign-in-risk-policy-settings"></a>登入風險原則設定

當您設定登入風險原則時，您必須設定：

- 要套用原則的使用者和群組：

    ![使用者和群組](./media/howto-configure-risk-policies/11.png)

- 可觸發原則的登入風險層級：

    ![登入風險層級](./media/howto-configure-risk-policies/12.png)

- 您想要在符合登入風險層級時強制執行的存取類型：  

    ![Access](./media/howto-configure-risk-policies/13.png)

- 原則的狀態：

    ![強制執行原則](./media/howto-configure-risk-policies/14.png)


原則組態對話方塊會提供一個選項，以供您評估重新設定的影響。

![預估的影響](./media/howto-configure-risk-policies/15.png)

## <a name="what-you-should-know-about-sign-in-risk-policies"></a>您應知道的登入風險原則事項

您可以設定登入風險安全性原則以要求 MFA︰

![需要 MFA](./media/howto-configure-risk-policies/16.png)

不過，基於安全性理由，此設定只適用於已註冊 MFA 的使用者。 如果具有 MFA 需求的使用者尚未註冊 MFA，則 Identity Protection 會封鎖他們。

如果您想要要求有風險的登入進行 MFA，您應該：

1. 為受影響的使用者啟用多重要素驗證註冊原則。

2. 要求受影響的使用者登入沒有危險的工作階段以執行 MFA 註冊。

完成這些步驟可確保有風險的登入一定需要 Multi-Factor Authentication。

登入風險原則：

- 會套用至所有使用新式驗證的瀏覽器流量和登入。

- 不會套用至使用較舊安全性通訊協定的應用程式，其做法是停用位於同盟 IDP (例如 ADFS) 的 WS-Trust 端點。


如需相關的使用者經驗概觀，請參閱︰

* [有風險的登入復原](flows.md#risky-sign-in-recovery)
* [已封鎖有風險的登入](flows.md#risky-sign-in-blocked)  
* [使用 Azure AD Identity Protection 時的登入體驗](flows.md)  









## <a name="what-is-a-user-risk-policy"></a>什麼是使用者風險原則？

Azure AD 會分析使用者的每次登入。 分析的目的是要偵測伴隨登入出現的可疑動作。 在 Azure AD 中，系統可以偵測到的可疑動作也稱為風險事件。 雖然可以即時偵測到有些風險事件，但也有宜些風險事件需要更多時間。 例如，若要偵測不可能到達非典型位置的旅行，系統需要 14 天的初始學習期間，才能了解使用者的一般行為。 有數個選項可以解決偵測到的風險事件。 例如，您可以手動解決個別的風險事件，或者使用登入風險或使用者風險條件式存取原則來解決這些事件。

針對使用者偵測到但未獲得解決的所有風險事件也稱為作用中風險事件。 與使用者相關聯的作用中風險事件也稱為使用者風險。 Azure AD 會根據使用者風險，計算使用者遭到入侵的可能性 (低、中、高)。 此可能性稱為使用者風險層級。

![使用者風險](./media/howto-configure-risk-policies/11031.png)

使用者風險原則是您可以針對特定使用者風險層級設定的自動化回應。 利用使用者風險原則，您可以封鎖對資源的存取，或需要變更密碼，讓使用者帳戶回到初始狀態。


## <a name="how-do-i-access-the-user-risk-policy"></a>如何存取使用者風險原則？
   
使用者風險原則位在 [Azure AD Identity Protection 頁面](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy)上的 [設定] 區段中。
   
![使用者風險原則](./media/howto-configure-risk-policies/11014.png)



## <a name="user-risk-policy-settings"></a>使用者風險原則設定

在設定使用者風險原則時，您必須設定：

- 要套用原則的使用者和群組：

    ![使用者和群組](./media/howto-configure-risk-policies/111.png)

- 可觸發原則的登入風險層級：

    ![使用者風險層級](./media/howto-configure-risk-policies/112.png)

- 您想要在符合登入風險層級時強制執行的存取類型：  

    ![Access](./media/howto-configure-risk-policies/113.png)

- 原則的狀態：

    ![強制執行原則](./media/howto-configure-risk-policies/114.png)

原則組態對話方塊會提供一個選項，以供您評估您的組態影響。

![預估的影響](./media/howto-configure-risk-policies/115.png)

## <a name="what-you-should-know-about-user-risk-polices"></a>您應知道的使用者風險原則事項

您可以設定使用者風險安全性原則，以根據風險層級防止使用者登入。

![封鎖](./media/howto-configure-risk-policies/116.png)


封鎖登入：

* 避免對受影響的使用者產生新的使用者風險事件
* 可讓系統管理員手動地補救會影響使用者身分識別的風險事件，並將它還原到安全的狀態


























## <a name="best-practices"></a>最佳作法

選擇 [高]  臨界值可減少觸發原則的次數，並將對使用者的影響降至最低。  

不過，它會從原則中排除標示 [低] 和 [中] 度風險的登入，而無法阻止攻擊者利用遭到入侵的身分識別。

設定原則時，

- 排除不會 / 不能有 Multi-Factor Authentication 的使用者

- 在啟用原則並不實用 (例如無法存取技術服務人員) 的地區設定中排除使用者

- 排除可能會產生大量誤判的使用者 (開發人員、安全性分析人員)

- 在原則推出初期，或如果您必須盡量減少使用者所看到的挑戰，請使用 [高] 閾值。

- 如果您的組織需要更高的安全性，請使用 [低]  臨界值。 選取 [低]  臨界值會帶來額外的使用者登入挑戰，並提高安全性。

大部分組織的建議預設值是設定 [中]  臨界值的規則，以取得可用性與安全性之間的平衡。





## <a name="next-steps"></a>後續步驟

 [第 9 頻道：Azure AD 和身分識別示範：身分識別保護預覽版](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

