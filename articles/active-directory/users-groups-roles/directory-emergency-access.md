---
title: 管理緊急存取系統管理員帳戶-Azure Active Directory |Microsoft Docs
description: 本文說明如何使用緊急存取帳戶，幫助防止不慎鎖定 Azure Active Directory (Azure AD) 租用戶。
services: active-directory
author: markwahl-msft
ms.author: curtand
ms.date: 03/19/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: f430a832ffb35b95d0bf4eff2d82be5ecc3d865c
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224935"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>在 Azure AD 中管理緊急存取帳戶

請務必防止不慎鎖定 Azure Active Directory (Azure AD) 租用戶，因為您無法以系統管理員的身分，登入或啟動現有個別使用者的帳戶。 您可以藉由在租用戶中建立兩個或多個「緊急存取帳戶」，來減緩不慎失去系統管理存取權的影響。

緊急存取帳戶具有高度權限，不會指派給特定個人。 緊急存取帳戶僅限用於無法使用一般系統管理帳戶的緊急或「急用」狀況。 組織必須限制只有在絕對必要時，才能使用緊急帳戶，並維護此限制目標。

本文提供在 Azure AD 中管理緊急存取帳戶的指導方針。

## <a name="when-would-you-use-an-emergency-access-account"></a>何時使用緊急存取帳戶？

組織在下列情況中可能需要使用緊急存取帳戶：

- 使用者帳戶為同盟，但由於行動網路中斷或身分識別提供者運作中斷，同盟目前停用。 例如，如果您環境中的識別提供者主機已中斷，那麼當 Azure AD 重新導向至使用者的識別提供者時，使用者可能無法登入。
- 管理員已透過 Azure Multi-Factor Authentication 進行註冊，但他們的所有個別裝置皆無法使用，或服務無法使用。 使用者可能無法完成 Multi-Factor Authentication 來啟動角色。 例如，行動網路的中斷會讓使用者無法接聽來電或接收文字簡訊 (唯一為裝置註冊的兩種驗證機制)。
- 具有最新全域系統管理員存取權的人員已離開組織。 Azure AD 可防止最新的「全域管理員」帳戶遭到刪除，但無法防止該帳戶在內部部署環境中遭到刪除或停用。 其中任一情況皆可能造成組織無法復原帳戶。
- 可能發生未預期的緊急情況，例如自然災害緊急情況，在此期間可能無法使用行動電話或其他網路。 

## <a name="create-two-cloud-based-emergency-access-accounts"></a>建立兩個雲端式緊急存取帳戶

建立兩個或以上的緊急存取帳戶。 這些帳戶應該是使用 \*.onmicrosoft.com 網域的雲端限定帳戶，而且未與內部部署環境同盟或同步。

設定這些帳戶時，必須符合下列需求：

- 這些緊急存取帳戶不應與組織中的任何個別使用者相關聯。 請確定您的帳戶未連線至任何員工提供的行動電話、會與個別員工一同移動的硬體權杖，或其他員工專屬的認證。 此預防措施適用於需要認證時卻聯絡不到個別員工的狀況。 請務必確保任何已註冊的裝置可處於已知且安全的位置，並有多個方法可與 Azure AD 通訊。
- 用於緊急存取帳戶的驗證機制應該與您其他管理帳戶 (包括其他緊急存取帳戶) 所使用的驗證機制不同。  例如，如果您的一般管理員登入是透過內部部署 MFA 進行，則 Azure MFA 會是不同的機制。  不過，如果 Azure MFA 是驗證管理帳戶的主要部分，請考慮採用不同的方法，例如，對第三方 MFA 提供者使用條件式存取。
- 裝置或認證不得過期或處於因缺乏使用而自動清除的範圍內。  
- 您應該為緊急存取帳戶永久保留全域管理員角色指派。 


### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>將至少一個帳戶從電話式多重要素驗證中排除

若要降低因密碼洩漏而遭到攻擊的風險，Azure AD 建議您要求所有個別使用者使用多重要素驗證。 這個群組包含管理員和所有其他人員 (例如財務人員)，其遭洩漏的帳戶會造成重大影響。

但是，至少有一個緊急存取帳戶不應該具有與其他非緊急帳戶相同的多重要素驗證機制。 這包括第三方多重要素驗證解決方案。 如果您具備條件式存取原則，要針對 Azure AD 和其他連線的軟體即服務 (SaaS) 應用程式，要求[每位管理員進行多重要素驗證](../authentication/howto-mfa-userstates.md)，則應該將緊急存取帳戶從此要求中排除，並設定其他機制。 此外，您應該確定帳戶沒有每位使用者的多重要素驗證原則。

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>從條件式存取原則中排除至少一個帳戶

在緊急情況下，您不希望有可能會阻止您存取以解決問題的原則。 應該從所有條件式存取原則中，排除至少一個緊急存取帳戶。 如果您已啟用[基準原則](../conditional-access/baseline-protection.md)，您應該排除緊急存取帳戶。

## <a name="additional-guidance-for-hybrid-customers"></a>混合式客戶的其他指導方針

適用於使用 AD Domain Services 和 ADFS 或類似識別提供者以建立同盟到 Azure AD 之組織的另一個選項是，設定可由該識別提供者提供其 MFA 宣告的緊急存取帳戶。  例如，緊急存取帳戶可由憑證和金鑰組 (例如儲存在智慧卡上的憑證和金鑰組) 支援。  當該使用者通過 AD 驗證時，ADFS 可以向 Azure AD 提供宣告，指出使用者已符合 MFA 需求。  即使採用這種方法，組織仍必須擁有雲端式的緊急存取帳戶，以避免無法建立同盟。 

## <a name="store-devices-and-credentials-in-a-safe-location"></a>將裝置和認證儲存在安全的位置

組織需要確保緊急存取帳戶的認證保持在安全狀態，且僅有獲得授權使用的個人知道該認證內容。 有些客戶使用智慧卡，而其他客戶使用密碼。 緊急存取帳戶的密碼通常會分成兩個或三個部分、記錄在不同的紙上，以及儲存在不同安全地點的安全防火保險箱中。

如果使用密碼，請確定帳戶具有不會使密碼過期的強式密碼。 理想情況下，密碼長度應該至少為 16 個字元，並隨機產生。


## <a name="monitor-sign-in-and-audit-logs"></a>監視登入與稽核記錄

監視 [Azure AD 登入和稽核記錄](../reports-monitoring/concept-sign-ins.md)，可了解緊急存取帳戶的任何登入和稽核活動。 通常這些帳戶應該不會登入，且應該不會進行變更，因此帳戶的使用很可能是出現異常，需要安全性檢查。

## <a name="validate-accounts-at-regular-intervals"></a>定期驗證帳戶

如果要培訓員工成員使用緊急存取帳戶並驗證緊急存取帳戶，請至少定期執行下列步驟：

- 確定安全性監控人員了解帳戶檢查活動須持續進行。
- 確定要使用這些帳戶的緊急急用程序已記錄下來，而且是最新的。
- 確定緊急情況下可能需要執行這些步驟的系統管理員和安全人員接受有關此程序的培訓。
- 為緊急存取帳戶更新帳戶認證，尤其是任何密碼，然後驗證緊急存取帳戶是否可以登入並執行管理工作。
- 確定使用者尚未將 Multi-Factor Authentication 或自助式密碼重設 (SSPR) 註冊至任何個別使用者的裝置或個人詳細資料。 
- 如果帳戶已在裝置上註冊以使用 Multi-Factor Authentication (在登入或角色啟用時使用)，請確定在發生緊急狀況時可能需要使用裝置的所有管理員皆可存取裝置。 另外也請確認裝置已透過至少兩個失敗模式不同的網路路徑來進行通訊。 例如，裝置可以透過設施的無線網路和電信業者提供的網路來與網際網路通訊。

應該定期執行這些步驟，並進行重要變更：

- 至少每隔 90 天
- 當 IT 人員在最近發生變動時，例如工作變更、離職或雇用新人員
- 當組織中的 Azure AD 訂用帳戶發生變更時

## <a name="next-steps"></a>後續步驟

- [在 Azure AD 中保護混合式部署和雲端部署的特殊權限存取](directory-admin-roles-secure.md)
- [使用 Azure AD 新增使用者](../fundamentals/add-users-azure-active-directory.md)，並[將新使用者指派至全域管理員角色](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- 請[註冊 Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) (如果您尚未註冊)
- [如何要求使用者使用雙步驟驗證](../authentication/howto-mfa-userstates.md)
- 如果您使用 Office 365，[在 Office 365 中為全域管理員設定額外保護](https://docs.microsoft.com/office365/enterprise/protect-your-global-administrator-accounts)
- [啟動全域管理員的存取權檢閱](../privileged-identity-management/pim-how-to-start-security-review.md)，並[將現有的全域管理員轉換為更具體的管理員角色](directory-assign-admin-roles.md)
