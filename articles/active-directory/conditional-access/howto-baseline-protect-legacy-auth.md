---
title: 基準原則封鎖舊版驗證 (預覽)-Azure Active Directory
description: 封鎖舊版驗證通訊協定的條件式存取原則
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
ms.openlocfilehash: 413df402d5acc878130a47821efd5fa3a512257d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931944"
---
# <a name="baseline-policy-block-legacy-authentication-preview"></a>基準原則:封鎖舊版驗證 (預覽)

為了讓您的使用者能夠輕鬆存取雲端應用程式，Azure Active Directory (Azure AD) 支援多種驗證通訊協定，包括舊式驗證。 舊版驗證是指由下列各項所提出的驗證要求:

* 不使用新式驗證的舊版 Office 用戶端 (例如, Office 2010 用戶端)
* 任何使用舊版郵件通訊協定的用戶端, 例如 IMAP/SMTP/POP3

現今, 所有危害的登入嘗試都是來自舊版驗證。 舊版驗證不支援多重要素驗證 (MFA)。 即使您的目錄已啟用 MFA 原則, 不良的執行者仍可使用舊版通訊協定進行驗證, 並略過 MFA。

保護您的帳戶免于舊版通訊協定所提出之惡意驗證要求的最佳方式, 就是完全封鎖這些嘗試。 為了讓您更輕鬆地保護您的環境, 我們建立了此基準原則來封鎖舊版驗證。

「**封鎖舊版驗證**」是一種[基準原則](concept-baseline-protection.md), 會封鎖從舊版通訊協定所發出的所有驗證要求。 必須使用新式驗證才能成功登入所有使用者。 與其他基準原則一起使用時, 所有來自舊版通訊協定的要求都會遭到封鎖, 且所有使用者都必須在需要時進行 MFA。 此原則不會封鎖 Exchange ActiveSync。

## <a name="identify-legacy-authentication-use"></a>識別舊版驗證使用

您必須先瞭解您的使用者是否有使用舊版驗證的應用程式, 以及它如何影響您的整體目錄, 才可以在目錄中封鎖舊版驗證。 Azure AD 登入記錄可以用來瞭解您是否使用舊版驗證。

1. 流覽至**Azure 入口網站** >  **Azure Active Directory**  > 登**入**。
1. 新增 [用戶端應用程式] 欄 (如果未顯示, 請按一下 [資料**行** > ] [**用戶端應用程式**])
1. **新增篩選** > **用戶端應用程式**> 選取**其他用戶端**的所有選項,然後按一下 [套用]。

篩選只會顯示舊版驗證通訊協定所進行的登入嘗試。 按一下每個個別的登入嘗試, 將會顯示其他詳細資料。 [**基本資訊**] 索引標籤下的 [**用戶端應用程式**] 欄位會指出所使用的舊版驗證通訊協定。

這些記錄會指出哪些使用者仍會根據傳統驗證, 以及哪些應用程式使用舊版通訊協定來提出驗證要求。 對於不會出現在這些記錄中且已確認不會使用舊版驗證的使用者, 請執行條件式存取原則, 或啟用基準原則: 僅針對這些使用者**封鎖舊版驗證**。

## <a name="moving-away-from-legacy-authentication"></a>離開舊版驗證

當您更瞭解誰在您的目錄中使用舊版驗證, 以及哪些應用程式依賴它時, 下一步就是將您的使用者升級為使用新式驗證。 新式驗證是身分識別管理的一種方法, 可提供更安全的使用者驗證和授權。 如果您的目錄已有 MFA 原則, 新式驗證可確保使用者在需要時收到 MFA 的提示。 這是舊版驗證通訊協定更安全的替代方式。

本節提供如何將您的環境更新為新式驗證的逐步概述。 在您的組織中啟用舊版驗證封鎖原則之前, 請先閱讀下列步驟。

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>步驟 1:在您的目錄中啟用新式驗證

啟用新式驗證的第一個步驟, 是確定您的目錄支援新式驗證。 2017年8月1日當天或之後所建立的目錄, 預設會啟用新式驗證。 如果您的目錄是在此日期之前建立的, 您必須使用下列步驟, 為您的目錄手動啟用新式驗證:

1. `Get-CsOAuthConfiguration`從[商務用 Skype Online PowerShell 模組](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell)執行, 以查看您的目錄是否已支援新式驗證。
1. 如果您的命令傳回空`OAuthServers`的屬性, 則會停用新式驗證。 使用`Set-CsOAuthConfiguration`來更新設定, 以啟用新式驗證。 如果您`OAuthServers`的屬性包含專案, 您就可以開始使用了。

請務必先完成此步驟, 再繼續進行。 請務必先變更目錄設定, 因為這些設定會規定所有 Office 用戶端將使用的通訊協定。 即使您使用的是支援新式驗證的 Office 用戶端, 如果您的目錄上已停用新式驗證, 則會預設為使用舊版通訊協定。

### <a name="step-2-office-applications"></a>步驟 2:Office 應用程式

在您的目錄中啟用新式驗證之後, 您就可以藉由啟用 Office 用戶端的新式驗證來開始更新應用程式。 根據預設, Office 2016 或更新版本的用戶端支援新式驗證。 不需要額外的步驟。

如果您使用的是 Office 2013 Windows 用戶端或更舊版本, 建議您升級到 Office 2016 或更新版本。 即使在完成在目錄中啟用新式驗證的先前步驟之後, 舊版 Office 應用程式仍會繼續使用舊版驗證通訊協定。 如果您使用的是 Office 2013 用戶端, 而且無法立即升級為 Office 2016 或更新版本, 請遵循下列文章中的步驟,[在 Windows 裝置上啟用 Office 2013 的新式驗證](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication)。 若要在使用傳統驗證時協助保護您的帳戶, 建議您在目錄中使用強式密碼。 查看[Azure AD 密碼保護](../authentication/concept-password-ban-bad.md), 以禁止在您的目錄中進行弱式密碼。

Office 2010 不支援新式驗證。 您必須將任何使用 Office 2010 的使用者升級至較新版本的 Office。 我們建議您升級至 Office 2016 或更新版本, 因為它預設會封鎖舊版驗證。

如果您使用 MacOS, 建議您升級至適用于 Mac 2016 或更新版本的 Office。 如果您使用原生郵件用戶端, 則在所有裝置上都必須有 MacOS 10.14 版或更新版本。

### <a name="step-3-exchange-and-sharepoint"></a>步驟 3：Exchange 和 SharePoint

對於以 Windows 為基礎的 Outlook 用戶端若要使用新式驗證, Exchange Online 也必須啟用新式驗證。 如果已停用 Exchange Online 的新式驗證, 支援新式驗證的 Windows 型 Outlook 用戶端 (Outlook 2013 或更新版本) 會使用基本驗證來連線至 Exchange Online 信箱。

SharePoint Online 已啟用新式驗證預設值。 針對2017年8月1日之後建立的目錄, Exchange Online 預設會啟用新式驗證。 不過, 如果您先前已停用新式驗證, 或使用在此日期之前建立的目錄, 請遵循下列文章中的步驟,[在 Exchange Online 中啟用新式驗證](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)。

### <a name="step-4-skype-for-business"></a>步驟 4：商務用 Skype

若要防止商務用 Skype 提出的舊版驗證要求, 必須啟用商務用 Skype Online 的新式驗證。 針對2017年8月1日之後建立的目錄, 預設會啟用商務用 Skype 的新式驗證。

我們建議您轉換至 Microsoft 小組, 其預設支援新式驗證。 不過, 如果您目前無法遷移, 則必須啟用商務用 Skype Online 的新式驗證, 商務用 Skype 用戶端才會開始使用新式驗證。 請依照這篇文章中的步驟,[使用新式驗證支援的商務拓朴](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported), 啟用商務用 Skype 的新式驗證。

除了針對商務用 Skype Online 啟用新式驗證之外, 我們也建議在啟用商務用 Skype 的新式驗證時, 啟用 Exchange Online 的新式驗證。 此程式將有助於同步處理 Exchange Online 和商務用 Skype online 的新式驗證狀態, 並防止商務用 Skype 用戶端有多個登入提示。

### <a name="step-5-using-mobile-devices"></a>步驟 5：使用行動裝置

您的行動裝置上的應用程式也必須封鎖舊版驗證。 我們建議使用 Outlook for Mobile。 適用于 Mobile 的 Outlook 預設支援新式驗證, 並將滿足其他 MFA 基準保護原則。

若要使用原生 iOS 郵件用戶端, 您必須執行 iOS 11.0 版或更新版本, 以確保郵件用戶端已更新為封鎖舊版驗證。

### <a name="step-6-on-premises-clients"></a>步驟 6：內部部署用戶端

如果您是使用內部部署 Exchange Server 和商務用 Skype 內部部署的混合式客戶, 這兩項服務都必須更新以啟用新式驗證。 在混合式環境中使用新式驗證時, 您仍在驗證內部部署使用者。 授權其存取資源 (檔案或電子郵件) 變更的案例。

在您開始啟用內部部署的新式驗證之前, 請確定您已符合先決條件。
您現在已經準備好啟用內部部署的新式驗證。

您可以在下列文章中找到啟用新式驗證的步驟:

* [如何設定 Exchange Server 內部部署以使用混合式新式驗證](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [如何搭配使用新式驗證 (ADAL) 與商務用 Skype](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="enable-the-baseline-policy"></a>啟用基準原則

原則**基準原則:[封鎖舊版驗證 (預覽** )] 已預先設定, 當您流覽至 Azure 入口網站中的 [條件式存取] 分頁時, 會顯示在頂端。 此設定只會在成功登入之後生效, 因此使用者仍然可以選擇嘗試使用舊版驗證。

若要啟用此原則並保護您的組織:

1. 以全域管理員、安全性系統管理員或條件式存取系統管理員的身分登入 **Azure 入口網站** 。
1. 流覽至**Azure Active Directory**  > **條件式存取**。
1. 在原則清單中, 選取 **[基準原則]:封鎖舊版驗證 (預覽)** 。
1. 將 [**啟用原則**] 設定為**立即使用原則**。
1. 按一下 [ **儲存**]。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱：

* [條件式存取基準保護原則](concept-baseline-protection.md)
* [可保護身分識別基礎結構的五個步驟](../../security/fundamentals/steps-secure-identity.md)
* [什麼是 Azure Active Directory 中的條件式存取？](overview.md)
