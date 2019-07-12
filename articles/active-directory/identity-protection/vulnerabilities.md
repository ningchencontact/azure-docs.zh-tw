---
title: Azure Active Directory Identity Protection 偵測到的弱點
description: Azure Active Directory Identity Protection 偵測到的弱點概觀。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 04/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: b481030c5d2d8e7d5e7061cdf256a202e08d6cbf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108795"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection 偵測到的弱點

弱點是可以利用的攻擊者的環境中。 我們建議系統管理員解決這些弱點來改善其組織的安全性狀態。

![Identity Protection 所報告的弱點](./media/vulnerabilities/identity-protection-vulnerabilities.png)

下列各節概述 Identity Protection 所報告的弱點。

## <a name="multi-factor-authentication-registration-not-configured"></a>未設定 Multi-factor Authentication 註冊

此弱點可協助您評估在您的組織部署 Azure Multi-factor Authentication。

Azure Multi-factor Authentication 提供第二個使用者驗證的安全性層級。 這有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。 Azure Multi-factor Authentication 提供易於使用的驗證選項，例如：

* 撥打電話
* 簡訊
* 行動應用程式通知
* OTP 驗證碼

建議您要求對使用者登入進行 Multi-Factor Authentication。多重要素驗證會透過 Identity Protection 的 風險型條件式存取原則中扮演重要的角色。

如需詳細資訊，請參閱[什麼是 Azure Multi-Factor Authentication？](../authentication/multi-factor-authentication.md)。

## <a name="unmanaged-cloud-apps"></a>非受控雲端應用程式

此弱點可協助您識別組織中非受控雲端應用程式。

IT 人員多半不知道其組織中的所有雲端應用程式。 您應該不難想像為什麼系統管理員必須對未經授權存取公司資料、可能的資料外洩和其他安全性風險有所顧慮。

我們建議您部署 Cloud Discovery 來探索非受控的雲端應用程式，以及管理這些使用 Azure Active Directory 應用程式。

如需詳細資訊，請參閱 [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)。

## <a name="security-alerts-from-privileged-identity-management"></a>來自 Privileged Identity Management 的安全性警示

此弱點可協助您找出並解決有關您組織中特殊權限身分識別的警示。  

若要讓使用者能夠執行特殊權限作業，組織必須賦予使用者在 Azure AD、Azure 或 Office 365 資源或其他 SaaS 應用程式中的暫時或永久特殊權限存取權。 這些特殊權限的使用者會增加您組織的受攻擊面。 此弱點可協助您識別具有非必要特殊權限存取權的使用者，並採取適當的行動來減少或消除其所造成的風險。

我們建議組織使用 Azure AD Privileged Identity Management 來管理，控制項，並監視在 Azure AD 中的特殊權限身分識別，以及 Office 365 或 Microsoft Intune 等其他 Microsoft online services。

如需詳細資訊，請參閱 [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md)。

## <a name="see-also"></a>另請參閱

[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
