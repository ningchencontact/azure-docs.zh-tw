---
title: 了解世界不需要與 Azure Active Directory 的密碼 |Microsoft Docs
description: 本指南可協助 Ceo、 Cio、 Ciso、 首席身分識別架構設計人員、 企業架構設計人員和安全性和 IT 決策者負責選擇其 Azure Active Directory 實作的無密碼驗證方法。
services: active-directory
keywords: 無密碼，azure ad
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 00f66b6010bead3de131095a47ba1e419d2511c0
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723437"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>不需要密碼與 Azure Active Directory 的世界

它就可以分解您使用密碼的關聯性。 密碼已在過去，我們很好，但是在現今的數位工作區中它們變得相當簡單的攻擊，讓駭客。 駭客所喜愛的密碼，並不難為什麼當您認為最常被拒絕的密碼，Azure Active Directory (Azure AD) 中包含年、 月、 季、 之類的詞彙或本機體育看到。 此外，[研究顯示](https://aka.ms/passwordguidance)長度需求、 複雜性需求等變更頻率的密碼管理的傳統的建議會造成不良的後果，原因有許多相關人類看得性質。

三種類型的攻擊通常用來危害的使用者帳戶是密碼噴灑網路釣魚和缺口重新執行。 這類 azure AD 功能[智慧鎖定](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)，[禁用的密碼](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)，並[密碼保護](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)有助於防範這類的攻擊。 同樣地，實作[多重要素驗證](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)(MFA) 或雙步驟驗證，藉由要求第二種形式的驗證提供額外的安全性。 但，長期而言，無密碼的解決方法是最佳的解決方案，以確保最安全的驗證方法。

這篇文章是旅程的您，可協助您了解和實作 Microsoft 的無密碼的解決方案，協助您選擇一或多個下列選項之間的開頭：

* **Windows hello 企業版**。 在 Windows 10 中，Windows hello 企業版密碼以取代電腦和行動裝置上的增強式雙重要素驗證。 這項驗證是由與裝置繫結的新型使用者認證所組成的，而且會使用生物特徵辨識或 PIN 碼。

* **無密碼登入使用 Microsoft Authenticator**。 Microsoft Authenticator 應用程式可以用於不使用密碼登入 Azure AD 帳戶。 類似的 Windows hello 企業版的技術，Microsoft Authenticator 會使用索引鍵為基礎的驗證啟用的使用者認證會繫結至裝置，並使用生物識別技術或 PIN。

* **FIDO2 安全性金鑰**。 FIDO2 提供跨每個網站都是獨一無二，且會儲存在本機裝置，例如 Windows Hello 或外部的安全性金鑰的密碼編譯的登入認證。 這些安全性金鑰能抵抗網路釣魚，密碼竊取的風險，並重新執行攻擊。 相較於透過生物識別技術或 pin 碼的使用者驗證，解決方案就是兩個因素驗證會議先進的安全性需求。

## <a name="the-future-of-passwordless-authentication"></a>未來的無密碼驗證

這幾天內，銀行、 信用卡公司和其他組織與線上服務通常保護您的帳戶透過要求您確認您的身分識別兩次： 一次是由透過電話、 文字或應用程式，然後再次使用您的密碼。 雖然多重要素驗證位址的共用密碼的安全性問題、 遭竊或猜到，它無法解決比試著記住它們的不便，敬請因素。 使用者和組織要在現今的雲端時代，什麼是非常安全的無密碼驗證方法*和*方便。

![便利性與安全性](./media/azure-ad/azure-ad-pwdless-image1.png)

Windows Hello 進行商務、 無密碼登入與 Microsoft Authenticator 和 FIDO2 安全性機碼所有共用簡單的一般架構，以提供使用者同時高度安全且方便使用的驗證方法。 這三個是以公用/私密金鑰技術，需要本機筆勢例如生物識別技術或 pin 碼和私用金鑰繫結至單一裝置且安全地儲存，而且永遠不會共用為基礎。

## <a name="windows-hello-for-business"></a>Windows Hello 企業版

在 Windows 10 中，Windows hello 企業版密碼以取代電腦和裝置上的增強式雙重要素驗證。 驗證包含新類型的繫結至裝置，並使用生物識別手勢或 pin 碼，可讓使用者向 Azure AD 的使用者認證以及在內部部署 Active Directory。 只要插入裝置簽署使用 Windows Hello 仩很簡單。 您使用 PIN 或生物識別手勢，例如指紋或臉部辨識。

### <a name="windows-hello-for-business-scenarios"></a>Windows hello 企業版案例

Windows hello 企業版是適用於資訊工作者自己指定的 Windows 電腦。 生物識別和認證會直接繫結至使用者的電腦上，這可防止從擁有者以外的任何人存取。 PKI 整合和單一登入 (SSO) 的內建支援，Windows hello 企業版提供簡單且方便的方法，來順暢地存取內部公司資源和雲端中。

### <a name="windows-hello-for-business-deployment-considerations"></a>Windows Hello for Business 部署考量

Windows hello 企業版是使用數種元件來完成裝置註冊、 佈建和驗證分散式的系統。 因此，部署需要適當的規劃跨組織內的多個小組。 Windows hello 企業版[規劃指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide)可用來幫助您做出商業部署以及您需要考量的選項 Windows hello 的型別。

針對混合式或內部部署，預期會得到：

* 連線狀態良好，作用中的網路

* 網際網路存取

* Multi-factor Authentication Server 以支援商務佈建期間 Windows Hello 的 MFA

* 正確的名稱解析，內部及外部名稱

* Active Directory 與足夠數量的每個站台的網域控制站，以支援的驗證

* Active Directory 憑證服務 2012年或更新版本

* 執行 Windows 10 版本 1903年的一或多個工作站電腦

因為所有類型的 Windows Hello 企業部署的網域控制站使用企業所發行的憑證為根的信任，您會想要自動更新過期的憑證，由[設定自動註冊的伺服器和使用者憑證](https://docs.microsoft.com/windows-server/networking/core-network-guide/cncg/server-certs/configure-server-certificate-autoenrollment)。 在內部部署身分識別提供者是執行 Windows Server Active Directory Federation Services (AD FS) 角色的內部部署伺服器。 同盟系統通常需要經過負載平衡的伺服器陣列，稱為伺服器陣列。 這個伺服器陣列是在內部網路以及周邊網路拓撲中設定，以確保驗證要求的高可用性。

當您設定群組原則來要求 Windows Hello for Business 中的工作場所時，您可能想要準備您的組織中的使用者說明如何使用 Windows Hello。 比方說，當有人設定新裝置時，系統會提示他們選擇**此裝置屬於我的組織**或是**這是我自己的個人裝置**。 公司裝置，他們應該選取前者項目。 使用者也必須被告知他們應選取哪一個連接選項：**加入 Azure AD**或是**設定本機帳戶 （稍後網域聯結）** 。

很常見的習慣使用生物識別手勢來驗證白天在白天最終忘記其 pin 碼的使用者。 若要避免呼叫技術支援人員，建議您提供使用者能夠重設忘記[密碼](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment)並[Pin](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset)。

有許多部署 Windows hello 企業版時，您可以選擇的選項。 提供多個選項可確保幾乎每個組織可部署 Windows hello 企業版。 請考慮下列類型的支援的部署：

* [已加入混合式 Azure AD 信任部署](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [混合式 Azure AD 已加入信任的憑證部署](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [加入 azure AD 單一登入部署指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [在內部部署金鑰的信任部署](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [內部部署憑證信任部署](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

提供許多選項可顯示複雜的部署。 不過，大部分的組織可能會決定，他們已實作大部分的 Windows hello 企業版部署所依賴的基礎結構。 不論如何，務必了解 Windows hello 企業版是分散式的系統，以及建議適當的規劃。

我們建議您先閱讀[規劃 Windows Hello for Business Deployment](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide)可協助您決定部署模型最適合您特定的組織。 然後，根據您進行規劃，請參閱[Windows Hello for Business 部署指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)以協助確保您現有的環境中成功部署的 Windows hello 企業版。

### <a name="how-windows-hello-for-business-works"></a>Windows Hello 企業版的運作方式

#### <a name="user-sets-up-windows-hello-for-business"></a>使用者設定 Windows hello 企業版

最初的兩步驟驗證之後的使用者在註冊期間，Windows Hello 已設定好使用者的裝置上，Windows 會要求使用者設定一個手勢，它可以是生物特徵辨識，例如指紋或臉部辨識或 PIN。 設定之後，使用者會提供該手勢來驗證其身分識別。 Windows 接著會使用 Windows Hello 驗證使用者。

根據您的 Windows 10 裝置的功能，您將必須內建的安全飛地，稱為硬體的受信任的平台模組 (TPM) 或軟體 TPM。 TPM 會儲存私密金鑰，這需要您臉部、 指紋或 PIN 來解除鎖定它。 生物特徵辨識資料不會漫遊，並永遠不會傳送至外部裝置或伺服器。 沒有任何攻擊者可能會危害竊取生物識別資料，因為 Windows Hello 只會儲存生物識別資料在裝置上的單一集合點。

> [!TIP]
> 表面上，PIN 會覺得密碼，但實際上更安全。 密碼和 PIN 的重要差異是，繫結至特定的裝置它已設定的 PIN。 竊取您的密碼的任何人可以登入您的帳戶，從任何地方。 不過，如果它們竊取您的 pin 碼，不必太竊取您的實體裝置 ！ 此外，由於 PIN 是本機裝置，但不會傳送任何位置，無法在傳輸中遭到攔截或從伺服器遭竊。

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>使用 Windows Hello for Business 登入的使用者

Windows Hello for Business 生物識別技術和 Pin 使用非對稱 （公開/私密金鑰） 進行驗證的加密。 在驗證期間，加密會繫結至 TLS 工作階段金鑰，以便攔截 (MiTM) 攻擊不能竊取產生的安全性權杖或成品，並從其他地方重新保護驗證程序。

Windows hello 企業版提供方便在登入體驗，驗證至 Azure AD 使用者和 Active Directory 資源。 Azure AD 已加入的裝置登入期間，對 Azure 進行驗證，並選擇性地向 Active Directory。 混合式 Azure Active Directory 已加入裝置登入期間向 Active Directory，並在背景中 Azure Active Directory 進行驗證。

![請參閱來源映像](./media/azure-ad/azure-ad-pwdless-image2.jpeg)

下列步驟會說明 Azure Active Directory 登入驗證。

![Windows 10 的鎖定畫面](./media/azure-ad/azure-ad-pwdless-image3.png)

1. 使用者用以登入使用 Windows 生物特徵或 PIN 筆勢。 筆勢解除鎖定 Windows Hello 的業務私用的索引鍵，並傳送至雲端驗證安全性支援提供者，稱為雲端 AP 提供者。

2. 雲端 AP 提供者會向 Azure Active Directory 要求 nonce。

3. Azure AD 傳回的有效時間為 5 分鐘的 nonce。

4. 雲端 AP 提供者登入使用使用者的私用金鑰的 nonce，並傳回至 Azure Active Directory 的帶正負號的 nonce。

5. Azure Active Directory 驗證使用使用者的安全地註冊的公開金鑰，nonce 的簽章的帶正負號的 nonce。 之後驗證簽章，Azure AD 接著會驗證傳回帶正負號的 nonce。 驗證之後的 nonce，Azure AD 會建立 PRT 加密裝置的傳輸金鑰並將它傳回給雲端 AP 提供者的工作階段金鑰。

6. 雲端 AP 提供者會收到加密的 PRT 使用工作階段金鑰。 使用裝置的私用的傳輸金鑰，雲端 AP 提供者會將工作階段金鑰解密，並保護使用裝置的 TPM 的工作階段金鑰。

7. 雲端 AP 提供者將驗證成功回應傳回給 Windows 之後，使用者可以存取 Windows，以及您在雲端和內部應用程式，而不需要再次驗證 」 (SSO)。

在其他情況下，包含 Windows hello 企業版的驗證程序帶您深入探討，請參閱 < [Windows Hello 為商務和驗證](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#Azure-AD-join-authentication-to-Active-Directory-using-a-Key)。

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>使用者管理其 Windows Hello for Business 認證

[Microsoft PIN 重設服務](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset)是一項功能可讓使用者重設其 pin 碼，如有需要的 Azure AD 中。 使用群組原則，Microsoft Intune 或相容的 MDM，系統管理員可以設定 Windows 10 裝置安全地使用 Microsoft PIN 重設服務，可讓使用者重設其使用者忘記 pin 碼透過設定或鎖定畫面上，而不需要重新註冊。

有時候使用者必須改為使用密碼。 [自助式密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment)(SSPR) 是另一個 Azure AD 功能，可讓使用者重設其密碼，而不需要連絡 IT 人員。 使用者必須註冊，或針對自助式密碼重設之前先使用服務註冊。 在註冊期間，使用者會選擇啟用其組織的一或多個驗證方法。 SSPR 可讓使用者快速解除封鎖，並繼續運作，無論他們身在何處或一天的時間。 藉由允許使用者自行解除封鎖，非生產時間並提高支援成本，最常見的密碼相關問題，可以減少您的組織。

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>無密碼登入使用 Microsoft Authenticator

無密碼登入使用 Microsoft Authenticator 是另一個無密碼解決方案，可用來使用手機登入的 Azure AD 帳戶登入。 您仍然必須提供您知道的東西，您有此項目，但電話登入可讓您略過輸入您的密碼，以及使用您的指紋、 臉部或 PIN 行動裝置上執行所有身分識別驗證來驗證您的身分識別。

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Microsoft Authenticator 無密碼的案例

Microsoft Authenticator 應用程式可讓使用者確認其身分識別，並向其公司或個人帳戶。 也可用來擴充含有單次密碼的密碼或推播通知或完全取代密碼的需求。 而不是使用密碼，使用者會確認其身分識別，使用他們的行動電話透過指紋掃描、 臉部或鳶尾花的辨識或 PIN。 類似於哪些 Windows Hello 使用安全技術建置，這項工具會封裝到簡單的應用程式，因此方便的選項，讓使用者在行動裝置上。 使用適用於 Android 和 iOS 的 Microsoft Authenticator 應用程式。

### <a name="microsoft-authenticator-deployment-considerations"></a>Microsoft Authenticator 部署考量

使用 Microsoft Authenticator 應用程式的無密碼登執行至 Azure AD 的必要條件如下：

* 終端使用者啟用 Azure Multi-factor Authentication

* 若要使用 Microsoft Intune 或協力廠商行動裝置管理 (MDM) 解決方案中註冊其裝置的使用者的能力

假設已符合這些需求，系統管理員啟用無密碼的手機登入租用戶中使用[Windows PowerShell。](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) 一旦手機登入租用戶中啟用，使用者也可以選擇使用他們的手機上，選取其工作或學校帳戶來登入**帳號**應用程式，然後選取畫面**啟用手機登入**.

假設無密碼登入已啟用由系統管理員，使用者必須符合下列需求：

* 在 Azure Multi-factor Authentication 中註冊

* 最新版的 Microsoft 驗證器安裝在裝置執行 iOS 8.0 或更新版本，Android 6.0 或更新版本

* 使用推播通知新增至應用程式的工作或學校帳戶

若要避免可能會遭到鎖定超出您的帳戶，或必須重新建立新的裝置上的帳戶，建議您使用 Microsoft Authenticator[備份您的帳戶認證](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-backup-recovery)至雲端。 備份之後，您也可以使用此應用程式在新裝置上復原您的資訊，這有可能避免遭到鎖定或者必須重新建立帳戶。

因為大多數使用者已經習慣僅使用密碼來進行驗證，務必貴組織負責教授關於此程序的使用者。 解程度可以降低使用者呼叫技術支援人員，任何的可能性[問題](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#known-issues)與使用 Microsoft Authenticator 應用程式登入。

> [!NOTE]
> 潛在此解決方案的失敗點時，漫遊使用者位於的位置沒有任何網際網路連線。 FIDO2 安全性金鑰和 Windows hello 企業版不收取相同的限制。

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>如何為無密碼登入 Microsoft 驗證器運作方式

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>使用者會設定無密碼登入使用 Microsoft Authenticator

Microsoft Authenticator 應用程式可以做為無密碼的方案用來登入 Azure AD 帳戶之前，必須由系統管理員與終端使用者執行步驟。

首先，系統管理員將需要[讓使用應用程式做為認證](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users)租用戶使用 Windows PowerShell 中。 系統管理員也必須啟用 Azure Multi-factor authentication (Azure MFA) 的使用者，並將 Microsoft Authenticator 應用程式設定的其中一個[驗證方法](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings#verification-methods)。

使用者將需要[下載並安裝](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install)的 Microsoft Authenticator 應用程式和[將他們的帳戶設定](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app)使用 Microsoft Authenticator 應用程式的其中一個驗證方法。

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>使用 Microsoft Authenticator 的無密碼登入的使用者

Microsoft Authenticator 應用程式可用來登入任何 Azure AD 帳戶 (而不需使用密碼)。 雖然在 Windows 10 的鎖定畫面不包含 Microsoft Authenticator 應用程式，做為登入選項，使用者仍輸入其使用者名稱，然後接收其行動裝置上的推播通知，請確認存在。 使用者以確認它們的存在相符的 [登入] 畫面上的數字，然後提供臉部掃描、 指紋或 PIN 以解除鎖定的私密金鑰，並完成驗證。 這個多重要素驗證方法是比密碼更安全、 更方便比輸入密碼，以及程式碼。

![驗證器登入](./media/azure-ad/azure-ad-pwdless-image4.png)

使用 Microsoft Authenticator 的無密碼驗證為 Windows hello 企業版但稍微複雜一點因為使用者需要讓 Azure AD 可以找到 Microsoft Authenticator 應用程式版本所識別，請遵循相同的基本模式使用此項目。

![驗證器程序](./media/azure-ad/azure-ad-pwdless-image5.png)

1. 使用者輸入其使用者名稱。

2. Azure AD 會偵測使用者有強式認證，並開始強式認證流程。

3. 若要透過 Apple Push Notification Service (APNS) iOS 裝置上或透過 Firebase 雲端通訊 (FCM) Android 裝置上的應用程式會傳送通知。

4. 使用者會收到推播通知，並開啟應用程式。

5. 應用程式會呼叫 Azure AD，並接收證明存在挑戰和 nonce。

6. 使用者輸入其生物識別技術或 PIN 來解除鎖定私密金鑰完成所面臨的挑戰。

7. Nonce 是使用私密金鑰簽署，並傳送回 Azure AD。

8. Azure AD 執行公用/私用金鑰驗證，並傳回權杖。

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>使用者管理其無密碼登入使用 Microsoft Authenticator 的認證

具有[結合註冊](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)，使用者可以註冊並取得 Azure Multi-factor Authentication 與自助式密碼重設的優勢。 使用者註冊和管理這些設定，請瀏覽至其[我的設定檔頁面](https://aka.ms/mysecurityinfo)。 除了啟用 SSPR，結合註冊支援，多個驗證方法和動作。

## <a name="fido2-security-keys"></a>FIDO2 安全性金鑰

FIDO2 是 FIDO 結盟標準的最新版本，並有兩個元件-W3C 的 Web 驗證 (WebAuthN) 標準和對應的 FIDO 結盟以驗證器的用戶端通訊協定 (CTAP2)。 FIDO2 標準，讓使用者運用硬體、 行動和生物識別技術為基礎的驗證器來輕鬆地驗證許多的應用程式及行動和桌面環境中的網站。

Microsoft 和業界合作夥伴已 FIDO2 安全性裝置的 Windows Hello 以啟用共用的裝置上的簡單又安全驗證一起工作。 FIDO2 安全性金鑰可讓您執行您與您的認證，並安全地向[Azure AD](https://aka.ms/azuread418)-加入屬於您的組織的 Windows 10 裝置。

WebAuthN 定義的 API 可讓開發與實作強式、 web 應用程式和服務的無密碼驗證。 CTAP 通訊協定可讓外部的裝置，例如 WebAuthN 並做為驗證器符合 FIDO 安全性金鑰。 使用 Web 驗證時，使用者可以登入線上服務的臉部、 指紋、 PIN 或可攜式 FIDO2 安全性金鑰，利用強式的公用金鑰認證，而不是密碼。 目前 WebAuthN 支援 Microsoft Edge 和 Chrome 支援，Firefox 處於發展階段。

裝置和遵守 FIDO2、 WebAuthN 和 CTAP 通訊協定的權杖將的強式驗證的跨平台解決方案而不使用密碼。 Microsoft 合作夥伴正致力於各種不同的安全性金鑰的外型規格，例如 USB 安全性金鑰和啟用 NFC 智慧卡。

### <a name="fido2-security-keys-scenarios"></a>FIDO2 安全性金鑰案例

FIDO2 安全性金鑰可用來登入 Azure AD 做為認證提供者，在 Windows 10 鎖定畫面上選擇的安全性金鑰。 使用者名稱或密碼不需要因此會很理想的解決方案的第一列工作者多位使用者間共用的電腦。 它們也是絕佳的驗證選項時公司的原則會要求使用者的認證必須是實際分開他們的裝置。 使用者也可以選擇在 Windows 10 版本 1809年或更高版本上使用 Microsoft Edge 瀏覽器內其 FIDO2 安全性金鑰登入網站。

### <a name="fido2-security-keys-deployment-considerations"></a>FIDO2 安全性金鑰部署考量

系統管理員可啟用 Azure AD 中的 FIDO2 支援，並指派給使用者或群組的功能。 原則也可以建立的金鑰佈建和設定限制，例如允許或封鎖一組特定的硬體安全性金鑰的方式。 使用者必須實際散發索引鍵。

**啟用無密碼的需求將 Azure AD 登入，並使用 FIDO2 安全性金鑰的網站都包含下列：**

* Azure AD

* Azure Multi-Factor Authentication

* 結合註冊預覽

* FIDO2 安全性金鑰預覽需要相容的 FIDO2 安全性金鑰

* Web 驗證 (WebAuthN) 需要 Windows 10 版本 1809年或更高版本上的 Microsoft Edge

* FIDO2 型的 Windows 登入需要 Azure AD 已加入 Windows 10 版本 1809年或更高版本 （最佳的體驗是在 Windows 10 版 1903年或更新版本）

FIDO2 相容的表單係數包括 USB、 NFC、 和藍芽裝置。 我們建議您選擇符合您特定的需求，因為某些平台的尺寸和瀏覽器尚無法 FIDO2 相容。

我們也建議每個組織建立使用者的通訊協定，並遵循的系統管理員應該安全性金鑰遺失或遭竊。 使用者應該報告遺失或遭竊的機碼，以便系統管理員或使用者可以從使用者的設定檔中刪除其安全性金鑰，並佈建一個新。

### <a name="how-fido2-security-keys-works"></a>FIDO2 安全性金鑰的運作方式

#### <a name="user-sets-up-fido2-security-key"></a>使用者設定 FIDO2 安全性金鑰

雖然系統管理員可以[以手動方式佈建金鑰](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable)並將它們散發給使用者，佈建以及啟用 FIDO2 認證提供者，在 Windows 10 的鎖定畫面上將支援透過[Intune](https://docs.microsoft.com/intune/windows-enrollment-methods). 系統管理員也必須使用[Azure 入口網站](https://portal.azure.com/)啟用硬體權杖裝置做為無密碼驗證方法。

部署 FIDO2 安全性金鑰也會要求使用者註冊使用其索引鍵[結合註冊](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)。 使用合併的註冊，使用者會一次註冊，並取得 Azure Multi-factor Authentication 和單一登入密碼重設 (SSPR) 的優點。

除了選取硬體權杖做為預設的多重要素驗證方法，建議您也選取其他驗證選項。

* Microsoft Authenticator-通知

* 驗證器應用程式或硬體權杖-程式碼

* 撥打電話

* 簡訊

#### <a name="user-using-fido2-security-key-for-sign-in"></a>使用者在登入時使用 FIDO2 安全性金鑰

FIDO2 提供一個抽象層之間用做驗證器 」 和 「 公用/私密金鑰密碼編譯，啟用 例如 Windows Hello 和安全性金鑰解析為私用金鑰，並提供公開金鑰的內建的平台驗證器的尺寸可用來當做識別項來存取外部資源。 FIDO2 安全性金鑰配備自己內建的安全飛地，會將私密金鑰存放及需要的生物識別技術或 PIN 來解除鎖定。 認證不能重複使用、 重新執行，或在服務之間共用而不受網路釣魚和 MiTM 攻擊或伺服器缺口。

![FIDO2 登入](./media/azure-ad/azure-ad-pwdless-image6.png)

FIDO2 安全性金鑰提供安全的驗證，獨立於外型規格。 安全性金鑰保存認證，以及應該使用其他的第二個因素，例如指紋 （整合的安全性金鑰） 或 Windows 登入時必須輸入 pin 碼保護。 Microsoft 合作夥伴正致力於各種不同的安全性金鑰的外型規格。 某些範例包括 USB 安全性金鑰，以及 NFC 啟用智慧卡。

> [!NOTE]
> 特定功能和從 FIDO2 CTAP 通訊協定的延伸模組，必須實作的安全性金鑰[Microsoft 相容](https://aka.ms/fido2securitykeys)。 Microsoft 已測試這些解決方案與 Windows 10 和 Azure Active Directory 的相容性。

![FIDO2 登入程序](./media/azure-ad/azure-ad-pwdless-image9.png)

1. 使用者會將 FIDO2 裝置插入電腦。

2. Windows 會偵測 FIDO2 安全性金鑰。

3. Windows 會傳送驗證要求。

4. Azure AD 會傳回 nonce。

5. 使用者完成其動作來解除鎖定私密金鑰儲存在 FIDO2 安全性金鑰的安全飛地。

6. FIDO2 安全性金鑰簽署的私用金鑰的 nonce。

7. 使用帶正負號的 nonce PRT 權杖要求會傳送至 Azure AD。

8. Azure AD 驗證使用 FIDO2 公開金鑰的帶正負號的 nonce。

9. Azure AD 傳回 PRT，若要啟用內部部署資源的存取權。

#### <a name="user-manages-their-fido2-security-key-credentials"></a>使用者管理其 FIDO2 安全性金鑰認證

類似於 Microsoft Authenticator 應用程式，FIDO2 安全性金鑰的認證管理依賴使用者合併的註冊體驗。

## <a name="deciding-a-passwordless-method"></a>決定無密碼的方法

選擇這三個無密碼的選項取決於您公司的安全性、 平台和應用程式需求。

以下是為您選擇 Microsoft 技術，無密碼時要考慮的一些因素：

||**Windows Hello 企業版**|**無密碼登入 Microsoft Authenticator 應用程式**|**FIDO2 安全性金鑰**|
|:-|:-|:-|:-|
|**必要條件**| Windows 10 版本 1809 或更新版本<br>Azure Active Directory| Microsoft Authenticator 應用程式<br>電話 (iOS 和 Android 裝置執行 Android 6.0 或更新版本。)|Windows 10 版本 1809 或更新版本<br>Azure Active Directory|
|**模式**|平台|軟體|硬體|
|**系統和裝置**|電腦具有內建信賴平台模組 (TPM)<br>PIN 與生物特徵辨識 |在手機上的 PIN 與生物特徵辨識|Microsoft 相容的 FIDO2 安全性裝置|
|**使用者體驗**|登入使用 Windows 裝置的 PIN 或生物特徵辨識 （「 臉部 」、 「 鳶尾花或 「 指紋 」）。<br>Windows Hello 驗證是繫結裝置;使用者必須在裝置和登入元件，例如 PIN 或生物特徵辨識因數來存取公司資源。|登入使用指紋掃描、 臉部或鳶尾花辨識的行動電話或 PIN。<br>使用者登入至工作或個人的帳戶從其個人電腦或行動電話。|登入使用 FIDO2 安全性裝置 （生物識別技術、 PIN 和 NFC）<br>使用者可以存取組織控制項為基礎的裝置，並驗證根據的 PIN、 生物識別技術使用裝置，例如 USB 安全性金鑰和啟用 NFC 智慧卡、 金鑰或穿戴式裝置。|
|**已啟用的案例**| 使用 Windows 裝置的無密碼的體驗。<br>適用於專用的工作電腦的能力，讓單一登入裝置和應用程式。|無密碼的任何地方使用行動電話的方案。<br>適用於從任何裝置存取公司或個人的應用程式在網站上。|使用生物識別技術、 PIN 和 NFC 的背景工作角色的無密碼的體驗。<br>適用於共用的電腦和行動電話是不可行的選項 （例如，針對支援服務中心人員、 公用資訊站或醫院小組）|

您可以使用下表來選擇哪一種方法將會支援您的需求和使用者。

|人物代表|狀況|環境|無密碼的技術|
|:-|:-|:-|:-|
|**系統管理員**|管理工作的裝置安全存取|指派的 Windows 10 裝置|Windows Hello 的商務和/或 FIDO2 安全性金鑰|
|**系統管理員**|在非 Windows 裝置上的管理工作| 行動裝置或非 windows 裝置|無密碼登入 Microsoft Authenticator 應用程式|
|**資訊工作者**|生產力工作|指派的 Windows 10 裝置|Windows Hello 的商務和/或 FIDO2 安全性金鑰|
|**資訊工作者**|生產力工作| 行動裝置或非 windows 裝置|無密碼登入 Microsoft Authenticator 應用程式|
|**第一道背景工作角色**|處理站、 工廠、 零售或資料的項目中的 kiosk|共用的 Windows 10 裝置|FIDO2 安全性金鑰|

## <a name="getting-started"></a>使用者入門

未來，以及更安全的環境路徑 wave 無密碼驗證。 建議您使用組織開始規劃這項變更，並減少其相依性的密碼。 若要開始，請考慮下列目標：

* 使用者啟用 MFA + Microsoft Authenticator 應用程式條件式存取。

* 導入 Azure AD 密碼保護 + 更新原則。

* 啟用 sspr 的使用者，使用合併的註冊。

* 將 Microsoft Authenticator 應用程式部署的行動。

* 部署 Windows hello 企業版 (1903年： 最新版本)。

* 部署 FIDO2 裝置無法使用電話的使用者。

* 可能的話，請停用密碼型驗證。

若要達成這些目標，我們建議下列方式：

1. 啟用 Azure Active Directory 來充分利用 Azure MFA 和條件式存取等功能。

2. 啟用多重要素驗證提供額外的保護。

3. 萬一使用者需要改為使用密碼，請啟用自助式密碼重設。

4. 部署 Microsoft 驗證器電話登入已加入的行動力。

5. 部署 Windows hello 企業版到所有 Windows 10 裝置。

6. 準備 FIDO2 安全性金鑰。

> [!NOTE]
> 請參閱 Azure Active Directory[授權頁面](https://azure.microsoft.com/pricing/details/active-directory/)如無密碼的方法的授權需求的詳細資訊。

## <a name="conclusion"></a>結論

過去幾年來，Microsoft 一直致力啟用無密碼的世界。 使用 Windows 10，Microsoft 引進 Windows hello 企業版，強式，硬體受到保護，可讓單一登入 Azure Active Directory 和 Active Directory 的雙因素認證。 類似的 Windows hello 企業版的技術，Microsoft Authenticator 應用程式使用金鑰型驗證，讓繫結到行動裝置，並使用生物識別技術或 PIN 的使用者認證。 現在 FIDO2 安全性金鑰可讓您執行您與您的認證並登入至 Azure AD 做為認證提供者，在 Windows 10 鎖定畫面上選擇的安全性金鑰。 這三個無密碼分別降低密碼噴灑網路釣魚的風險和重新執行攻擊，並提供使用者高度安全且方便的方式登入，並從任何地方存取資料。

採用現代化的多重要素驗證的技術，例如生物識別技術和公開金鑰加密中廣泛存取的裝置是其中一個影響最大有意義地可以減少公司的身分識別風險的步驟。 將無密碼是長期安全的驗證方法，並仍處於發展階段。 指定新興的需求，組織就可以準備本身進行計劃以開始將移至 無密碼的技術。

## <a name="next-steps"></a>後續步驟

* 概觀[無密碼是什麼？](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)
* [如何啟用 Azure AD 中的無密碼](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable)
