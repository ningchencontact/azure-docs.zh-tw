---
title: 設定適用於 B2B-Azure Active Directory 身分識別提供者的直接同盟 |Microsoft Docs
description: 直接與 SAML 或 Ws-fed 的身分識別提供者同盟讓來賓可以登入您的 Azure AD 應用程式
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bc3c1325e8379082134e2cbec1586f7d338ee61
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797929"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>搭配 AD FS 與來賓使用者 （預覽） 的協力廠商提供者的直接同盟
|     |
| --- |
| 直接同盟是 Azure Active directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

本文說明如何設定與另一個組織的 B2B 共同作業的直接同盟。 您可以設定與任何組織的身分識別提供者 (IdP) 支援 SAML 2.0 或 Ws-fed 通訊協定的直接同盟。
當您設定與協力廠商的 IdP 的直接同盟時，該網域中的新增來賓使用者可以使用自己 IdP 管理組織帳戶登入 Azure AD 租用戶，並開始與您共同作業。 建立個別的來賓使用者不需要 Azure AD 帳戶。
> [!NOTE]
> 直接同盟來賓使用者必須使用登入連結，其中包含租用戶內容 (例如`https://myapps.microsoft.com/?tenantid=<tenant id>`或`https://portal.azure.com/<tenant id>`，或者，如果已驗證網域， `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`)。 應用程式和資源的直接連結只要包含租用戶內容，也可有同樣作用。 直接同盟使用者是目前無法使用沒有租用戶內容的通用端點來登入。 例如，使用`https://myapps.microsoft.com`， `https://portal.azure.com`，或`https://teams.microsoft.com`會導致錯誤。
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>以直接聯盟時驗證為來賓使用者？
設定與組織的直接同盟之後，就會使用直接同盟來驗證任何新您邀請的來賓使用者。 請務必注意建立直接同盟設定不會變更已兌換您邀請的來賓使用者的驗證方法。 以下是一些範例：
 - 如果來賓使用者已兌換的而且您之後設定直接同盟與組織的邀請，這些來賓使用者會繼續使用相同的驗證方法，然後再設定直接同盟使用它們。
 - 如果您直接與夥伴組織的同盟設定，並邀請來賓使用者，然後稍後在夥伴組織將移至 Azure AD 已兌換邀請的來賓使用者會繼續使用直接聯盟，只要直接同盟租用戶中的原則存在。
 - 如果您刪除直接與夥伴組織的同盟，則任何目前正在使用直接同盟的來賓使用者將無法登入。

在任何這些情況下，您可以藉由從您的目錄中刪除 guest 使用者帳戶，並重新邀請他們更新來賓使用者的驗證方法。

直接同盟繫結至網域命名空間，例如 contoso.com 和 fabrikam.com。 當建立直接同盟設定與 AD FS 或第三方 IdP，組織會將一或多個網域命名空間，以這些 Idp 產生關聯。 

## <a name="end-user-experience"></a>使用者體驗 
以直接聯盟，來賓使用者登入使用組織帳戶的 Azure AD 租用戶。 當他們存取共用的資源，然後系統會提示輸入登入時，直接同盟使用者重新導向至其 IdP 中。 成功登入之後，則會傳回至 Azure AD 來存取資源。 直接同盟使用者的重新整理權杖的有效期為 12 小時，[預設長度為通過重新整理權杖](../develop/active-directory-configurable-token-lifetimes.md#exceptions)在 Azure AD 中。 如果同盟的 IdP 有已啟用 SSO，使用者會體驗 SSO，並在初始驗證之後看不到任何登入提示。

## <a name="limitations"></a>限制

### <a name="dns-verified-domains-in-azure-ad"></a>在 Azure AD 中的 DNS 驗證的網域
直接同盟只允許的網域***不***在 Azure AD 中驗證 DNS。 受控 （電子郵件驗證或 「 病毒式 」） 的 Azure AD 租用戶，因為它們不是 DNS 驗證允許直接聯盟。
### <a name="authentication-url"></a>驗證 URL
直接同盟只會允許原則，其中驗證 URL 的網域必須符合目標網域，或其中的驗證 URL 是其中一個允許的身分識別提供者 （此清單會隨時變更）：
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

例如，當設定為直接聯盟**fabrikam.com**，將驗證 URL`https://fabrikam.com/adfs`會通過驗證。 在相同的網域中的主機也會傳遞，例如`https://sts.fabrikam.com/adfs`。 不過，驗證 URL`https://fabrikamconglomerate.com/adfs`或`https://fabrikam.com.uk/adfs`針對相同的網域不會通過。

### <a name="signing-certificate-renewal"></a>簽署的憑證更新
如果您的身分識別提供者設定中指定的中繼資料 URL，Azure AD 會自動更新簽署憑證到期時。 不過，如果憑證在到期時間之前旋轉因為任何原因，或如果您未提供中繼資料 URL，Azure AD 將無法更新它。 在此情況下，您必須手動更新簽署憑證。
## <a name="frequently-asked-questions"></a>常見問題集
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>我可以設定與網域，存在 （電子郵件驗證） 非受控租用戶的直接同盟嗎？ 
是的。 如果已驗證網域和租用戶尚未經過[管理員接管](../users-groups-roles/domains-admin-takeover.md)，您可以設定直接聯盟。 在使用者兌換的 B2B 邀請，或執行使用目前不存在的網域的 Azure ad 的自助式註冊時，會建立未受管理，或以電子郵件驗證的租用戶。 您可以直接與這些網域的同盟設定。 如果您嘗試建立直接同盟使用 DNS 驗證網域時，在 Azure 入口網站中或透過 PowerShell，您會看到錯誤。
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>如果同時啟用直接同盟和電子郵件單次密碼驗證，哪一種方法的優先順序較高？
與夥伴組織建立直接同盟時，它的優先順序高於新的來賓使用者，來自該組織的電子郵件單次密碼驗證。 如果來賓使用者兌換邀請使用單次密碼驗證，然後再設定直接聯盟，它們會繼續使用單次密碼驗證。 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>由於部分已同步處理的租用戶 direct 位址的同盟登入問題？
不對[單次密碼電子郵件傳送](one-time-passcode.md)功能應在此案例中使用。 「 部分同步租用戶 」 指的是，內部部署使用者的身分識別未完全同步處理至雲端的協力廠商 Azure AD 租用戶。 其身分識別尚未存在於雲端，但使用者嘗試兌換您的 B2B 邀請的來賓將無法登入。 單次密碼功能可讓這個登入的來賓。 直接同盟功能可解決其中客體有其自己 IdP 管理組織帳戶，但組織有沒有 Azure AD 支援所有的案例。

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>步驟 1：設定夥伴組織的身分識別提供者
首先，夥伴組織必須具有必要的宣告和信賴憑證者信任設定其身分識別提供者。 

> [!NOTE]
> 為了說明如何設定直接同盟的身分識別提供者，我們將使用 Active Directory Federation Services (AD FS) 作為範例。 請參閱文章[設定與 AD FS 的直接同盟](direct-federation-adfs.md)，提供有關如何設定 AD FS 做為 SAML 2.0 或 Ws-fed 身分識別提供者以直接聯盟準備的範例。

### <a name="saml-20-configuration"></a>SAML 2.0 設定

可以設定 azure AD B2B，與下面所列的特定需求使用 SAML 通訊協定的身分識別提供者同盟。 如需有關設定您的 SAML 識別提供者與 Azure AD 之間建立信任的詳細資訊，請參閱[使用 SAML 2.0 識別提供者 (IdP) 的單一登入](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp)。  

> [!NOTE]
> 請注意直接同盟的目標網域不能 DNS 驗證 Azure ad。 驗證 URL 網域必須符合目標網域，或者它必須是允許的身分識別提供者的網域。 請參閱[限制](#limitations)一節以取得詳細資料。 

#### <a name="required-saml-20-attributes-and-claims"></a>必要的 SAML 2.0 屬性和宣告
下表顯示特定的屬性，以及必須在第三方識別提供者設定的需求。 若要設定直接聯盟，必須從身分識別提供者 SAML 2.0 回應中收到下列屬性。 藉由連結到線上的安全性權杖服務的 XML 檔案或手動輸入認證，可以設定這些屬性。

IdP 的 SAML 2.0 回應的必要的屬性：

|屬性  |值  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|對象     |`urn:federation:MicrosoftOnline`         |
|簽發者     |簽發者的夥伴 IdP，如範例的 URI `http://www.example.com/exk10l6w90DHM0yi...`         |


必要的 IdP 所簽發之 SAML 2.0 權杖的宣告：

|屬性  |值  |
|---------|---------|
|NameID 格式     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>Ws-fed 組態 
Azure AD B2B 可以設定為與特定需求使用 Ws-fed 通訊協定，如下所示的身分識別提供者同盟。 目前，Azure AD 與相容性包括 AD FS 與 Shibboleth 經過測試兩個 Ws-fed 提供者。 如需有關建立信賴憑證者合作對象之間的信任 Ws-fed 相容的提供者與 Azure AD 的詳細資訊，請參閱 「 STS 整合紙張使用 WS 通訊協定 」 中可用[Azure AD 身分識別提供者相容性文件](https://www.microsoft.com/download/details.aspx?id=56843)。

> [!NOTE]
> 直接同盟的目標網域必須不是 DNS 驗證 Azure ad。 驗證 URL 網域必須符合目標網域或允許的身分識別提供者的網域。 請參閱[限制](#limitations)一節以取得詳細資料。 

#### <a name="required-ws-fed-attributes-and-claims"></a>必要 Ws-fed 屬性和宣告

下表顯示特定的屬性，以及必須在第三方 Ws-fed 身分識別提供者設定的需求。 若要設定直接聯盟，下列屬性必須是 Ws-fed 在訊息中收到來自識別提供者。 藉由連結到線上的安全性權杖服務的 XML 檔案或手動輸入認證，可以設定這些屬性。

從 IdP Ws-fed 訊息中的必要的屬性：
 
|屬性  |值  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|對象     |`urn:federation:MicrosoftOnline`         |
|簽發者     |簽發者的夥伴 IdP，如範例的 URI `http://www.example.com/exk10l6w90DHM0yi...`         |

必要的 Ws-fed 權杖的 IdP 所簽發的宣告：

|屬性  |值  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>步驟 2：在 Azure AD 中設定直接同盟 
接下來，您會設定與 Azure AD 中設定步驟 1 中的身分識別提供者同盟。 您可以使用的 Azure AD 入口網站或 PowerShell。 可能需要 5-10 分鐘，才能直接聯盟原則會生效。 在此期間，請勿嘗試兌換邀請的直接同盟網域。 以下為必要屬性：
- 簽發者夥伴 IdP URI
- 協力廠商 IdP （支援僅 https） 的被動驗證端點
- 憑證

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>若要設定 Azure AD 入口網站中的直接同盟

1. 移至 [Azure 入口網站](https://portal.azure.com/)。 在左窗格中，選取 [Azure Active Directory]  。 
2. 選取 [組織關係]  。
3. 選取 **身分識別提供者**，然後選取**新 SAML/Ws-fed IdP**。

    ![新增新的 SAML 或 Ws-fed IdP 的螢幕擷取畫面顯示按鈕](media/direct-federation/new-saml-wsfed-idp.png)

4. 在 **新 SAML/Ws-fed IdP**頁面的 **身分識別提供者通訊協定**，選取**SAML**或是**WS-FED**。

    ![在 SAML 或 Ws-fed IdP 頁面的螢幕擷取畫面顯示剖析按鈕](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. 輸入夥伴組織的網域名稱，將會直接同盟的目標網域名稱
6. 您可以上傳中繼資料檔案，以便填入中繼資料的詳細資料。 如果您選擇手動輸入中繼資料，請輸入下列資訊：
   - 夥伴 IdP 的網域名稱
   - 協力廠商 IdP 實體識別碼
   - 夥伴 IdP 的被動式要求者端點
   - 憑證
   > [!NOTE]
   > 中繼資料 URL 是選擇性的但是我們強烈建議。 如果您提供的中繼資料 URL 時，Azure AD 可以自動更新的簽署憑證到期時。 如果憑證的到期時間之前，會旋轉因為任何原因或者如果您未提供中繼資料 URL，Azure AD 無法更新它。 在此情況下，您必須手動更新簽署憑證。

7. 選取 [ **儲存**]。 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>若要使用 PowerShell 的 Azure AD 中設定直接同盟

1. 安裝最新版的 Azure AD PowerShell for Graph 模組 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。 (如果您需要詳細的步驟，新增來賓使用者的快速入門包含一節[安裝最新的 AzureADPreview 模組](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module)。) 
2. 執行下列命令： 
   ```powershell
   Connect-AzureAD
   ```
1. 在出現登入提示時，使用受控的全域系統管理員帳戶來登入。 
2. 執行下列命令，將 同盟中繼資料檔案中的值。 對於 AD FS 伺服器與 Okta，同盟檔案是 federationmetadata.xml，例如： `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`。 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>步驟 3：測試 Azure AD 中的直接同盟
邀請新的 B2B 來賓使用者，現在測試您的直接同盟設定。 如需詳細資訊，請參閱 <<c0> [ 在 Azure 入口網站中的新增 Azure AD B2B 共同作業使用者](add-users-administrator.md)。
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>我該如何編輯的直接同盟關聯性？

1. 移至 [Azure 入口網站](https://portal.azure.com/)。 在左窗格中，選取 [Azure Active Directory]  。 
2. 選取 [組織關係]  。
3. 選取**身分識別提供者**
4. 底下**SAML/Ws-fed 身分識別提供者**，選取提供者。
5. 在 [識別提供者詳細資料] 窗格中，更新值。
6. 選取 [ **儲存**]。


## <a name="how-do-i-remove-direct-federation"></a>如何移除直接同盟？
您可以移除您的直接同盟設定。 如果您這樣做，則直接同盟已兌換其邀請的來賓使用者，將無法登入。 但是，您可以為他們提供存取您的資源再次從目錄刪除並重新邀請他們。 若要在 Azure AD 入口網站移除直接同盟身分識別提供者：

1. 移至 [Azure 入口網站](https://portal.azure.com/)。 在左窗格中，選取 [Azure Active Directory]  。 
2. 選取 [組織關係]  。
3. 選取 **身分識別提供者**。
4. 選取身分識別提供者，然後選取**刪除**。 
5. 選取 [是]  以確認要刪除。 

若要使用 PowerShell 移除直接同盟身分識別提供者：
1. 安裝最新版的 Azure AD PowerShell for Graph 模組 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 執行下列命令： 
   ```powershell
   Connect-AzureAD
   ```
3. 在出現登入提示時，使用受控的全域系統管理員帳戶來登入。 
4. 輸入下列命令：
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```
