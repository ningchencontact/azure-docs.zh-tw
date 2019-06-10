---
title: 規劃 Azure Active Directory 單一登入部署
description: 指南可協助您規劃、 部署及管理您組織中的 SSO。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29569302d20e23c95b6508a5b58c7ed96e005885
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499245"
---
# <a name="plan-a-single-sign-on-deployment"></a>規劃單一登入部署

單一登入 (SSO) 表示存取所有應用程式和資源的使用者需要登入，只要使用單一使用者帳戶。 使用 SSO，使用者可以存取所需的所有應用程式，而不需要再驗證一次。

## <a name="benefits-of-sso"></a>SSO 的優點

單一登入 (SSO) 時，新增安全性及便利性的使用者登入 Azure Active Directory (Azure AD) 中的應用程式。 

許多組織依賴軟體即服務 (SaaS) 應用程式，例如 Office 365、 Box 和 Salesforce 來提升使用者生產力。 在過去，IT 人員需要個別建立並更新每個 SaaS 應用程式，使用者在需要記住的密碼，每個使用者帳戶。

Azure Marketplace 有 3000 的應用程式，有了預先整合的 SSO 連線，讓您輕鬆地將它們整合您的租用戶中。

## <a name="licensing"></a>授權

- **Azure AD 授權**-預先整合的 SaaS 應用程式的 SSO 是免費。 不過，在您的目錄，以及您想要部署的功能中的物件數目可能會需要額外的授權。 如需授權需求的完整清單，請參閱 < [Azure Active Directory 定價](https://azure.microsoft.com/pricing/details/active-directory/)。
- **應用程式授權**-您將需要您的 SaaS 應用程式，以符合您的業務需求的適當授權。 使用應用程式擁有者，以判斷指派給應用程式的使用者是否具有適當的授權，其在應用程式中的角色。 如果您管理 Azure AD 自動佈建角色為基礎，在 Azure AD 中指派的角色必須配合在應用程式中所擁有的授權數目。 在應用程式所擁有的授權數目不可能會導致錯誤期間佈建/更新的使用者。

## <a name="plan-your-sso-team"></a>規劃您的 SSO 小組

- **進行正確的專案關係人**-當技術專案失敗，它通常是因為不相符的期望上影響、 結果和責任。 若要避免這些陷阱中，[確保您正在引人入勝的正確的專案關係人](https://aka.ms/deploymentplans)和專案關係人了解其角色。
- **規劃溝通**-通訊非常重要的任何新的服務成功。 主動傳達給您的使用者有關其體驗會如何變更、 當它將會變更，以及如何取得支援，如果它們時遇到問題。 檢閱的選項[使用者將如何存取其 SSO 功能的應用程式](end-user-experiences.md)，並製作您的通訊，以符合您的選取範圍。 

## <a name="plan-your-sso-protocol"></a>規劃您的 SSO 通訊協定

同盟通訊協定為基礎的 SSO 實作可改善安全性、 可靠性、 和終端使用者體驗，並實作的工作變得更容易。 許多應用程式會在 Azure AD 中預先整合[逐步解說會引導可用](../saas-apps/tutorial-list.md)。 您可以在找到我們[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)。 每個 SSO 方法的詳細的資訊可在發行項[單一登入 Azure Active Directory 中的應用程式](what-is-single-sign-on.md)。

有兩種主要方式，您可以在此讓您的應用程式的單一登入使用者：

- **使用同盟單一登入**Azure AD 會使用其 Azure AD 帳戶驗證至應用程式使用者。 這個方法支援應用程式支援 SAML 2.0 中，WS-同盟或 OpenID Connect，例如通訊協定，且是最豐富的單一登入模式。 我們建議使用 Federated SSO 與 Azure AD，當應用程式支援，而不是密碼型 SSO 和 ADFS。

- **使用密碼型單一登入**使用者登入應用程式的使用者名稱和密碼的第一次存取它。 第一次登入之後，Azure AD 就會向應用程式提供使用者名稱和密碼。 密碼式單一登入可以使用網頁瀏覽器擴充功能或行動應用程式，安全儲存應用程式的密碼以及重新執行。 此選項利用現有登入程序的應用程式所提供、 讓系統管理員可以管理的密碼，並不需要使用者知道密碼。

### <a name="considerations-for-federation-based-sso"></a>同盟 SSO 的考量

- **使用 OpenID Connect 和 OAuth** -如果您要連線，支援的應用程式使用 OIDC/OAuth 2.0 方法，讓您對該應用程式的 SSO。 這個方法需要較少的設定，並可提供更豐富的使用者體驗。 如需詳細資訊，請參閱 < [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)， [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)，並[Azure Active Directory 開發人員指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)。
- **SAML 型 SSO 的端點組態**-如果您使用 SAML，為您的開發人員必須再設定應用程式的特定資訊。 如需詳細資訊，請參閱 <<c0> [ 設定 SAML 的基本選項](configure-single-sign-on-portal.md)。
- **憑證管理 saml sso** -當您啟用同盟 SSO，您的應用程式，Azure AD 會建立三年的有效預設憑證。 您可以自訂視該憑證的到期日。 請確定您已經找備妥更新尚未過期的憑證的程序。 若要進一步了解，請參閱[Azure AD 管理憑證](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)。

### <a name="considerations-for-password-based-sso"></a>密碼型 SSO 的考量

使用 Azure AD 進行密碼型 SSO 需要部署瀏覽器延伸模組，將會安全地擷取認證，並填寫 登入表單。 定義一個機制來部署規模擴充功能[支援的瀏覽器](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。 這些選項包括：

- [Internet explorer 的群組原則 ](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [Internet explorer 的 system Center Configuration Manager (SCCM) ](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)
- [使用者導向下載和設定適用於 Chrome、 Firefox、 Microsoft Edge 或 IE ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

若要進一步了解，請參閱[如何設定密碼單一登入](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)。

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>擷取不在資源庫中的應用程式的登入表單中繼資料

Microsoft 支援密碼儲存庫存 （擷取的使用者名稱和密碼欄位） 的 web 應用程式擷取的中繼資料。 在 設定應用程式來擷取 form 中繼資料的程序期間，瀏覽至 登入 URL。 要求的確切的登入 URL 的應用程式擁有者。 這項資訊可在登入過程中，對應至登入期間的應用程式的 Azure AD 認證。

若要進一步了解，請參閱[什麼是應用程式存取和 SSO 與 Azure AD？ – 密碼型 SSO](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/)。

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>重新擷取需要的指示，在表單中的中繼資料

當應用程式變更其 HTML 配置時，您可能需要重新擷取的中繼資料來調整所做的變更。 指示之 HTML 配置已變更的常見徵兆包括：

- 報告，按一下應用程式取得 「 保留 」 的登入頁面中的使用者
- 報告未填入 使用者名稱或密碼的使用者

#### <a name="shared-accounts"></a>共用的帳戶

登入的觀點而言，使用共用帳戶的應用程式不是從資源庫應用程式使用密碼 SSO 的個別使用者不同。 不過，有一些規劃和設定應用程式在想要使用共用的帳戶時所需的額外步驟：

1. 使用下列的文件的應用程式商務使用者：
   1. 一組會使用應用程式的組織中的使用者
   1. 現有的應用程式相關聯的使用者集合中的認證集 
1. 每個使用者設定和認證的組合，建立在雲端或內部部署您的需求為基礎的安全性群組。
1. 重設共用的認證。 一旦應用程式部署在 Azure AD 中，個人無須共用帳戶的密碼。 因為 Azure AD 會儲存密碼，請考慮設定要很長且複雜。 
1. 如果應用程式支援的話，請設定自動密碼變換。 如此一來，即使未在初始安裝的系統管理員會知道共用帳戶的密碼。 

## <a name="plan-your-authentication-method"></a>規劃您的驗證方法

選擇正確的驗證方法是設定 Azure AD 混合式身分識別解決方案的第一項重要決策。 請實作使用 Azure AD Connect 所設定的驗證方法，這也會在雲端佈建使用者。

若要選擇驗證方法，需要考慮實作您選取項目的時間、現有基礎結構、複雜度及成本。 這些因素對每個組織而言不同，並可能隨時間改變。 您應該選擇最符合您的特定案例。 如需詳細資訊，請參閱 <<c0> [ 選擇正確的驗證方法，Azure Active Directory 混合式身分識別解決方案](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)。

## <a name="plan-your-security-and-governance"></a>規劃您的安全性和治理 

身分識別是新的主要關鍵的安全性注意和投資，因為網路周邊變得越來越 porous 且小於有效使用暴增的 BYOD 裝置和雲端應用程式。 

### <a name="plan-access-reviews"></a>計劃的存取權檢閱

[存取權檢閱](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)讓組織有效率地管理群組成員資格、 存取企業應用程式，以及角色指派。 您應該規劃檢閱定期執行，藉此確定適當的人員具有連續存取權的使用者存取權。

規劃設定存取權檢閱時重要的主題包括：

1. 用來識別存取權檢閱符合您的商務需求的的頻率。 這可以是，經常為每週一次，每月、 每年或依需求練習。

1. 建立代表應用程式存取報告的檢閱者的群組。 您必須確保專案關係人最熟悉的應用程式和其目標使用者和使用案例，您的存取權檢閱的參與者

1. 完成存取權檢閱，包括不再需要存取的使用者拿掉的應用程式的存取權限。 規劃處理潛在的支援要求，從拒絕的使用者。 已刪除的使用者仍在這段期間可以還原它們由系統管理員視的 30 天的 Azure AD 中已刪除。 經過 30 天後，該使用者將永久刪除。 使用 Azure Active Directory 入口網站，全域系統管理員可以明確永久刪除已刪除的使用者在達到該時段之前。

### <a name="plan-auditing"></a>計劃的稽核

Azure AD 提供[包含技術和商務的深入解析報告](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)。 

安全性及活動報告可用。 安全性報告會顯示標幟為有風險和有風險的登入。活動報告可協助您了解您組織中使用者的行為，詳述登入活動，並提供的所有登入的稽核線索。 您可以使用報表來管理風險、 提高生產力，並監視合規性。

| 報表類型 | 存取權檢閱 | 安全性報告 | 登入報告 |
|-------------|---------------|------------------|----------------|
| 若要檢閱的使用 | 應用程式權限和使用方式。 | 可能遭盜用的帳戶 | 誰正在存取應用程式 |
| 可能的動作 | 稽核的存取;撤銷權限 | 撤銷存取權;強制執行安全性重設 | 撤銷存取權 |

Azure AD 會將大部分的稽核資料保留 30 天，並使該資料可透過 Azure 管理入口網站或透過 API 供您下載到您的分析系統。

### <a name="consider-using-microsoft-cloud-application-security"></a>請考慮使用 Microsoft 雲端應用程式安全性

Microsoft 雲端應用程式安全性 (MCAS) 是雲端存取的安全性訊息代理程式 (CASB) 解決方案。 它可讓您掌握雲端應用程式和服務、 提供複雜的分析，以識別並對抗網路，並可讓您控制如何將資料傳輸。

部署 MCAS 可讓您：

- 使用 Cloud Discovery 對應並識別您的雲端環境，以及您的組織使用的雲端應用程式。
- 批准和取消批准的應用程式，您的雲端中
- 使用輕鬆部署應用程式連接器利用提供者 Api，以取得可見度及治理您連接到的應用程式
- 若要取得即時的可見度及控制存取與您的雲端應用程式內的活動中使用條件式存取應用程式控制保護
- 可協助您透過設定，並接著持續微調原則持續進行控制。

Microsoft 雲端應用程式安全性 (MCAS) 工作階段控制項可供在任何作業系統上所有主要平台上的任何瀏覽器。 行動裝置應用程式和桌面應用程式也可以封鎖或允許。 藉由原生與 Azure AD 整合，任何應用程式使用 SAML、 設定或 Open ID Connect 單一登入 Azure AD 中的應用程式可支援，包括[數個熱門應用程式](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)。

如需 MCAS 資訊，請參閱[Microsoft Cloud App Security 概觀](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)。 MCAS 是以使用者為基礎的訂用帳戶的服務。 您可以檢閱中的授權詳細資料[MCAS 授權資料工作表](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)。

### <a name="use-conditional-access"></a>使用條件式存取

使用條件式存取，您可以自動化雲端應用程式條件式存取控制決定。

完成第一個要素驗證之後，即會強制執行條件式存取原則。 因此，條件式存取不是如案例的第一列防禦阻絕服務 (DoS) 攻擊，但可以使用從這些事件的訊號，來決定存取權限。 例如登入風險層級時，可以使用的要求，並依此類推的位置。 如需有關條件式存取的詳細資訊，請參閱 <<c0> [ 概觀](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)並[部署計劃](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)。

## <a name="azure-sso-technical-requirements"></a>Azure 的 SSO 技術需求

下一節詳細說明若要設定包含必要的環境、 端點、 宣告對應、 所需的屬性、 憑證及使用的通訊協定的特定應用程式的需求。 您將需要此資訊來設定中的 SSO [Azure AD 入口網站](https://portal.azure.com/)。

### <a name="authentication-mechanism-details"></a>驗證機制的詳細資料

所有預先整合的 SaaS 應用程式，Microsoft 提供的教學課程，而不需要這項資訊。 如果應用程式不在我們的應用程式 marketplace / 資源庫中，您可能需要收集下列資料片段：

- **如果適用的話，應用程式使用 sso 的目前身分識別提供者**-例如：AD FS，PingFederate，Okta
- **目標應用程式所支援的通訊協定**-例如，SAML 2.0、 OpenID Connect、 OAuth、 表單型驗證，Ws-fed，1.1、WS-Trust
- **使用 Azure AD 進行設定的通訊協定**-例如，SAML 2.0 或 1.1，OpenID Connect、 OAuth、 表單型 Ws-fed

### <a name="attribute-requirements"></a>屬性需求

沒有一組預先設定的屬性和 Azure AD 使用者物件和每個 SaaS 應用程式的使用者物件之間的屬性對應。 有些 app 則管理其他類型的物件，例如群組。 規劃您的應用程式的使用者屬性從 Azure AD 的對應並[來自訂預設屬性對應](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)根據您的業務需要。

### <a name="certificate-requirements"></a>憑證需求

應用程式的憑證必須是最新狀態，或有風險的使用者無法存取應用程式。 大部分的 SaaS 應用程式憑證的有效期限為 36 個月。 您變更憑證持續時間的應用程式刀鋒視窗中。 請確定文件到期，並了解如何管理您的憑證更新。 

有兩種方式管理您的憑證。 

- **自動憑證變換**-Microsoft 支援[在 Azure AD 中簽署金鑰變換](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover)。 雖然這是我們慣用的方法，來管理憑證，並非所有的 ISV 支援此案例。

- **手動更新**-每個應用程式有自己的憑證到期，根據定義的方式。 應用程式的憑證到期之前，建立新的憑證，並將它傳送給 ISV。 這項資訊可以提取從同盟中繼資料。 [閱讀的更多關於同盟中繼資料。](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>實作 SSO

規劃和部署您的解決方案，組織中使用下列階段：

### <a name="user-configuration-for-sso"></a>SSO 的使用者設定

- **識別您的測試使用者**

   應用程式擁有者連絡並要求他們建立的應用程式內的三個測試使用者的最小值。 請確定您將使用的主要識別碼的資訊已正確地填入，且符合 Azure AD 中可用的屬性。 在大部分情況下，這會對應至 「 NameID 」 的 SAML 型應用程式。 JWT 權杖，它是"preferred_username"。
   
   使用者在 Azure AD 中是手動建立以雲端為基礎的使用者，或從使用 Azure AD Connect 同步處理引擎在內部使用者同步處理。 請確定資訊符合應用程式所傳送的宣告。

- **設定 SSO**

   從[的應用程式清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)、 找出並開啟 SSO 教學課程中，為您的應用程式，然後依照本教學課程的步驟為成功設定您的 SaaS 應用程式。

   如果找不到您的應用程式，請參閱[自訂應用程式文件](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)。 這會逐步引導您如何加入 Azure AD 資源庫在找不到的應用程式。

   （選擇性） 您可以使用企業應用程式使用的 SAML 權杖中發出的宣告[Microsoft 的指導方針文件](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)。 請確定這會對應到您預期收到您的應用程式的 SAML 回應中。 如果您在設定期間遇到問題，請在使用我們的指引[如何偵錯 SSO 整合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)。

自訂應用程式上架是 Azure AD Premium P1 或 P2 的授權功能。

### <a name="provide-sso-change-communications-to-end-users"></a>提供給使用者的 SSO 變更通訊

實作溝通計劃。 請確定您會讓您知道該變更傳入，當它到達，該怎麼辦，以及如何尋求協助的使用者。

### <a name="verify-end-user-scenarios-for-sso"></a>Sso 驗證終端使用者案例

您可以使用下列測試案例來進行測試，在公司和個人裝置，以確保您的 SSO 組態按預期方式。 下列案例會假設使用者是瀏覽至應用程式 URL，並瀏覽由服務提供者 （SP 起始驗證流程） 起始驗證流程。

| 案例 | 預期的結果，在使用者的 SP 起始驗證流程 |
|----------|---------------------------------------------------|
| 要在公司網路上的 IE 應用程式的登入。 | 整合式 Windows 驗證 (IWA) 就會發生與任何其他的提示。 |
| 登入應用程式，以關閉 公司網路與新的登入嘗試的 IE。 | 在 AD FS 伺服器表單為基礎的提示字元。 使用者成功登入，瀏覽器會提示進行 MFA。 |
| 登入應用程式，以關閉 公司網路與目前的工作階段的 IE，且永遠不會有執行 MFA。 | 使用者未收到提示的第一個因素。 使用者會收到提示適用於 MFA。 |
| 登入應用程式，以關閉 公司網路與目前的工作階段的 IE，已經在這個工作階段中執行 MFA。 | 使用者未收到提示的第一個因素。 使用者未收到 MFA。 使用者 SSOs 到應用程式。 |
| 登入應用程式使用 Chrome/Firefox/Safari 在關閉與目前的工作階段的公司網路時，已經在這個工作階段中執行 MFA。 | 使用者未收到提示的第一個因素。 使用者未收到 MFA。 使用者經由 SSO 的應用程式。 |
| 若要登入與 Chrome/Firefox/Safari 關閉公司網路與新的登入嘗試時的應用程式。 | 在 AD FS 伺服器表單為基礎的提示字元。 使用者成功登入，瀏覽器會提示進行 MFA。 |
| 使用 Chrome/Firefox 與目前的工作階段的公司網路上的應用程式的登入。 | 使用者未收到提示的第一個因素。 使用者未收到 MFA。 使用者經由 SSO 的應用程式。 |
| 登入應用程式與應用程式與新的登入嘗試的行動裝置應用程式。 | 在 AD FS 伺服器表單為基礎的提示字元。 使用者成功登入和 MFA 提示 ADAL 用戶端。 |
| 未經授權的使用者嘗試登入應用程式與登入 URL。 | 在 AD FS 伺服器表單為基礎的提示字元。 使用者無法登入，第一個因素。 |
| 已獲授權的使用者嘗試登入，但輸入不正確的密碼。 | 使用者瀏覽至應用程式 URL，並收到不正確的使用者名稱/密碼錯誤。 |
| 已獲授權的使用者按一下連結以電子郵件，並已通過驗證。 | 使用者按一下 URL，並已登入應用程式的任何其他的提示。 |
| 已獲授權的使用者按一下連結以電子郵件，以及未經驗證。 | 使用者按一下 URL，並為第一個要素驗證的提示。 |
| 授權使用者登入應用程式的行動裝置應用程式使用的應用程式 （SP 起始） 與新的登入嘗試。 | 在 AD FS 伺服器表單為基礎的提示字元。 使用者成功登入和 MFA 提示 ADAL 用戶端。 |
| 未經授權的使用者嘗試登入應用程式與登入 URL （SP 起始）。 | 在 AD FS 伺服器表單為基礎的提示字元。 使用者無法登入，第一個因素。 |
| 已獲授權的使用者嘗試登入，但輸入不正確的密碼。| 使用者瀏覽至應用程式 URL，並收到不正確的使用者名稱/密碼錯誤。 |
| 已獲授權的使用者登出並再次登入。 | 如果已設定 登出 URL，使用者已登出所有服務和驗證的提示字元。 |
| 已獲授權的使用者登出並再次登入。 | 如果未設定登出 URL，使用者將會自動記錄使用現有的權杖，從現有的 Azure AD 的瀏覽器工作階段內。 |
| 已獲授權的使用者按一下連結以電子郵件，並已通過驗證。 | 使用者按一下 URL，並已登入應用程式的任何其他的提示。 |
| 已獲授權的使用者按一下連結以電子郵件，以及未經驗證。 | 使用者按一下 URL，並為第一個要素驗證的提示。 |

## <a name="manage-sso"></a>管理 SSO

本節概述的需求和建議，以成功地管理 SSO。

### <a name="required-administrative-roles"></a>必要的系統管理角色

一律使用最少的權限可用來完成必要的工作，Azure Active Directory 內的角色。 Microsoft 建議[檢閱可用的不同角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)和選擇正確解決您的需求，為此應用程式的每個角色。 有些角色可能需要暫時套用和移除部署完成之後。

| 人物代表| 角色 | Azure AD 角色 （如有必要） |
|--------|-------|-----------------------------|
| 協助服務中心系統管理員 | 第 1 層支援 | None |
| 身分識別管理 | 設定和偵錯時的問題會影響 Azure AD | 全域管理員 |
| 應用程式系統管理員 | 在應用程式權限的使用者上設定的使用者證明 | None |
| 基礎結構系統管理員 | 憑證變換的擁有者 | 全域管理員 |
| 業務擁有者/專案關係人 | 在應用程式權限的使用者上設定的使用者證明 | None |

我們建議您使用[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) 來管理您的角色，以提供額外的稽核、 控制與存取權檢閱的目錄權限的使用者。

### <a name="sso-certificate-lifecycle-management"></a>SSO 憑證生命週期管理

請務必找出正確的角色，並傳送電子郵件通訊群組清單絕大多數負責管理 Azure AD 之間的簽章憑證的生命週期和應用程式設定單一登入。 以下是一些我們建議您備妥的主要角色：

- 更新應用程式中的使用者屬性的擁有者
- 擁有者上呼叫的應用程式協助修正支援
- 與憑證相關變更通知的密切監視的電子郵件通訊群組清單

憑證的最大存留期為三年。 我們建議您建立您將如何處理憑證變更 Azure AD 之間的處理程序和應用程式。 這有助於防止或減少因憑證到期而發生中斷或強制執行憑證變換。

### <a name="rollback-process"></a>回復程序

完成測試根據您的測試案例之後，就可以移至您的應用程式的生產環境。 移至生產環境時，表示您會在生產租用戶中實作您的計劃和測試組態，並推出給您的使用者。 不過，務必規劃萬一您的部署不會如預期，該怎麼辦。 如果 SSO 組態無法在部署期間，您必須了解如何減輕任何中斷情況，並降低對使用者的影響。

在應用程式中的驗證方法的可用性將會決定最佳的策略。 務必確定您有詳細的文件如何完全回到原始的登入組態狀態萬一您的部署會遇到問題的應用程式擁有者。

- **如果您的應用程式支援多個身分識別提供者**、 範例 LDAP 和 AD FS 和 Ping，不會刪除現有的 SSO 組態在首度發行期間。 相反地，停用它在移轉期間萬一您需要將其切換回更新版本。 

- **如果您的應用程式不支援多個 Idp**但可允許使用者使用表單型驗證 （使用者名稱/密碼） 登入，請確定該使用者可切換回這種方法如果新的 SSO 組態首度發行失敗。

### <a name="access-management"></a>存取管理

我們建議您選擇的擴充的方法，管理資源的存取權時。 常見的方法包括透過 Azure AD Connect 同步處理利用內部部署群組[在 Azure AD 中建立動態群組根據使用者屬性](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)，或建立[自助服務群組](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)在 Azure AD 中資源擁有者管理。

### <a name="monitor-security"></a>監視安全性

我們建議您設定一般的頻率，您可以在其中檢閱 SaaS 應用程式安全性的不同層面，並執行所需的任何修復動作。

### <a name="troubleshooting"></a>疑難排解

下列連結提供疑難排解案例。 若要建立您的支援人員，其中包含這些案例和步驟來修正它們的特定指南。

#### <a name="consent-issues"></a>同意的問題

- [未預期的同意錯誤](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [使用者同意錯誤](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>登入問題

- [從自訂的入口網站登入問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [從存取面板登入時發生問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [應用程式登入頁面發生錯誤](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [登入 Microsoft 應用程式的問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Azure 應用程式資源庫中列出的應用程式的 SSO 問題

- [Azure 應用程式資源庫中所列的密碼 SSO 應用程式的問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [與 Azure 應用程式資源庫中列出的應用程式的同盟 SSO 搭配運作的問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Azure 應用程式資源庫中未列出的應用程式的 SSO 問題

- [以 Azure 應用程式資源庫中未列出的應用程式的密碼 SSO 的問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [與 Azure 應用程式資源庫中未列出的應用程式的同盟 SSO 搭配運作的問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>後續步驟

[偵錯 SAML 型 SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[透過 PowerShell 的應用程式的宣告對應](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[自訂在 SAML 權杖中發出的宣告](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[單一登入 SAML 通訊協定](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[單一登出 SAML 通訊協定](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) （適用於外部使用者，例如合作夥伴和廠商）

[Azure AD 條件式存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[SSO 存取](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[應用程式 SSO 教學課程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
