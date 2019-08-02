---
title: 基準原則需要適用于系統管理員的 MFA-Azure Active Directory
description: 要求系統管理員使用多重要素驗證的條件式存取原則
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
ms.openlocfilehash: 213540a5b6c77146155365133f2cca08eea25351
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608152"
---
# <a name="baseline-policy-require-mfa-for-admins-preview"></a>基準原則:需要系統管理員的 MFA (預覽)

有權存取特殊權限帳戶的使用者可以自由存取您的環境。 這些帳戶具有強大權力，您應特別小心處理。 改善特殊權限帳戶保護的常見方法之一，就是在帳戶用於登入時要求更強大的帳戶驗證形式。 在 Azure Active Directory 中，要求多重要素驗證 (MFA)，即可取得比較強大的帳戶驗證。

**[要求系統管理員使用 mfa (預覽)**  ] 是一種[基準原則](concept-baseline-protection.md), 需要在每次有下列特殊許可權管理員角色登入時才進行 mfa:

* 全域管理員
* SharePoint 管理員
* Exchange 系統管理員
* 條件式存取管理員
* 安全性系統管理員
* 服務台管理員/密碼管理員
* 計費管理員
* 使用者系統管理員

啟用 [需要 MFA for admins] 原則時, 必須使用驗證器應用程式註冊 MFA, 以上九個系統管理員角色才會需要。 MFA 註冊完成後, 系統管理員每次登入時都必須執行 MFA。

## <a name="deployment-considerations"></a>部署考量

由於 [**要求系統管理員的 MFA (預覽)** ] 原則適用于所有重要的系統管理員, 因此需要進行幾項考慮以確保部署順利。 這些考慮包括識別 Azure AD 中無法或不應執行 MFA 的使用者和服務原則, 以及您的組織使用不支援新式驗證的應用程式和用戶端。

### <a name="legacy-protocols"></a>舊版通訊協定

郵件用戶端會使用舊版驗證通訊協定 (IMAP、SMTP、POP3 等等) 來提出驗證要求。 這些通訊協定不支援 MFA。 Microsoft 所看到的大部分帳戶危害, 都是由不良的執行者針對嘗試略過 MFA 的舊版通訊協定來執行攻擊所造成。 為確保登入系統管理帳戶時需要 MFA, 而不良的執行者無法略過 MFA, 此原則會封鎖從舊版通訊協定對系統管理員帳戶提出的所有驗證要求。

> [!WARNING]
> 啟用此原則之前, 請確定您的系統管理員未使用舊版驗證通訊協定。 請參閱下列[文章:使用條件式存取](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use)來封鎖 Azure AD 的舊版驗證以取得詳細資訊。

## <a name="enable-the-baseline-policy"></a>啟用基準原則

原則**基準原則:[要求系統管理員使用 MFA (** 預覽)] 已預先設定, 而且當您流覽至 Azure 入口網站中的 [條件式存取] 分頁時, 會顯示在頂端。

若要啟用此原則並保護您的系統管理員:

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入 **Azure 入口網站** 。
1. 流覽至**Azure Active Directory**  > **條件式存取**。
1. 在原則清單中, 選取 **[基準原則]:需要系統管理員的 MFA (預覽**)。
1. 將 [**啟用原則**] 設定為**立即使用原則**。
1. 按一下 [ **儲存**]。

> [!WARNING]
> 當此原則為預覽狀態時, 有一個選項**會自動啟用原則**。 我們已移除此選項, 以將突然的使用者影響降至最低。 如果您在可用時選取此選項, 則 [不要**使用原則**] 現在會自動選取。 如果他們想要使用此基準原則, 請參閱上述步驟以加以啟用。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱：

* [條件式存取基準保護原則](concept-baseline-protection.md)
* [可保護身分識別基礎結構的五個步驟](../../security/fundamentals/steps-secure-identity.md)
* [什麼是 Azure Active Directory 中的條件式存取？](overview.md)
