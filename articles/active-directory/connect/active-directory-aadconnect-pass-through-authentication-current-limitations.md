---
title: Azure AD Connect：傳遞驗證 - 目前的限制 | Microsoft Docs
description: 本文說明 Azure Active Directory (Azure AD) 傳遞驗證目前的限制
services: active-directory
keywords: Azure AD Connect 傳遞驗證, 安裝 Active Directory, Azure AD 的必要元件, SSO, 單一登入
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: billmath
ms.openlocfilehash: ac085bf972885819f7c79996b0f6638fc01fc00d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory 傳遞驗證：目前的限制

>[!IMPORTANT]
>Azure Active Directory (Azure AD) 傳遞驗證是免費功能，您不需要擁有任何 Azure AD 付費版本即可使用此功能。 傳遞驗證僅適用於 Azure AD 的全球執行個體，不適用於 [Microsoft Azure 德國雲端](http://www.microsoft.de/cloud-deutschland)或 [Microsoft Azure Government 雲端](https://azure.microsoft.com/features/gov/)。

## <a name="supported-scenarios"></a>支援的案例

下列案例會有完整支援︰

- 使用者登入所有網頁瀏覽器型應用程式
- 使用者登入支援[新式驗證](https://aka.ms/modernauthga)的 Office 應用程式 - 「具有」新式驗證的 Office 2016 和 Office 2013
- 使用者使用傳統通訊協定 (例如 Exchange ActiveSync、SMTP、POP 及 IMAP) 來登入 Outlook 用戶端。
- 使用者登入支援新式驗證的商務用 Skype，包括線上與混合式拓撲。 如需支援拓撲的詳細資訊，請前往[這裡](https://technet.microsoft.com/library/mt803262.aspx)。
- 適用於 Windows 10 裝置的 Azure AD 網域加入
- 用於多重要素驗證的應用程式密碼。

## <a name="unsupported-scenarios"></a>不支援的情節

下列案例不受支援︰

- 使用者登入舊版 Office 用戶端應用程式 (Outlook 除外)：Office 2010 和「未啟用」新式驗證的 Office 2013。 組織應該盡可能切換到新式驗證。 新式驗證允許傳遞驗證支援。 其還能使用[條件式存取](../active-directory-conditional-access-azure-portal.md)功能 (如 Azure Multi-Factor Authentication) 保護使用者帳戶。
- 使用者登入「不具有」新式驗證的商務用 Skype 用戶端應用程式。
- 使用者登入 PowerShell 1.0 版。 我們建議您使用 PowerShell 2.0 版。
- 偵測[認證外洩](../active-directory-reporting-risk-events.md#leaked-credentials)的使用者。
- Azure AD 網域服務必須在租用戶上啟用 [密碼雜湊同步處理]。 因此，使用傳遞驗證的租用戶「只有」在需要 Azure AD 網域服務的情況下無法運作。
- 傳遞驗證未與 [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md) 整合。
- Apple 裝置註冊計劃 (Apple DEP) 不支援新式驗證。  Apple DEP 裝置無法在 Intune 中註冊使用傳遞驗證的網域。

>[!IMPORTANT]
>_僅_針對不支援的情節，請在 Azure AD Connect 精靈的 [選用功能](active-directory-aadconnect-get-started-custom.md#optional-features) 頁面上，啟用密碼雜湊同步處理作為因應措施。

>[!NOTE]
啟用密碼雜湊同步處理可讓您選擇在內部部署基礎結構中斷時，將驗證容錯移轉。 這個從傳遞驗證到 Active Directory 密碼雜湊同步處理的容錯移轉不會自動進行， 您必須使用 Azure AD Connect，以手動方式切換登入方法。 如果執行 Azure AD Connect 的伺服器關閉，您將需要 Microsoft 支援服務的協助，以關閉傳遞驗證。

## <a name="next-steps"></a>後續步驟
- [快速入門](active-directory-aadconnect-pass-through-authentication-quick-start.md)：開始使用 Azure AD 傳遞驗證。
- [智慧鎖定](active-directory-aadconnect-pass-through-authentication-smart-lockout.md)：了解如何在租用戶中設定智慧鎖定功能以保護使用者帳戶。
- [技術深入探討](active-directory-aadconnect-pass-through-authentication-how-it-works.md)：了解傳遞驗證功能的運作方式。
- [常見問答集](active-directory-aadconnect-pass-through-authentication-faq.md)：尋找傳遞驗證功能常見問題的解答。
- [疑難排解](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)：了解如何解決傳遞驗證功能的常見問題。
- [安全性深入探討](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md)：取得傳遞驗證功能上的深入技術資訊。
- [Azure AD 無縫 SSO](active-directory-aadconnect-sso.md)：深入了解此互補功能。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)：使用 Azure Active Directory 論壇提出新功能要求。
