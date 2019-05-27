---
title: 條件式存取基準保護原則-Azure Active Directory
description: 基準條件式存取原則來保護組織遭受常見的攻擊
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca062f4024bb5b0946812e00c8ccc1254b56d333
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003292"
---
# <a name="what-are-baseline-policies"></a>什麼是基準原則？

基準原則是一組預先定義的原則，協助您保護組織針對許多常見的攻擊。 密碼噴灑、 重新執行和網路釣魚，可以包含這些常見的攻擊。 基準原則可用於所有版本的 Azure AD。 Microsoft 將這些基準保護原則提供給所有人因為過去幾年來的已識別為基礎的攻擊。 這些四個原則的目標是要確保所有組織都都不會有任何啟用的安全性基準層級需額外費用。  

管理自訂的條件式存取原則，需要有 Azure AD Premium 授權。

## <a name="baseline-policies"></a>基準原則

![在 Azure 入口網站中的條件式存取基準原則](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

有四個基準原則，可讓組織：

* [適用於系統管理員需要 MFA](howto-baseline-protect-administrators.md)
* [終端使用者 protection （預覽）](howto-baseline-protect-end-users.md)
* [區塊舊有的驗證 （預覽）](howto-baseline-protect-legacy-auth.md)
* [需要 MFA 才能進行服務管理 （預覽）](howto-baseline-protect-azure.md)

這些原則的所有四個會影響 POP、 IMAP 等舊版的 Office 桌面用戶端的舊有的驗證流程。

### <a name="require-mfa-for-admins"></a>管理員需要進行 MFA

由於 power 和系統管理員帳戶具有的存取，您應該將它們視為特別。 一種通用的方法，以改善保護特殊權限帳戶是要求更強的帳戶驗證表單，當它們用來登入。 在 Azure Active Directory 中，您可以取得更強的帳戶驗證需要系統管理員來註冊及使用 Azure Multi-factor Authentication。

[適用於系統管理員要求 MFA](howto-baseline-protect-administrators.md) 是被視為最特殊權限的 Azure AD 角色的下列目錄角色需要 multi-factor authentication (MFA) 的基準原則：

* 全域管理員
* SharePoint 管理員
* Exchange 系統管理員
* 條件式存取系統管理員
* 安全性系統管理員
* 技術支援中心管理員 / 密碼管理員
* 計費管理員
* 使用者管理員

如果貴組織擁有這些帳戶在指令碼或程式碼中使用，請考慮更換它們 [受管理身分識別](../managed-identities-azure-resources/overview.md)。 您暫時可以在基準原則中排除特定使用者帳戶，來解決此問題。

### <a name="end-user-protection-preview"></a>終端使用者 protection （預覽）

高特殊權限的系統管理員不是唯一的攻擊目標。 不良執行者通常會以一般使用者為目標。 存取後，這些不良執行者可以要求存取代表原始帳戶的持有者的特殊權限資訊或下載整個目錄並執行在整個組織的網路釣魚攻擊。 時有風險的登入時偵測到需要更為強式帳戶驗證是一種通用的方法，以改善所有使用者的保護。

**終端使用者 protection （預覽）** 是基準原則，可保護的目錄中的所有使用者。 啟用此原則需要註冊 Azure Multi-factor authentication 14 天內的所有使用者。 註冊之後，將 mfa 提示使用者，只在有風險的登入嘗試。 遭盜用的使用者帳戶會遭到封鎖，直到重設密碼，以及風險 dismissal。

### <a name="block-legacy-authentication-preview"></a>區塊舊有的驗證 （預覽）

舊版驗證通訊協定 (例如：IMAP、 SMTP、 POP3） 是通常用來驗證較舊的郵件用戶端的通訊協定。 舊版通訊協定不支援多重要素驗證。 即使您擁有需要 multi-factor authentication 為您的目錄的原則時，不良執行者可以使用其中一個這些舊版的通訊協定進行驗證，並略過 multi-factor authentication。

若要從惡意的驗證要求所做的舊版通訊協定保護您的帳戶，最好是封鎖它們。

**區塊舊有的驗證 （預覽）** 基準原則封鎖了使用傳統通訊協定進行的驗證要求。 已成功登入的所有使用者，就必須使用新式驗證。 其他的基準原則一起使用，將會封鎖來自舊版通訊協定的要求。 此外，所有使用者都都需要 mfa 時所需。 此原則不會封鎖 Exchange ActiveSync。

### <a name="require-mfa-for-service-management-preview"></a>需要 MFA 才能進行服務管理 （預覽）

組織會使用各種 Azure 服務，並從 Azure Resource Manager 為基礎的工具，像是管理：

* Azure 入口網站
* Azure PowerShell
* Azure CLI

使用任何一種工具來執行資源管理是具有高度權限的動作。 這些工具可以改變整個訂用帳戶的組態，例如服務設定和訂用帳戶計費。

若要保護特殊權限的動作，這**需要服務管理 （預覽） 的 MFA**原則會為任何使用者存取 Azure 入口網站、 Azure PowerShell 或 Azure CLI 需要多重要素驗證。

## <a name="enable-a-baseline-policy"></a>啟用基準原則

若要啟用基準原則：

1. 登入 **Azure 入口網站** 為全域管理員、 安全性系統管理員或條件式存取系統管理員。
1. 瀏覽至**Azure Active Directory** > **條件式存取**。
1. 在原則清單中，選取您想要啟用的基準原則。
1. 設定**啟用原則**要**上**。
1. 按一下 [儲存]。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱

* [可保護身分識別基礎結構的五個步驟](../../security/azure-ad-secure-steps.md)
* [什麼是 Azure Active Directory 中的條件式存取？](overview.md)
* [適用於系統管理員需要 MFA](howto-baseline-protect-administrators.md)
* [終端使用者 protection （預覽）](howto-baseline-protect-end-users.md)
* [區塊舊有的驗證 （預覽）](howto-baseline-protect-legacy-auth.md)
* [需要 MFA 才能進行服務管理 （預覽）](howto-baseline-protect-azure.md)