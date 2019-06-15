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
ms.openlocfilehash: 4f8b7f281ad5ed8424110696544ffdb49e50ce59
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112433"
---
# <a name="baseline-policy-require-mfa-for-admins"></a>基準原則：管理員需要進行 MFA

有權存取特殊權限帳戶的使用者可以自由存取您的環境。 這些帳戶具有強大權力，您應特別小心處理。 改善特殊權限帳戶保護的常見方法之一，就是在帳戶用於登入時要求更強大的帳戶驗證形式。 在 Azure Active Directory 中，要求多重要素驗證 (MFA)，即可取得比較強大的帳戶驗證。

**適用於系統管理員要求 MFA** 是[基準原則](concept-baseline-protection.md)每次下列特殊權限的系統管理員角色的其中一個登入時需要 MFA:

* 全域管理員
* SharePoint 管理員
* Exchange 系統管理員
* 條件式存取系統管理員
* 安全性系統管理員
* 技術支援中心管理員 / 密碼管理員
* 計費管理員
* 使用者管理員

在啟用系統管理員原則需要 MFA，上述的九個的系統管理員角色必須註冊 MFA 使用驗證器應用程式。 MFA 註冊完成之後，系統管理員必須執行 MFA 每次登入。

![系統管理員 」 的基準原則需要 MFA](./media/howto-baseline-protect-administrators/baseline-policy-require-mfa-for-admins.png)

## <a name="deployment-considerations"></a>部署考量

因為**需要系統管理員的 MFA**原則會套用至所有重要的系統管理員，數個考量必須對確保順利部署。 這些考量包括用來識別使用者與 Azure ad 中無法或不應該執行 MFA，以及應用程式與您組織所使用的用戶端不支援新式驗證的服務主體。

### <a name="legacy-protocols"></a>舊版通訊協定

郵件用戶端會使用舊版驗證通訊協定 （IMAP、 SMTP、 POP3 等） 提出驗證要求。 這些通訊協定不支援 MFA。 大部分看過 microsoft 帳戶入侵的不良執行者執行嘗試略過 MFA 的舊版通訊協定攻擊所造成。 若要確保登入系統管理帳戶時，會需要 MFA，和不良執行者無法略過 MFA，此原則會封鎖舊版通訊協定從對系統管理員帳戶的所有驗證要求。

> [!WARNING]
> 啟用此原則之前，請確定您的系統管理員不使用舊版驗證通訊協定。 請參閱文章[How to:區塊至 Azure AD 條件式存取的舊版驗證](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use)如需詳細資訊。

### <a name="user-exclusions"></a>使用者排除項目

此基準原則會提供選項來排除使用者。 之前為您的租用戶中啟用原則，建議您排除下列帳戶：

* **緊急存取**或是**急用**以防止租用戶的帳戶鎖定的帳戶。 在罕見的案例，所有的系統管理員會鎖定您的租用戶，您的系統管理緊急存取帳戶可用來登入租用戶採取步驟來復原存取。
   * 可以在本文中，找到更多資訊[在 Azure AD 中管理緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)。
* **服務帳戶**並**服務原則**，例如 Azure AD Connect 同步處理帳戶。 服務帳戶是不會繫結至任何特定使用者的非互動式帳戶。 它們通常由後端服務，並允許以程式設計方式存取應用程式。 服務帳戶應該排除，因為無法以程式設計方式完成 MFA。
   * 如果貴組織擁有這些帳戶在指令碼或程式碼中使用，請考慮更換它們 [受管理身分識別](../managed-identities-azure-resources/overview.md)。 暫時的解決方法，您可以從基準原則中排除這些特定的帳戶。
* 使用者不需要或不能使用智慧型手機。
   * 此原則會要求系統管理員註冊使用 Microsoft Authenticator 應用程式的 MFA。

## <a name="enable-the-baseline-policy"></a>啟用基準原則

原則**基準原則：適用於系統管理員要求 MFA**隨附預先設定，然後會出現在頂端當您瀏覽至 Azure 入口網站中的 [條件式存取] 刀鋒視窗。

若要啟用此原則，並保護您的系統管理員：

1. 登入 **Azure 入口網站** 為全域管理員、 安全性系統管理員或條件式存取系統管理員。
1. 瀏覽至**Azure Active Directory** > **條件式存取**。
1. 在原則清單中，選取 **基準原則：適用於系統管理員要求 MFA**。
1. 設定**啟用原則**要**立即使用原則**。
1. 新增任何使用者排除項目上即可**使用者** > **選取排除的使用者**，然後選擇要排除的使用者。 按一下 **選取** 再**完成**。
1. 按一下  **儲存**。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱

* [條件式存取基準保護原則](concept-baseline-protection.md)
* [可保護身分識別基礎結構的五個步驟](../../security/azure-ad-secure-steps.md)
* [什麼是 Azure Active Directory 中的條件式存取？](overview.md)
