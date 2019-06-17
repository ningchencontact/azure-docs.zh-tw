---
title: 基準原則使用者保護 （預覽）-Azure Active Directory
description: 條件式存取原則，以要求多重要素驗證的使用者
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
ms.openlocfilehash: e5b72be0dbe35cf95eed404c7c1407c53f5f2ecb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112355"
---
# <a name="baseline-policy-end-user-protection-preview"></a>基準原則：終端使用者 protection （預覽）

我們傾向於認為系統管理員帳戶是唯一的帳戶需要使用 multi-factor authentication (MFA) 的保護。 系統管理員具有廣泛存取機密資訊，而且可以對整個訂用帳戶的設定進行變更。 不過，不良執行者會傾向於目標使用者。 存取後，這些不良執行者可以要求特殊權限的資訊代表原始帳戶的持有者的存取權，或下載整個目錄，以執行在整個組織的網路釣魚攻擊。 需要更為強式帳戶驗證，例如多重要素驗證 (MFA) 是一種通用的方法，以改善所有使用者的保護。

若要達到合理的平衡的安全性和可用性，應該不會提示使用者登入每一次。 反映一般使用者的行為，例如從相同的位置，從相同的裝置登入的驗證要求會有低危害的機會。 只有登入，會被視為有風險，並示範的不良執行者的特性應 MFA 挑戰提示。

![需要 MFA 的使用者](./media/howto-baseline-protect-end-users/baseline-policy-end-user-protection.png)

終端使用者保護是以風險為基礎的 MFA[基準原則](concept-baseline-protection.md)來保護在目錄中，包括所有的系統管理員角色的所有使用者。 啟用此原則要求所有使用者註冊使用驗證器應用程式的 MFA。 使用者可以忽略 MFA 註冊提示 14 天之後，他們即將遭到封鎖無法登入，直到在註冊 MFA。 一旦註冊過 MFA，將 mfa 提示使用者，只在有風險的登入嘗試。 遭盜用的使用者帳戶會遭到封鎖，直到重設其密碼，並有已關閉風險事件。

> [!NOTE]
> 此原則會套用到所有使用者，包括來賓帳戶，並登入所有應用程式時，會評估。

## <a name="recovering-compromised-accounts"></a>復原遭盜用的帳戶

為了協助保護我們的客戶，Microsoft 的認證外洩的服務會尋找公開可用的使用者名稱/密碼組。 如果它們符合其中一個使用者，我們會協助立即保護該帳戶。 確認使用者識別為具有外洩的認證入侵。 這些使用者將無法登入，直到重設其密碼。

如果啟用的功能可在其目錄中，指派 Azure AD Premium 授權的使用者可以還原透過自助式密碼重設 (SSPR) 的存取。 不需要進階授權會被封鎖的使用者必須連絡系統管理員執行手動密碼重設及關閉已標幟的使用者風險事件。

### <a name="steps-to-unblock-a-user"></a>若要解除封鎖使用者的步驟

確認使用者已藉由檢查使用者的登入記錄封鎖原則。

1. 系統管理員必須登入**Azure 入口網站**並瀏覽至**Azure Active Directory** > **使用者**> 按一下使用者的名稱，然後瀏覽登入。
1. 若要起始密碼重設的已封鎖的使用者，系統管理員必須瀏覽至**Azure Active Directory** > **標示有風險的使用者**
1. 按一下以檢視使用者的最近登入活動的相關資訊會封鎖其帳戶的使用者。
1. 按一下 重設密碼將在下次登入時必須變更暫時密碼。
1. 按一下 關閉以重設使用者的風險分數的所有事件。

使用者現在可以登入、 重設其密碼，並存取應用程式。

## <a name="deployment-considerations"></a>部署考量

因為**終端使用者保護**原則會套用至您的目錄中的所有使用者，需要進行以確保順利部署的幾個考量。 這些考量包括用來識別使用者與 Azure ad 中無法或不應該執行 MFA，以及應用程式與您組織所使用的用戶端不支援新式驗證的服務主體。

### <a name="legacy-protocols"></a>舊版通訊協定

郵件用戶端會使用舊版驗證通訊協定 （IMAP、 SMTP、 POP3 等） 提出驗證要求。 這些通訊協定不支援 MFA。  大部分看過 microsoft 帳戶入侵的不良執行者執行嘗試略過 MFA 的舊版通訊協定攻擊所造成。 若要確保登入帳戶時，會需要 MFA，和不良執行者無法略過 MFA，此原則會封鎖舊版通訊協定從對系統管理員帳戶的所有驗證要求。

> [!WARNING]
> 啟用此原則之前，請確定您的使用者不會使用舊版驗證通訊協定。 請參閱文章[How to:區塊至 Azure AD 條件式存取的舊版驗證](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use)如需詳細資訊。

### <a name="user-exclusions"></a>使用者排除項目

此基準原則會提供選項來排除使用者。 之前為您的租用戶中啟用原則，建議您排除下列帳戶：

* **緊急存取**或是**急用**以防止租用戶的帳戶鎖定的帳戶。 在罕見的案例，所有的系統管理員會鎖定您的租用戶，您的系統管理緊急存取帳戶可用來登入租用戶採取步驟來復原存取。
   * 可以在本文中，找到更多資訊[在 Azure AD 中管理緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)。
* **服務帳戶**並**服務原則**，例如 Azure AD Connect 同步處理帳戶。 服務帳戶是不會繫結至任何特定使用者的非互動式帳戶。 它們通常由後端服務，並允許以程式設計方式存取應用程式。 服務帳戶應該排除，因為無法以程式設計方式完成 MFA。
   * 如果貴組織擁有這些帳戶在指令碼或程式碼中使用，請考慮更換它們 [受管理身分識別](../managed-identities-azure-resources/overview.md)。 暫時的解決方法，您可以從基準原則中排除這些特定的帳戶。
* 使用者不需要或不能使用智慧型手機。
   * 此原則會要求使用者註冊使用 Microsoft Authenticator 應用程式的 MFA。

## <a name="enable-the-baseline-policy"></a>啟用基準原則

原則**基準原則：終端使用者 protection （預覽）** 隨附預先設定，然後會出現在頂端當您瀏覽至 Azure 入口網站中的 [條件式存取] 刀鋒視窗。

若要啟用此原則，並保護您的使用者：

1. 登入 **Azure 入口網站** 為全域管理員、 安全性系統管理員或條件式存取系統管理員。
1. 瀏覽至**Azure Active Directory** > **條件式存取**。
1. 在原則清單中，選取 **基準原則：終端使用者 protection （預覽）** 。
1. 設定**啟用原則**要**立即使用原則**。
1. 新增任何使用者排除項目上即可**使用者** > **選取排除的使用者**，然後選擇要排除的使用者。 按一下 **選取** 再**完成**。
1. 按一下  **儲存**。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱

* [條件式存取基準保護原則](concept-baseline-protection.md)
* [可保護身分識別基礎結構的五個步驟](../../security/azure-ad-secure-steps.md)
* [什麼是 Azure Active Directory 中的條件式存取？](overview.md)
