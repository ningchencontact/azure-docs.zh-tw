---
title: 基準原則區塊舊有的驗證 （預覽）-Azure Active Directory
description: 條件式存取原則，來封鎖舊版驗證通訊協定
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
ms.openlocfilehash: e55bf4a66dcd8c1c7cfbc881d6f93c3a12f5dc00
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112307"
---
# <a name="baseline-policy-block-legacy-authentication-preview"></a>基準原則：區塊舊有的驗證 （預覽）

為了讓您的使用者能夠輕鬆存取雲端應用程式，Azure Active Directory (Azure AD) 支援多種驗證通訊協定，包括舊式驗證。 舊式驗證是一個詞彙，指的是所做的驗證要求：

* 舊版 Office 用戶端未使用新式驗證 （例如，Office 2010 用戶端）
* 使用舊版的郵件通訊協定，例如 IMAP/smtp/pop3 任何用戶端

現在，所有危及登入嘗試的大部份是舊有的驗證。 舊有的驗證不支援多重要素驗證 (MFA)。 即使您有在您的目錄上啟用的 MFA 原則，不良執行者可以使用舊版的通訊協定進行驗證，並略過 MFA。

若要從惡意的驗證要求所做的舊版通訊協定保護您的帳戶，最好是一起封鎖這些嘗試。 若要讓您更輕鬆地封鎖舊版通訊協定所做的所有登入要求，我們會建立僅具有基準原則。

![使用條件式存取的封鎖舊版驗證](./media/howto-baseline-protect-legacy-auth/baseline-policy-block-legacy-authentication.png)

**區塊舊有的驗證**已[基準原則](concept-baseline-protection.md)封鎖舊版通訊協定從進行的所有驗證要求。 已成功登入的所有使用者，就必須使用新式驗證。 其他的基準原則一起使用，所有來自舊版通訊協定的要求將遭到封鎖和所有使用者都都需要 mfa 時所需。 此原則不會封鎖 Exchange ActiveSync。

## <a name="identify-legacy-authentication-use"></a>識別舊有的驗證，請使用

您可以封鎖您目錄中的舊有的驗證之前，您需要先了解您的使用者是否具有使用舊有的驗證以及分割如何影響您整體的目錄的應用程式。 Azure AD 登入記錄檔可用來了解是否您使用舊有的驗證。

1. 瀏覽至**Azure 入口網站** > **Azure Active Directory** > **登入**。
1. 如果它不會顯示上的 新增用戶端應用程式的資料行**資料行** > **用戶端應用程式**。
1. 篩選依據**用戶端應用程式** > **其他用戶端**，按一下 **套用**。

篩選只顯示您登入嘗試，所進行的舊版驗證通訊協定。 按一下每個個別的登入嘗試，將會顯示其他詳細資料。 **用戶端應用程式**欄位下**基本資訊** 索引標籤會指出已使用的舊版驗證通訊協定。

這些記錄檔會指出哪些使用者仍然需要用到舊有的驗證，以及哪些應用程式使用舊版的通訊協定來進行驗證要求。 使用者不會出現在這些記錄檔，並確認不會使用舊有的驗證，實作條件式存取原則，或啟用**基準原則： 封鎖舊版驗證**僅適用於這些使用者。

## <a name="moving-away-from-legacy-authentication"></a>脫離舊有的驗證

深入了解誰您目錄中使用舊有的驗證，以及哪些應用程式依存於此之後下, 一個步驟升級您使用新式驗證的使用者。 新式驗證是身分識別管理，可提供更安全的使用者驗證和授權的方法。 如果您的目錄位置中有 MFA 原則，可確保新式驗證，系統會提示使用者進行 MFA 時所需。 它是以舊版驗證通訊協定更安全的替代方案。

本節提供如何更新您的環境到新式驗證的逐步概觀。 啟用封鎖組織中的原則的舊版驗證之前閱讀下面的步驟。

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>步驟 1：啟用您目錄中的新式驗證

啟用新式驗證的第一個步驟確定您的目錄支援新式驗證。 對於目錄上或在 2017 年 8 月 1 日之後建立的預設會啟用新式驗證。 如果您的目錄建立在此日期之前，您必須手動啟用新式驗證您的目錄使用下列步驟：

1. 請檢查您的目錄是否已經支援執行新式驗證`Get-CsOAuthConfiguration`從[Skype for Business Online PowerShell 模組](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell)。
1. 如果您的命令會傳回空白`OAuthServers`屬性，則新式驗證已停用。 將設定更新為使用新式驗證啟用`Set-CsOAuthConfiguration`。 如果您`OAuthServers`屬性包含一個項目，您可以大顯身手。

請務必完成此步驟，再繼續。 很重要，因為它們會決定哪一個通訊協定適用於所有的 Office 用戶端目錄組態會先變更。 即使您使用支援新式驗證的 Office 用戶端，它們會預設為使用傳統通訊協定，如果您的目錄上的新式驗證已停用。

### <a name="step-2-office-applications"></a>步驟 2：Office 應用程式

一旦您已啟用新式驗證您的目錄中，您可以開始更新應用程式啟用新式驗證的 Office 用戶端。 Office 2016 或更新版本的用戶端支援新式驗證的預設值。 不不需要任何額外的步驟。

如果您使用 Office 2013 Windows 用戶端或更舊版本，建議您升級為 Office 2016 或更新版本。 即使完成先前的步驟，啟用您目錄中的新式驗證，較舊的 Office 應用程式會繼續使用舊版驗證通訊協定。 如果您使用 Office 2013 用戶端，而且無法立即升級為 Office 2016 或更新版本，請遵循下列文章中的步驟[適用於 Office 2013 Windows 裝置上啟用新式驗證](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication)。 為了協助保護您的帳戶，您在使用舊有的驗證時，我們建議在您的目錄中使用強式密碼。 請參閱[Azure AD 密碼保護](../authentication/concept-password-ban-bad.md)禁止跨您目錄的弱式密碼。

Office 2010 不支援新式驗證。 您必須升級至較新版本的 Office 的 Office 2010 中的任何使用者。 我們建議升級為 Office 2016 或更新版本，因為它預設會封鎖舊版驗證。

如果您使用 MacOS，建議您升級至 Office for Mac 2016 或更新版本。 如果您使用原生郵件用戶端，您必須具有 MacOS 版本 10.14 或更新版本上的所有裝置。

### <a name="step-3-exchange-and-sharepoint"></a>步驟 3：Exchange 和 SharePoint

針對使用新式驗證的以 Windows 為基礎的 Outlook 用戶端，Exchange Online 必須一併啟用新式驗證。 如果已停用新式驗證 Exchange Online、 以 Windows 為基礎的 Outlook 用戶端支援新式驗證 (Outlook 2013 或更新版本) 會使用基本驗證來連接到 Exchange Online 信箱。

SharePoint Online 啟用新式驗證的預設值。 對於在 2017 年 8 月 1 日之後建立的目錄預設會在 Exchange Online 啟用新式驗證。 不過，如果您先前已停用新式驗證，或您使用在此日期之前建立的目錄，請遵循下列文章中的步驟[Exchange online 啟用新式驗證](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)。

### <a name="step-4-skype-for-business"></a>步驟 4：商務用 Skype

若要避免透過商務用 Skype 的舊有的驗證要求，就必須啟用新式驗證 skype for Business Online。 對於在 2017 年 8 月 1 日之後建立的目錄預設會啟用商務用 Skype 的新式驗證。

若要啟用在商務用 Skype 的新式驗證，我們建議您轉換至 Microsoft Teams，而預設支援新式驗證。 不過，如果您是 tr 無法在這個階段，您必須啟用新式驗證 skype for Business Online，讓 Skype for Business 用戶端會開始使用新式驗證。 請遵循下列文章中的步驟[Skype 支援新式驗證的商務拓撲](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)，如需啟用商務用 skype 的新式驗證的步驟。

除了啟用新式驗證 skype for Business Online，我們建議新式驗證啟用 Exchange Online 啟用新式驗證的商務用 Skype。 此程序可協助您同步處理的新式驗證的 Exchange Online 和商務用 Skype online 狀態，而且，會造成多個登入提示 skype for Business 用戶端。

### <a name="step-5-using-mobile-devices"></a>步驟 5：使用行動裝置

在您的行動裝置上的應用程式必須封鎖以及舊版驗證。 我們建議使用 Outlook mobile。 Outlook Mobile 預設支援新式驗證，並將滿足其他 MFA 基準保護原則。

若要使用原生 iOS 郵件用戶端，您必須執行 iOS 版本 11.0 或更新版本，以確定郵件用戶端已更新為封鎖舊有的驗證。

### <a name="step-6-on-premises-clients"></a>步驟 6：在內部部署用戶端

如果您是使用企業內部部署 Exchange Server 內部部署和 Skype 的混合式客戶時，這兩項服務必須更新，才能啟用新式驗證。 當混合式環境中使用新式驗證，您仍要驗證使用者內部部署。 授權其存取資源 （檔案或電子郵件） 變更故事。

您可以開始啟用新式驗證內部部署之前，先確定您符合 theIf 符合需求，您現在可以準備啟用新式驗證內部部署。

啟用新式驗證的步驟請參閱下列文章：

* [如何設定 Exchange Server 內部部署使用混合式新式驗證](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [如何以新式驗證 (ADAL) 使用商務用 Skype](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="enable-the-baseline-policy"></a>啟用基準原則

原則**基準原則：區塊舊有的驗證 （預覽）** 隨附預先設定，然後會出現在頂端當您瀏覽至 Azure 入口網站中的 [條件式存取] 刀鋒視窗。

若要啟用此原則，並保護您的組織：

1. 登入 **Azure 入口網站** 為全域管理員、 安全性系統管理員或條件式存取系統管理員。
1. 瀏覽至**Azure Active Directory** > **條件式存取**。
1. 在原則清單中，選取 **基準原則：區塊舊有的驗證 （預覽）** 。
1. 設定**啟用原則**要**立即使用原則**。
1. 新增任何使用者排除項目上即可**使用者** > **選取排除的使用者**，然後選擇要排除的使用者。 按一下 **選取** 再**完成**。
1. 按一下  **儲存**。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱

* [條件式存取基準保護原則](concept-baseline-protection.md)
* [可保護身分識別基礎結構的五個步驟](../../security/azure-ad-secure-steps.md)
* [什麼是 Azure Active Directory 中的條件式存取？](overview.md)
