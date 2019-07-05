---
title: 基準原則需要 MFA，系統管理員-Azure Active Directory
description: 條件式存取原則，以系統管理員需要多重要素驗證
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
ms.openlocfilehash: 4474283b9a233e39497cd05f0f04ea0984f02401
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560942"
---
# <a name="baseline-policy-require-mfa-for-admins-preview"></a>基準原則：適用於系統管理員 （預覽） 需要 MFA

有權存取特殊權限帳戶的使用者可以自由存取您的環境。 這些帳戶具有強大權力，您應特別小心處理。 改善特殊權限帳戶保護的常見方法之一，就是在帳戶用於登入時要求更強大的帳戶驗證形式。 在 Azure Active Directory 中，要求多重要素驗證 (MFA)，即可取得比較強大的帳戶驗證。

**適用於系統管理員 （預覽） 要求 MFA** 是[基準原則](concept-baseline-protection.md)每次下列特殊權限的系統管理員角色的其中一個登入時需要 MFA:

* 全域管理員
* SharePoint 管理員
* Exchange 系統管理員
* 條件式存取系統管理員
* 安全性系統管理員
* 技術支援中心管理員 / 密碼管理員
* 計費管理員
* 使用者管理員

在啟用系統管理員原則需要 MFA，上述的九個的系統管理員角色必須註冊 MFA 使用驗證器應用程式。 MFA 註冊完成之後，系統管理員必須執行 MFA 每次登入。

## <a name="deployment-considerations"></a>部署考量

因為**適用於系統管理員 （預覽） 的要求 MFA**原則會套用至所有重要的系統管理員，數個考量必須對確保順利部署。 這些考量包括用來識別使用者與 Azure ad 中無法或不應該執行 MFA，以及應用程式與您組織所使用的用戶端不支援新式驗證的服務主體。

### <a name="legacy-protocols"></a>舊版通訊協定

郵件用戶端會使用舊版驗證通訊協定 （IMAP、 SMTP、 POP3 等） 提出驗證要求。 這些通訊協定不支援 MFA。 大部分看過 microsoft 帳戶入侵的不良執行者執行嘗試略過 MFA 的舊版通訊協定攻擊所造成。 若要確保登入系統管理帳戶時，會需要 MFA，和不良執行者無法略過 MFA，此原則會封鎖舊版通訊協定從對系統管理員帳戶的所有驗證要求。

> [!WARNING]
> 啟用此原則之前，請確定您的系統管理員不使用舊版驗證通訊協定。 請參閱文章[How to:區塊至 Azure AD 條件式存取的舊版驗證](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use)如需詳細資訊。

## <a name="enable-the-baseline-policy"></a>啟用基準原則

原則**基準原則：適用於系統管理員 （預覽） 要求 MFA**隨附預先設定，然後會出現在頂端當您瀏覽至 Azure 入口網站中的 [條件式存取] 刀鋒視窗。

若要啟用此原則，並保護您的系統管理員：

1. 登入 **Azure 入口網站** 為全域管理員、 安全性系統管理員或條件式存取系統管理員。
1. 瀏覽至**Azure Active Directory** > **條件式存取**。
1. 在原則清單中，選取 **基準原則：適用於系統管理員 （預覽） 要求 MFA**。
1. 設定**啟用原則**要**立即使用原則**。
1. 按一下  **儲存**。

> [!WARNING]
> 沒有選項**自動在未來啟用原則**此原則處於預覽狀態。 我們已移除此選項以突然使用者的影響降到最低。 如果您選取此選項時，**不使用原則**會自動現在已選取。 如果他們想要使用這個基準原則，請參閱上述步驟來啟用它。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱

* [條件式存取基準保護原則](concept-baseline-protection.md)
* [可保護身分識別基礎結構的五個步驟](../../security/azure-ad-secure-steps.md)
* [什麼是 Azure Active Directory 中的條件式存取？](overview.md)
