---
title: 條件式存取基準保護原則-Azure Active Directory
description: 基準條件式存取原則, 以保護組織免于遭受常見的攻擊
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
ms.openlocfilehash: c2fc552211ed55239259ce84d84584c451733d70
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499864"
---
# <a name="what-are-baseline-policies"></a>什麼是基準原則？

基準原則是一組預先定義的原則, 可協助組織抵禦許多常見的攻擊。 這些常見的攻擊可能包括密碼噴灑、重新執行和網路釣魚。 基準原則適用于所有版本的 Azure AD。 Microsoft 將這些基準保護原則提供給所有人, 因為以身分識別為基礎的攻擊已在過去幾年的增加。 這四個原則的目標是要確保所有組織都已啟用基準層級的安全性, 而不需要額外成本。  

管理自訂的條件式存取原則需要 Azure AD Premium 授權。

## <a name="baseline-policies"></a>基準原則

![Azure 入口網站中的條件式存取基準原則](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

組織可以啟用四個基準原則:

* [需要系統管理員的 MFA (預覽)](howto-baseline-protect-administrators.md)
* [終端使用者保護 (預覽)](howto-baseline-protect-end-users.md)
* [封鎖舊版驗證 (預覽)](howto-baseline-protect-legacy-auth.md)
* [服務管理需要 MFA (預覽)](howto-baseline-protect-azure.md)

這四種原則都會影響舊版的驗證流程, 例如 POP、IMAP 和較舊的 Office 桌面用戶端。

### <a name="require-mfa-for-admins-preview"></a>需要系統管理員的 MFA (預覽)

由於系統管理員帳戶擁有的能力和存取權, 因此您應該特別小心對待它們。 若要改善特殊許可權帳戶的保護, 其中一個常見的方法是在用來登入時需要更強大的帳戶驗證形式。 在 Azure Active Directory 中, 您可以要求系統管理員註冊並使用 Azure 多重要素驗證, 以取得更強的帳戶驗證。

[[要求系統管理員使用 mfa (預覽)](howto-baseline-protect-administrators.md)  ] 是一項基準原則, 其需要下列目錄角色的多重要素驗證 (MFA), 視為最具許可權的 Azure AD 角色:

* 全域管理員
* SharePoint 管理員
* Exchange 系統管理員
* 條件式存取管理員
* 安全性系統管理員
* 服務台管理員/密碼管理員
* 計費管理員
* 使用者系統管理員

如果您的組織在腳本或程式碼中使用這些帳戶, 請考慮將它們取代為 [受控](../managed-identities-azure-resources/overview.md)識別。

### <a name="end-user-protection-preview"></a>終端使用者保護 (預覽)

高特殊許可權的系統管理員並不是攻擊的唯一目標。 不良執行者通常會以一般使用者為目標。 取得存取權之後, 這些不良的執行者就可以代表原始帳戶持有者要求存取許可權資訊, 或下載整個目錄並在整個組織中執行網路釣魚攻擊。 有一個改善所有使用者保護的常見方法, 就是在偵測到有風險的登入時, 需要更強大的帳戶驗證形式。

**終端使用者保護 (預覽)** 是保護目錄中所有使用者的基準原則。 若要啟用此原則, 所有使用者都必須在14天內註冊 Azure 多重要素驗證。 註冊之後, 只有在有風險的登入嘗試時, 才會提示使用者進行 MFA。 遭盜用的使用者帳戶會遭到封鎖, 直到重設密碼和風險關閉為止。 

[!NOTE]
先前標示為有風險的任何使用者都會遭到封鎖, 直到發生密碼重設和在原則啟用關閉的風險為止。

### <a name="block-legacy-authentication-preview"></a>封鎖舊版驗證 (預覽)

舊版驗證通訊協定 (例如:IMAP、SMTP、POP3) 是通常由較舊的郵件用戶端用來進行驗證的通訊協定。 舊版通訊協定不支援多重要素驗證。 即使您的原則要求您的目錄需要多重要素驗證, 不良的執行者還是可以使用其中一種舊版通訊協定進行驗證, 並略過多重要素驗證。

保護您的帳戶免于舊版通訊協定所提出之惡意驗證要求的最佳方式, 就是封鎖它們。

「**封鎖舊版驗證」 (預覽)** 基準原則會封鎖使用舊版通訊協定所提出的驗證要求。 必須使用新式驗證才能成功登入所有使用者。 與其他基準原則一起使用時, 將會封鎖來自舊版通訊協定的要求。 此外, 所有使用者都需要在需要時進行 MFA。 此原則不會封鎖 Exchange ActiveSync。

### <a name="require-mfa-for-service-management-preview"></a>服務管理需要 MFA (預覽)

組織會使用各種不同的 Azure 服務, 並從以 Azure Resource Manager 為基礎的工具進行管理, 例如:

* Azure 入口網站
* Azure PowerShell
* Azure CLI

使用上述任何工具來執行資源管理是一項具有高度許可權的動作。 這些工具可以改變全訂用帳戶的設定, 例如服務設定和訂用帳戶計費。

為了保護特殊許可權動作, 這**需要服務管理 (預覽)** 原則的 MFA, 會要求任何存取 Azure 入口網站、Azure PowerShell 或 Azure CLI 的使用者進行多重要素驗證。

## <a name="enable-a-baseline-policy"></a>啟用基準原則

若要啟用基準原則:

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入 **Azure 入口網站** 。
1. 流覽至**Azure Active Directory**  > **條件式存取**。
1. 在原則清單中, 選取您想要啟用的基準原則。
1. 將 [**啟用原則**] 設定為 [**開啟**]。
1. 按一下 [儲存]。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱：

* [可保護身分識別基礎結構的五個步驟](../../security/azure-ad-secure-steps.md)
* [什麼是 Azure Active Directory 中的條件式存取？](overview.md)
* [需要系統管理員的 MFA (預覽)](howto-baseline-protect-administrators.md)
* [終端使用者保護 (預覽)](howto-baseline-protect-end-users.md)
* [封鎖舊版驗證 (預覽)](howto-baseline-protect-legacy-auth.md)
* [服務管理需要 MFA (預覽)](howto-baseline-protect-azure.md)
