---
title: "在 Azure AD 中管理緊急存取系統管理帳戶 |Microsoft 文件"
description: "本文說明如何使用緊急存取帳戶以協助組織限制現有的 Azure Active Directory 環境內的特殊權限的存取。"
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
ms.openlocfilehash: 039012b8ba0b83f6338128a2200d1232ae6467f3
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="manage-emergency-access-administrative-accounts-in-azure-ad"></a>在 Azure AD 中管理緊急存取系統管理帳戶 

大部分的日常活動*全域管理員*，讓使用者不需要權限。 使用者應該不會永久指派給角色，因為它們可能會不小心執行需要比它們也應該有較高的權限的工作。 當使用者不需要做為全域系統管理員時，它們應該使用 Azure Active Directory (Azure AD) 特殊權限身分識別管理 (PIM)，他們自己的帳戶或其他系統管理員帳戶啟用角色指派。

除了使用者的系統管理存取權限的本身，您需要避免不慎鎖定超出您的 Azure AD 租用戶的系統管理工作因為您無法登入也啟用為現有個別使用者的帳戶系統管理員。 您可以避免不小心缺乏系統管理存取權的影響儲存兩個或多個*緊急存取帳戶*租用戶中。

緊急存取帳戶可協助組織限制現有的 Azure Active Directory 環境內的特殊權限的存取。 這類帳戶具有高度權限，以及它們不會指派給特定人員。 緊急存取帳戶的上限為緊急或 '急用' 的情況下，無法使用一般的系統管理帳戶的情況。 組織必須維護目標，以限制只有該期間為所需的時間緊急的帳戶使用。

組織可能需要在下列情況中使用的緊急存取帳戶：

 - 同盟的使用者帳戶，以及同盟是目前無法使用，因為資料格網路中斷或身分識別提供者中斷。 例如，如果您的環境中身分識別提供者主機已中斷，使用者可能無法登入 Azure AD 會重新導向至其身分識別提供者時。 
 - 系統管理員註冊透過 Azure 多重要素驗證，而且所有其個別的裝置無法使用。 使用者可能無法完成多因素驗證至啟用角色。 例如，儲存格網路中斷禁止接聽電話撥號，或接收文字訊息，只有兩種驗證機制，其註冊其裝置。 
 - 具有最新的全域系統管理存取權的人員已離開組織。 Azure AD 可防止上次*全域管理員*帳戶被刪除，但它不會防止帳戶被刪除或已停用在內部部署。 符合其中任一情況，可能會造成無法復原此帳戶的組織。

## <a name="initial-configuration"></a>初始設定

建立兩個以上的緊急存取帳戶。 這些應該是僅限雲端使用的帳戶\*。 onmicrosoft.com 網域，而且未同盟或從內部部署環境同步處理。 

帳戶不應該與組織中任何個別的使用者相關聯。 組織需要確保這些帳戶的認證會安全且已知保持只給已獲授權使用他們的個人。 

> [!NOTE]
> 緊急存取帳戶的帳戶密碼通常會分成兩個或三個部分、 寫入張不同的紙張，並儲存在安全的其他位置的安全、 以外的防火保險箱。 
>
> 請確定您的緊急存取帳戶未連接任何員工提供行動電話與，硬體權杖該移動個別的員工，或其他特定員工的認證。 此預防措施涵蓋的例子中，個別的員工會無法連線時需要的認證。 

### <a name="initial-configuration-with-permanent-assignments"></a>具有永久指派的初始組態

其中一個選項是讓使用者永久成員的*全域管理員*角色。 此選項會是適用於沒有 Azure AD Premium P2 訂用帳戶的組織。

若要減少攻擊受害密碼造成的風險，Azure AD 會建議您針對所有的個別使用者需要多重要素驗證。 這個群組應該包含系統管理員和其他所有項目 （例如，財務人員） 其遭盜用的帳戶會有重大影響。 

不過，如果您的組織不需要共用的裝置，Multi-factor Authentication 可能無法為這些緊急存取帳戶。 如果您設定條件式存取原則，來要求[每位系統管理員的多重要素驗證註冊](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states)Azure AD 的其他連線和軟體即服務 (SaaS) 應用程式，您可能需要設定原則要排除這項需求的緊急存取帳戶的排除項目。

### <a name="initial-configuration-with-approvals"></a>具有核准的初始組態

另一個選項是設定為合格和核准者啟動您的使用者*全域管理員*角色。 此選項會要求您的組織有 Azure AD Premium P2 訂用帳戶。 它也會需要適用於多個人擔當和網路環境之間共用使用多重要素驗證選項。 這些需求會因為啟動*全域管理員*角色會要求使用者執行多重要素驗證過。 如需詳細資訊，請參閱[如何需要多重要素驗證在 Azure AD Privileged Identity Management](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa)。

不建議使用的緊急存取帳戶的個人裝置與相關聯的多重要素驗證。 在實際的緊急狀況下，需要多因素驗證註冊的裝置的存取權的人員可能無法擁有個人裝置。 

也請考慮威脅大環境。 例如，發生未預期的情況下，例如天災緊急可能會引發，在行動電話或其他網路可能會無法使用。 請務必確保任何已註冊的裝置會保留在 Azure AD 與通訊的多個方法已知且安全位置。

## <a name="ongoing-monitoring"></a>持續不斷的監控

監視[Azure AD 登入和稽核記錄](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-activity-sign-ins)的任何登入和稽核 」 活動的緊急存取帳戶。 通常這些帳戶不登入，而且應該不會進行變更，因此使用它們很可能是異常，而且需要安全性調查。

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>驗證必須在定期間隔發生帳戶檢查

若要驗證的帳戶，執行下列步驟最小值：
- 每隔 90 天。
- 當已在 IT 人員，例如工作變更、 出發或新雇用的人員最近的變更時。
- 組織中的 Azure AD 訂用帳戶發生變更時。

訓練人員使用的緊急存取帳戶，請執行下列動作：

* 請確定安全性監視的人員了解帳戶檢查活動正在進行中。
* 驗證雲端使用者帳戶可以登入，並啟用其角色，並在發生緊急狀況時執行這些步驟可能需要的使用者定型程序。
* 請確定它們有未註冊多重要素驗證或自助式密碼重設 (SSPR) 至任何個別使用者的裝置或個人的詳細資料。 
* 如果帳戶註冊的多重要素驗證在角色啟動期間使用的裝置，請確定裝置是可存取所有的系統管理員可能需要使用在發生緊急狀況時。 同時確認透過至少兩個不共用通用的失敗模式的機制註冊裝置。 例如，裝置可以透過設備的無線網路和儲存格的提供者網路的網際網路通訊。
* 更新的帳戶認證。

## <a name="next-steps"></a>後續步驟
- [新增雲端式使用者](add-users-azure-active-directory.md)和[全域管理員角色指派給新使用者](active-directory-users-assign-role-azure-portal.md)。
- [註冊 Azure Active Directory Premium](active-directory-get-started-premium.md)，如果您尚未註冊已。
- [針對個別使用者指派為系統管理員需要 Azure Multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states)。
- [在 Office 365 中設定的全域系統管理員提供額外保護](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560)，如果您使用 Office 365。
- [執行全域系統管理員存取檢閱](active-directory-privileged-identity-management-how-to-start-security-review.md)和[轉換現有的全域系統管理員更特定的系統管理員角色](active-directory-assign-admin-roles-azure-portal.md)。


