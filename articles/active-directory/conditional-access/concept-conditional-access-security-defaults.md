---
title: Azure Active Directory 安全性預設值
description: 安全性預設原則，專門用來保護組織免于遭受常見的攻擊
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4921f0605f16a601f6e2ee9a15b71b50d253201
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72453039"
---
# <a name="what-are-security-defaults"></a>什麼是安全性預設值？

當常見的身分識別相關攻擊（例如密碼噴灑、重新執行和網路釣魚）變得越來越熱門時，管理安全性可能會很棘手。 建立更簡單的方式，讓您的組織更安全地受到這些常見的攻擊，就是 Azure Active Directory （AD）中安全性預設的目標。 安全性預設值可讓您更輕鬆地保護組織免于受到常見的攻擊。 安全性預設值包含這些常見攻擊的預先設定安全性設定。 Microsoft 將安全性預設值提供給所有人。 目標是要確保所有組織都已啟用基本層級的安全性，而不需要額外成本。

![新安全性預設 UX 的螢幕擷取畫面](./media/concept-conditional-access-security-defaults/security-defaults-azure-ad-portal.png)
 
系統會在您的租使用者中開啟下列安全性設定。 

## <a name="unified-mfa-registration"></a>整合 MFA 註冊

租使用者中的所有使用者都需要註冊 Azure 多重要素驗證（MFA）。 使用者會有14天的時間，可以使用 Microsoft Authenticator 應用程式註冊 Azure MFA。 經過14天之後，使用者將無法登入，直到 MFA 註冊完成為止。

我們瞭解某些使用者可能不在辦公室，而且（或）在啟用安全性預設值之後的14天內將不會登入。 為確保每位使用者有充足的時間來註冊 MFA，每個使用者的14天期間都是唯一的。 一旦啟用安全性預設值，使用者的14天期間就會在第一次成功的互動式登入之後開始。

## <a name="mfa-enforcement"></a>MFA 強制執行

### <a name="protecting-administrators"></a>保護系統管理員

具有特殊許可權帳戶存取權的使用者，可以提高您環境的存取權。 這些帳戶具有強大權力，您應特別小心處理。 改善特殊權限帳戶保護的常見方法之一，就是在帳戶用於登入時要求更強大的帳戶驗證形式。 在 Azure Active Directory 中，您可以藉由要求多重要素驗證，來取得更強的帳戶驗證。

一旦完成 MFA 註冊，下列九個 Azure AD 系統管理員角色就必須在每次登入時執行 MFA。

- 全域管理員
- SharePoint 管理員
- Exchange 系統管理員
- 條件式存取系統管理員
- 安全性系統管理員
- 服務台管理員/密碼管理員
- 計費管理員
- 使用者管理員
- 驗證系統管理員

### <a name="protecting-all-users"></a>保護所有使用者

我們通常會將系統管理員帳戶視為唯一需要使用多重要素驗證（MFA）進行保護的帳戶。 系統管理員有廣泛的機密資訊存取權，而且可以對全訂用帳戶的設定進行變更。 不過，不良的執行者傾向于鎖定終端使用者。 取得存取權之後，這些不良的執行者就可以代表原始帳戶持有者要求存取許可權資訊，或下載整個目錄來執行整個組織的網路釣魚攻擊。 改善所有使用者之保護的常見方法之一，就是需要更強的帳戶驗證形式，例如適用于每個人的多重要素驗證（MFA）。 完成 MFA 註冊之後，系統會在必要時提示使用者進行 MFA。

### <a name="blocking-legacy-authentication"></a>封鎖舊版驗證

為了讓您的使用者能夠輕鬆存取雲端應用程式，Azure Active Directory (Azure AD) 支援多種驗證通訊協定，包括舊式驗證。 舊版驗證是指由下列各項所提出的驗證要求：

- 不使用新式驗證的舊版 Office 用戶端（例如，Office 2010 用戶端）
- 任何使用舊版郵件通訊協定的用戶端，例如 IMAP/SMTP/POP3

現今，所有危害的登入嘗試都是來自舊版驗證。 舊版驗證不支援多重要素驗證（MFA）。 即使您的目錄已啟用 MFA 原則，不良的執行者仍可使用舊版通訊協定進行驗證，並略過 MFA。 一旦在您的租使用者中啟用安全性預設值，舊版通訊協定對任何的驗證要求將會遭到封鎖。 安全性預設值不會封鎖 Exchange ActiveSync。

### <a name="protecting-privileged-actions"></a>保護特殊許可權動作

組織會使用透過 Azure Resource Manager API 管理的各種 Azure 服務，包括：

- Azure Portal 
- Azure PowerShell 
- Azure CLI

使用 Azure Resource Manager 來管理您的服務是具有高許可權的動作。 Azure Resource Manager 可以改變整個租使用者的設定，例如服務設定和訂用帳戶計費。 單一要素驗證容易遭受各種攻擊，例如網路釣魚和密碼噴灑。 因此，請務必確認想要存取 Azure Resource Manager 和更新設定的使用者身分識別，方法是要求多重要素驗證，然後才允許存取。

一旦在您的租使用者中啟用安全性預設值，任何存取 Azure 入口網站、Azure PowerShell 或 Azure CLI 的使用者，都將需要完成多重要素驗證。 此原則適用于所有存取 Azure Resource Manager 的使用者，不論他們是系統管理員或使用者。 如果使用者未註冊 MFA，使用者將需要使用 Microsoft Authenticator 應用程式進行註冊，才能繼續進行。 不會提供14天的 MFA 註冊期間。

## <a name="deployment-considerations"></a>部署考量

以下是有關部署租使用者安全性預設值的一些其他考慮。

### <a name="legacy-protocols"></a>舊版通訊協定

郵件用戶端會使用舊版驗證通訊協定（IMAP、SMTP、POP3 等等）來提出驗證要求。 這些通訊協定不支援 MFA。 Microsoft 所看到的大部分帳戶危害，都是由不良的執行者針對嘗試略過 MFA 的舊版通訊協定來執行攻擊所造成。 為確保登入系統管理帳戶時需要 MFA，而不良的執行者無法略過 MFA，安全性預設值會封鎖從舊版通訊協定對系統管理員帳戶發出的所有驗證要求。

> [!WARNING]
> 啟用此設定之前，請確定您的系統管理員未使用舊版驗證通訊協定。 如需詳細資訊，請參閱 [如何移出舊版驗證](concept-conditional-access-block-legacy-authentication.md)一文。

### <a name="conditional-access"></a>條件式存取

條件式存取可以用來設定原則，以提供安全性預設值所啟用的相同行為。 如果您使用條件式存取，並在您的環境中啟用條件式存取原則，將無法使用安全性預設值。 如果您的授權提供條件式存取，但未在您的環境中啟用任何條件式存取原則，您就可以使用安全性預設值，直到您啟用 CA 原則為止。

![安全性預設值或條件式存取不是兩者](./media/concept-conditional-access-security-defaults/security-defaults-conditional-access.png)

以下是如何使用條件式存取來設定對等原則的逐步指南：

- [系統管理員需要 MFA](howto-conditional-access-policy-admin-mfa.md)
- [Azure 管理需要 MFA](howto-conditional-access-policy-azure-management.md)
- [封鎖舊版驗證](howto-conditional-access-policy-block-legacy.md)

## <a name="enabling-security-defaults"></a>啟用安全性預設值

若要在您的目錄中啟用安全性預設值：

1. 登入 [Azure 入口網站](https://portal.azure.com) as 安全性系統管理員、條件式存取系統管理員或全域管理員。
1. 流覽至 **Azure Active Directory**  @ no__t-2 **屬性**
1. 選取 [**管理安全性預設值**]
1. 將 [**啟用安全性預設值**] 切換為 **[是]** 。
1. 按一下 [儲存]。

## <a name="next-steps"></a>後續步驟

[一般條件式存取原則](concept-conditional-access-policy-common.md)

[什麼是條件式存取？](overview.md)
