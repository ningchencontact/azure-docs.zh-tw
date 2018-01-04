---
title: "在 Azure AD 中管理的緊急存取的系統管理帳戶 |Microsoft 文件"
description: "描述如何使用緊急存取帳戶以協助組織限制現有的 Azure Active Directory 環境內的特殊權限的存取。"
services: active-directory
keywords: "請勿在未諮詢 SEO 之前新增或編輯關鍵字。"
author: markwahl-msft
ms.author: billmath
ms.date: 12/13/2017
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.openlocfilehash: 75ea8e445c890e7af5ab14f4fc4c53cfb1af4568
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2017
---
# <a name="managing-emergency-access-administrative-accounts-in-azure-ad"></a>在 Azure AD 中管理的緊急存取系統管理帳戶 

大部分的日常活動，不需要全域的系統管理角色權限。  使用者應該不會永久指派給角色，使用者可能會不小心執行較高的權限所需的工作。 當使用者需要做為全域系統管理員時，他們應該啟動使用 Azure AD PIM，在他們自己的帳戶或其他系統管理員帳戶上的角色指派。

除了自己進行系統管理存取權限的使用者，客戶必須準備以確保，它們不會收到其中它們可能會不小心遭到鎖定而無法因其無法登入其 Azure AD 租用戶的系統管理的情況在或啟用現有個別使用者的系統管理員身分的帳戶。  它們可減少意外缺乏透過將儲存在兩個或多個其租用戶的系統管理存取權的影響*緊急存取帳戶*。

緊急存取帳戶可協助組織限制現有的 Azure Active Directory 環境內的特殊權限的存取。 這些帳戶具有高度權限，且不會指派給特定人員。 緊急存取帳戶的上限為緊急無法使用一般的系統管理帳戶的 '急用' 案例。  組織必須確保控制和減少只有它是必要的時間緊急的帳戶使用的目標。

組織可能需要將案例使用的緊急存取帳戶：

 - 同盟的使用者帳戶，而無法使用，因為網路中斷或中斷的身分識別提供者同盟。  例如，如果客戶的環境中身分識別提供者主機已中斷，然後使用者可能無法登入 Azure AD 會重新導向至其身分識別提供者時。 
 - 透過 MFA 註冊系統管理員 」 和所有其個別的裝置都無法使用。  可能的使用者，都不能完成多因素驗證，以啟用角色，如果資料格網路中斷可能會防止使用者能夠接聽電話來電或接收文字訊息的執行個體，而這是唯一的機制它們註冊其裝置。 
 - 最後一個有通用的系統管理存取權的人員離開組織。  Azure AD 防止上次的全域管理員帳戶刪除，但不會防止帳戶被刪除或已停用在內部，導致其中組織是無法復原該帳戶的情況下。

## <a name="initial-configuration"></a>初始設定

建立兩個以上的緊急存取帳戶。  這些應該是利用僅限雲端帳戶 *。 onmicrosoft.com 網域，非同盟或從內部部署環境同步處理。  

它們不應該與組織中的任何個別使用者關聯。  組織需要確保這些帳戶的認證都保持安全且已知只有已獲授權使用它們的人員。 

注意： 一般而言，緊急存取帳戶的帳戶密碼是分成兩個或三個部分，然後張不同的紙張上的寫入並儲存在安全的不同位置的安全、 以外的防火保險箱。 確定未與任何員工提供行動電話連線的緊急存取帳戶，硬體權杖具有個別的員工或其他特定員工的認證，該旅行時是無法連線到該個別員工需要的認證。 

### <a name="initial-configuration-with-permanent-assignments"></a>具有永久指派的初始組態

其中一個選項是將這些使用者指派為永久全域管理員角色的成員。  這會是適用於組織沒有 Azure AD Premium P2 訂用帳戶。

Azure AD 會建議您需要所有的個別使用者，包括系統管理員和所有其帳戶會有重大影響其他使用者的 multi-factor authentication (MFA) 危害 （例如財務長）。 這可降低密碼遭入侵所導致的攻擊風險。 不過，如果您的組織不需要共用的裝置，然後 MFA 可能無法為這些緊急存取帳戶。  如果您設定條件式存取原則，來要求[每位系統管理員的 MFA 註冊](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states)Azure ad 和其他連線 SaaS 應用程式，您可能需要設定要排除的緊急存取帳戶從此原則排除項目需求。

### <a name="initial-configuration-with-approvals"></a>具有核准的初始組態

另一個選項是設定為合格的使用者和核准者啟動全域管理員角色。  這會需要組織有 Azure AD Premium P2 訂閱，以及適用於多個人擔當和網路環境之間共用使用 MFA 選項。  這是因為全域管理員角色的啟用要求使用者先前已執行 MFA。  進一步了解在[如何要求 MFA，在 Azure AD Privileged Identity Management](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa)。

個人裝置與相關聯的 MFA 不是建議使用的緊急存取帳戶，因為在實際緊急一位人員需要有 MFA 註冊的裝置存取權，可能不會保留個人裝置的使用者。  也請考慮威脅架構之下，如果因為發生未預期的情況下的行動電話或其他網路無法使用在緊急天然災害。  因此，務必確保任何已註冊的裝置會保留在 Azure AD 與通訊的多個方法已知且安全位置。

## <a name="ongoing-monitoring"></a>持續不斷的監控

監視[Azure AD 登入和稽核記錄檔](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-activity-sign-ins)的任何登入和稽核 」 活動的緊急存取帳戶。  通常這些帳戶不登入，而且應該不會進行變更，因此使用它們很可能是異常，而且需要安全性調查。

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>帳戶檢查驗證必須在定期間隔發生

在至少執行下列步驟：
 - 每隔 90 天
 - 當已在 IT 人員 – 最近已變更工作變更，離開或新雇用的人員
 - 組織中的 Azure AD 訂用帳戶發生變更時

若要確保員工會將定型中使用的緊急存取帳戶：

1.  請確定安全性監視的人員了解帳戶核取活動正在進行中。
2.  驗證雲端使用者帳戶可以登入，並啟用其角色，並在發生緊急狀況時執行這些步驟可能需要的使用者定型程序。
3.  請確認他們尚未註冊 MFA 或 SSPR 任何個別使用者的裝置或個人的詳細資料。  
4. 如果帳戶為 MFA 註冊裝置，使用角色啟動期間，請確認裝置可以存取所有的系統管理員可能需要使用在發生緊急狀況時。  也請確認裝置已註冊透過不共用通用的失敗模式的至少兩個機制 （例如，裝置無法通訊至網際網路，同時透過設備的無線網路，以及透過儲存格的提供者網路）。
5.  更新認證。

## <a name="next-steps"></a>後續步驟
- [新增雲端式使用者](add-users-azure-active-directory.md)和[全域管理員角色指派給新使用者](active-directory-users-assign-role-azure-portal.md)
- [註冊 Azure Active Directory Premium](active-directory-get-started-premium.md)，如果您還沒有這麼做
- [針對個別使用者指派為系統管理員需要 Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states)
- [在 Office 365 中設定的全域系統管理員提供額外保護](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560)，如果您使用 Office 365
- [執行全域系統管理員存取檢閱](active-directory-privileged-identity-management-how-to-start-security-review.md)和[轉換現有的全域系統管理員更特定的系統管理員角色](active-directory-assign-admin-roles-azure-portal.md)

