---
title: 基準原則終端使用者保護 (預覽)-Azure Active Directory
description: 要求使用者使用多重要素驗證的條件式存取原則
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
ms.openlocfilehash: afcd9c9d3191caeabe182f499b5fd80cd8e1d8dd
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608145"
---
# <a name="baseline-policy-end-user-protection-preview"></a>基準原則:終端使用者保護 (預覽)

我們通常會將系統管理員帳戶視為唯一需要使用多重要素驗證 (MFA) 進行保護的帳戶。 系統管理員有廣泛的機密資訊存取權, 而且可以對全訂用帳戶的設定進行變更。 不過, 不良的執行者傾向于鎖定終端使用者。 取得存取權之後, 這些不良的執行者就可以代表原始帳戶持有者要求存取許可權資訊, 或下載整個目錄來執行整個組織的網路釣魚攻擊。 若要改善所有使用者的保護, 其中一個常見的方法是要求較強的帳戶驗證形式, 例如多重要素驗證 (MFA)。

為了達成合理的安全性和可用性平衡, 使用者在每次登入時都不應該收到提示。 反映一般使用者行為的驗證要求 (例如從相同位置登入相同的裝置), 會有較低的入侵機會。 只有被視為有風險的登入和顯示不良動作專案的特性, 才會出現 MFA 挑戰。

使用者保護是以風險為基礎的 MFA[基準原則](concept-baseline-protection.md), 可保護目錄中的所有使用者, 包括所有系統管理員角色。 若要啟用此原則, 所有使用者都必須使用驗證器應用程式註冊 MFA。 使用者可以略過14天的 MFA 登錄提示, 在這之後, 他們將會遭到封鎖而無法登入, 直到他們註冊 MFA 為止。 註冊 MFA 之後, 只有在有風險的登入嘗試時, 才會提示使用者進行 MFA。 遭盜用的使用者帳戶會遭到封鎖, 直到重設其密碼且已關閉風險事件為止。

> [!NOTE]
> 此原則適用于所有使用者, 包括來賓帳戶, 並會在登入所有應用程式時進行評估。

## <a name="recovering-compromised-accounts"></a>復原遭盜用的帳戶

為了協助保護客戶, Microsoft 流失的認證服務會尋找公開可用的使用者名稱/密碼組。 如果它們符合我們的其中一個使用者, 我們會立即協助保護該帳戶。 識別為有流失認證的使用者已確認遭到入侵。 這些使用者在重設密碼之前, 將會遭到封鎖而無法登入。

指派 Azure AD Premium 授權的使用者可以在其目錄中啟用功能的情況下, 透過自助式密碼重設 (SSPR) 來還原存取權。 沒有 premium 授權的使用者會遭到封鎖, 必須洽詢系統管理員以執行手動密碼重設, 並關閉已加上旗標的使用者風險事件。

### <a name="steps-to-unblock-a-user"></a>解除封鎖使用者的步驟

檢查使用者的登入記錄, 確認使用者已遭到原則封鎖。

1. 系統管理員必須登入**Azure 入口網站**並流覽至 [ **Azure Active Directory**  > **使用者**] > 按一下使用者的名稱, 然後流覽至 [登入]。
1. 若要起始已封鎖使用者的密碼重設, 系統管理員必須流覽  > 至**標示有風險的 Azure Active Directory 使用者**
1. 按一下已封鎖帳戶的使用者, 以查看使用者最近登入活動的相關資訊。
1. 按一下 [重設密碼], 指派必須在下次登入時變更的暫時密碼。
1. 按一下 [關閉所有事件] 以重設使用者的風險分數。

使用者現在可以登入、重設其密碼, 並存取應用程式。

## <a name="deployment-considerations"></a>部署考量

由於使用者**保護**原則會套用至目錄中的所有使用者, 因此需要進行幾項考慮以確保部署順利。 這些考慮包括識別 Azure AD 中無法或不應執行 MFA 的使用者和服務原則, 以及您的組織使用不支援新式驗證的應用程式和用戶端。

### <a name="legacy-protocols"></a>舊版通訊協定

郵件用戶端會使用舊版驗證通訊協定 (IMAP、SMTP、POP3 等等) 來提出驗證要求。 這些通訊協定不支援 MFA。  Microsoft 所看到的大部分帳戶危害, 都是由不良的執行者針對嘗試略過 MFA 的舊版通訊協定來執行攻擊所造成。 為確保登入帳戶時需要 MFA, 而不良的執行者無法略過 MFA, 此原則會封鎖從舊版通訊協定對系統管理員帳戶提出的所有驗證要求。

> [!WARNING]
> 啟用此原則之前, 請確定您的使用者未使用舊版驗證通訊協定。 請參閱下列[文章:使用條件式存取](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use)來封鎖 Azure AD 的舊版驗證以取得詳細資訊。

## <a name="enable-the-baseline-policy"></a>啟用基準原則

原則**基準原則:當您流覽至 Azure 入口網站中**的 [條件式存取] 分頁時, 系統會預先設定終端使用者保護 (預覽), 並顯示在頂端。

若要啟用此原則並保護您的使用者:

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入 **Azure 入口網站** 。
1. 流覽至**Azure Active Directory**  > **條件式存取**。
1. 在原則清單中, 選取 **[基準原則]:終端使用者保護 (預覽)** 。
1. 將 [**啟用原則**] 設定為**立即使用原則**。
1. 按一下 [ **儲存**]。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱：

* [條件式存取基準保護原則](concept-baseline-protection.md)
* [可保護身分識別基礎結構的五個步驟](../../security/fundamentals/steps-secure-identity.md)
* [什麼是 Azure Active Directory 中的條件式存取？](overview.md)
