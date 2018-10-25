---
title: 在 Azure Active Directory B2C 中使用自訂原則來設定以 LinkedIn 帳戶進行登入 | Microsoft Docs
description: 在 Azure Active Directory B2C 中使用自訂原則來設定以 Google 帳戶進行登入。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b5022e1475b9f15738dd015e16946b754fcd49c9
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887303"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定以 LinkedIn 帳戶進行登入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

此文章說明如何在 Azure Active Directory (Azure AD) B2C 中透過使用[自訂原則](active-directory-b2c-overview-custom.md)，讓使用者能夠從 LinkedIn 帳戶登入。

## <a name="prerequisites"></a>先決條件

- 完成[在 Azure Active Directory B2C 中開始使用自訂原則](active-directory-b2c-get-started-custom.md)中的步驟。
- 如果您還沒有 LinkedIn 帳戶，請在 [LinkedIn 註冊頁面](https://www.linkedin.com/start/join)建立一個帳戶。
- LinkedIn 應用程式會要求您提供一個 80 X 80 像素的標誌影像來代表您的應用程式。

## <a name="create-an-application"></a>建立應用程式

若要使用 LinkedIn 作為 Azure AD B2C 中的識別提供者，您必須建立 LinkedIn 應用程式。

1. 使用您的 LinkedIn 帳戶認證來登入 [LinkedIn 應用程式管理](https://www.linkedin.com/secure/developer?newapp=)網站。
2. 選取 [Create Application] \(建立應用程式\)。
3. 輸入您的 [Company Name] \(公司名稱\)、[Application Name] \(應用程式名稱\) 及 [Application Description] \(應用程式描述\)。
4. 上傳您建立的 [Application Logo] \(應用程式標誌\)。
5. 從所提供的清單中選擇一個 [Application Use] \(應用程式用途\)。
6. 針對 [Website URL] \(網站 URL\)，輸入 `https://your-tenant.b2clogin.com`。  將 `your-tenant` 取代為您的 Azure AD B2C 租用戶名稱。 例如 contoso.b2clogin.com。
7. 輸入您的 [Business Email] \(公司電子郵件\) 地址和 [Business Phone] \(公司電話\) 號碼。
8. 在頁面底部，閱讀並接受使用條款，然後選取 [提交]。
9. 選取 [Authentication] \(驗證\)，然後記錄 [Client ID] \(用戶端識別碼\) 和 [Client Secret] \(用戶端密碼\) 值以供稍後使用。
10. 在 [Authorized Redirect URLs] \(授權的重新導向 URL\) 中，輸入 `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`。 以您的租用戶名稱取代 `your-tenant-name`。 即使租用戶在 Azure AD B2C 中是使用大寫字母來定義的，您還是需要在輸入租用戶名稱時，全部使用小寫字母。 
11. 選取 [更新]。
12. 選取 [設定]、將 [應用程式狀態] 變更為 [即時]，然後選取 [更新]。

## <a name="create-a-policy-key"></a>建立原則金鑰

您必須將先前記錄的用戶端密碼儲存在 Azure AD B2C 租用戶中。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 在 [概觀] 頁面上，選取 [識別體驗架構 - 預覽]。
5. 選取 [原則金鑰]，然後選取 [新增]。
6. 針對 [選項] 選擇 `Manual`。
7. 輸入原則金鑰的 [名稱]。 例如： `LinkedInSecret`。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
8. 在 [祕密] 中，輸入您先前記錄的用戶端密碼。
9. 針對 [金鑰使用方法]，選取 `Signature`。
10. 按一下頁面底部的 [新增] 。

## <a name="add-a-claims-provider"></a>新增宣告提供者

如果想要讓使用者使用 LinkedIn 帳戶進行登入，您必須將該帳戶定義成 Azure AD B2C 能夠透過端點與之通訊的宣告提供者。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。 

您可以藉由將 LinkedIn 帳戶新增至原則擴充檔中的 **ClaimsProviders** 元素，將其定義成宣告提供者。

1. 開啟 *TrustFrameworkExtensions.xml*。
2. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增它。
3. 新增新的 **ClaimsProvider**，如下所示：

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
            <Item Key="ClaimsEndpointAccessTokenName">oauth2_access_token</Item>
            <Item Key="ClaimsEndpointFormatName">format</Item>
            <Item Key="ClaimsEndpointFormat">json</Item>
            <Item Key="scope">r_emailaddress r_basicprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
            <!--<OutputClaim ClaimTypeReferenceId="jobTitle" PartnerClaimType="headline" />-->
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. 以您先前記錄的用戶端識別碼取代 **client_id** 的值。
5. 儲存檔案。

### <a name="upload-the-extension-file-for-verification"></a>上傳擴充檔案準備驗證

到目前為止，您已設定原則，讓 Azure AD B2C 知道如何與您的 LinkedIn 帳戶進行通訊。 嘗試上傳原則的擴充檔案，這只是為了確認它到目前為止沒有任何問題。

1. 在 Azure AD B2C 租用戶的 [自訂原則] 頁面上，選取 [上傳原則]。
2. 啟用 [覆寫現有的原則]，然後瀏覽並選取 [TrustFrameworkExtensions.xml] 檔案。
3. 按一下 [上傳] 。

## <a name="register-the-claims-provider"></a>註冊宣告提供者

此時，識別提供者已設定妥當，但還未出現在任何註冊或登入畫面中。 若要讓它可供使用，您必須建立現有範本使用者旅程圖的複本，然後修改它，讓它也包含 LinkedIn 識別提供者。

1. 從 Starter Pack 開啟 *TrustFrameworkBase.xml* 檔案。
2. 尋找並複製包含 `Id="SignUpOrSignIn"` 之 **UserJourney** 元素的整個內容。
3. 開啟 *TrustFrameworkExtensions.xml*，並尋找 **UserJourneys** 元素。 如果此元素不存在，請新增。
4. 貼上您複製的整個 **UserJourney** 元素內容作為 **UserJourneys** 元素的子系。
5. 重新命名使用者旅程圖的識別碼。 例如： `SignUpSignInLinkedIn`。

### <a name="display-the-button"></a>顯示按鈕

**ClaimsProviderSelection** 元素類似於註冊或登入畫面上的識別提供者按鈕。 如果您為 LinkedIn 帳戶新增 **ClaimsProviderSelection** 元素，當使用者登陸頁面時，就會出現新按鈕。

1. 在您建立的使用者旅程圖中，尋找包含 `Order="1"` 的 **OrchestrationStep** 元素。
2. 在 **ClaimsProviderSelects** 底下，新增下列元素。 將 **TargetClaimsExchangeId** 的值設定成適當的值，例如 `LinkedInExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>將按鈕連結至動作

現在已備妥按鈕，您需要將它連結至動作。 在此案例中，動作是用來讓 Azure AD B2C 與 LinkedIn 帳戶進行通訊以接收權杖。

1. 在使用者旅程圖中，尋找包含 `Order="2"` 的 **OrchestrationStep**。
2. 新增下列 **ClaimsExchange** 元素，請確定用於 **Id** 的值與用於 **TargetClaimsExchangeId** 的值相同：

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```
    
    將 **TechnicalProfileReferenceId** 的值更新成您稍早所建立技術設定檔的 **Id**。 例如： `LinkedIn-OAUTH`。

3. 儲存*TrustFrameworkExtensions.xml* 檔案，並再次上傳它以供驗證。

## <a name="create-an-azure-ad-b2c-application"></a>建立 Azure AD B2C 應用程式

與 Azure AD B2C 的通訊會透過您在租用戶中建立的應用程式進行。 此節會列出您可以視需要完成以建立測試應用程式的步驟 (如果您尚未這麼做)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 選取 [應用程式]，然後選取 [新增]。
5. 輸入應用程式的名稱，例如 *testapp1*。
6. 針對 [Web 應用程式 / Web API]，選取 `Yes`，然後針對 [回覆 URL] 輸入 `https://jwt.ms`。
7. 按一下頁面底部的 [新增] 。

## <a name="update-and-test-the-relying-party-file"></a>更新並測試信賴憑證者檔案

更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 在您的工作目錄中建立一份 *SignUpOrSignIn.xml* 複本，並將它重新命名。 例如，將它重新命名為 *SignUpSignInLinkedIn.xml*。
2. 開啟新檔案，並將 **TrustFrameworkPolicy** 的 **PolicyId** 屬性更新成唯一值。 例如： `SignUpSignInLinkedIn`。
3. 將 **PublicPolicyUri** 的值更新成原則的 URI。 例如 `http://contoso.com/B2C_1A_signup_signin_linkedin`
4. 更新 **DefaultUserJourney** 中 **ReferenceId** 屬性的值，以符合您所建立新使用者旅程圖 (SignUpSignLinkedIn) 的識別碼。
5. 儲存您的變更、上傳檔案，然後選取清單中的新原則。
6. 確定 [選取應用程式] 欄位中已選取您所建立的 Azure AD B2C 應用程式，然後按一下 [立即執行]來進行測試。
