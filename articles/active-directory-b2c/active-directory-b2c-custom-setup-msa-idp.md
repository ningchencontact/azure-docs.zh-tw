---
title: 在 Azure Active Directory B2C 中使用自訂原則新增 Microsoft 帳戶 (MSA) 作為識別提供者 | Microsoft Docs
description: 透過 OpenID 連線 (OIDC) 通訊協定使用 Microsoft 作為識別提供者的範例。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 4bcc51c3efce95178dbb190eb86cb7ac8e224cd0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55187516"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定以 Microsoft 帳戶進行登入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文說明如何在 Azure Active Directory (Azure AD) B2C 中藉由使用[自訂原則](active-directory-b2c-overview-custom.md)，讓使用者能夠從 Microsoft 帳戶登入。

## <a name="prerequisites"></a>必要條件

- 完成在 [Azure Active Directory B2C 中開始使用自訂原則](active-directory-b2c-get-started-custom.md)中的步驟。
- 如果您還沒有 Microsoft 帳戶，請前往 [https://www.live.com/](https://www.live.com/) 來建立。

## <a name="add-an-application"></a>新增應用程式

若要在 Azure AD B2C 中使用 Microsoft 帳戶作為識別提供者，您必須新增 Microsoft 帳戶應用程式。

1. 請使用您的 Microsoft 帳戶認證登入 [Microsoft 應用程式註冊入口網站](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)。
2. 在右上角選取 [新增應用程式]。
3. 輸入 [應用程式名稱]，然後按一下 [建立] 
4. 選取 [產生新密碼]，並確定您會複製該密碼，以便在設定識別提供者時使用。 此外，還要複製**應用程式識別碼**。 
5. 在 [重新導向 URL] 中，輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 以您的租用戶名稱取代 `your-tenant-name`。
6. 選取 [ **儲存**]。

## <a name="create-a-policy-key"></a>建立原則金鑰

您必須將先前產生並記錄的密碼儲存在 Azure AD B2C 租用戶中。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 在 [概觀] 頁面上，選取 [識別體驗架構 - 預覽]。
5. 選取 [原則金鑰]，然後選取 [新增]。
6. 針對 [選項] 選擇 `Manual`。
7. 輸入原則金鑰的 [名稱]。 例如： `MSASecret`。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
8. 在 [祕密] 中，輸入您先前記錄的密碼。
9. 針對 [金鑰使用方法]，選取 `Signature`。
10. 按一下頁面底部的 [新增] 。

## <a name="add-a-claims-provider"></a>新增宣告提供者

如果想要讓使用者使用 Microsoft 帳戶進行登入，您必須將該帳戶定義為 Azure AD B2C 能夠透過端點與之通訊的宣告提供者。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。 

您可以藉由在原則擴充檔中新增 **ClaimsProvider** 元素，將 Azure AD 定義成宣告提供者。

1. 開啟 *TrustFrameworkExtensions.xml*。
2. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增。
3. 新增新的 **ClaimsProvider**，如下所示：

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Microsoft application client id</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" />
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

4.  以您先前記錄的應用程式識別碼取代 **client_id** 的值。
5.  儲存檔案。

### <a name="upload-the-extension-file-for-verification"></a>上傳擴充檔案準備驗證

到目前為止，您已設定原則，讓 Azure AD B2C 知道如何與 Microsoft 帳戶進行通訊。 嘗試上傳原則的擴充檔案，這只是為了確認它到目前為止沒有任何問題。

1. 在 Azure AD B2C 租用戶的 [自訂原則] 頁面上，選取 [上傳原則]。
2. 啟用 [覆寫現有的原則]，然後瀏覽並選取 *TrustFrameworkExtensions.xml* 檔案。
3. 按一下 [上傳] 。

## <a name="register-the-claims-provider"></a>註冊宣告提供者

此時，識別提供者已設定妥當，但還未出現在任何註冊或登入畫面中。 若要讓其可供使用，您需建立現有範本使用者旅程圖的複本，然後加以修改，讓它也包含 Microsoft 帳戶識別提供者。

1. 從 Starter Pack 開啟 TrustFrameworkBase.xml 檔案。
2. 尋找並複製包含 `Id="SignUpOrSignIn"` 之 **UserJourney** 元素的整個內容。
3. 開啟 *TrustFrameworkExtensions.xml*，並尋找 **UserJourneys** 元素。 如果此元素不存在，請新增。
4. 貼上您複製的整個 **UserJourney** 元素內容作為 **UserJourneys** 元素的子系。
5. 重新命名使用者旅程圖的識別碼。 例如： `SignUpSignInMSA`。

### <a name="display-the-button"></a>顯示按鈕

**ClaimsProviderSelection** 元素類似於註冊或登入畫面上的識別提供者按鈕。 如果您為 Microsoft 帳戶新增 **ClaimsProviderSelection** 元素，當使用者登陸頁面時，就會出現新按鈕。

1. 在 TrustFrameworkExtensions.xml 檔案中，於您所建立的使用者旅程圖中尋找包含 `Order="1"` 的 **OrchestrationStep** 元素。
2. 在 **ClaimsProviderSelects** 底下新增下列元素。 將 **TargetClaimsExchangeId** 的值設定成適當的值，例如 `MicrosoftAccountExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>將按鈕連結至動作

現在已備妥按鈕，您需要將它連結至動作。 在此案例中，動作是用來讓 Azure AD B2C 與 Twitter 帳戶進行通訊以接收權杖。

1. 在使用者旅程圖中，尋找包含 `Order="2"` 的 **OrchestrationStep**。
2. 新增下列 **ClaimsExchange** 元素，請確定用於 **Id** 的值與用於 **TargetClaimsExchangeId** 的值相同：

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```
    
    將 **TechnicalProfileReferenceId** 的值更新成您稍早所建立技術設定檔的 **Id**。 例如： `MSA-OIDC`。

3. 儲存 TrustFrameworkExtensions.xml 檔案，並再次上傳它以供驗證。

## <a name="create-an-azure-ad-b2c-application"></a>建立 Azure AD B2C 應用程式

與 Azure AD B2C 的通訊會透過您在租用戶中建立的應用程式進行。 此節會列出您可以視需要完成以建立測試應用程式的步驟 (如果您尚未這麼做)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 選取 [應用程式]，然後選取 [新增]。
5. 輸入應用程式的名稱，例如 testapp1。
6. 針對 [Web 應用程式 / Web API] ，選取 `Yes`，然後y在 [回覆 URL] 欄位輸入 `https://jwt.ms`。
7. 按一下頁面底部的 [新增] 。

## <a name="update-and-test-the-relying-party-file"></a>更新並測試信賴憑證者檔案

更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 在您的工作目錄中建立一份 SignUpOrSignIn.xml 複本，並將它重新命名。 例如，將它重新命名為 SignUpSignInMSA.xml。
2. 開啟新檔案，並將 **TrustFrameworkPolicy** 的 **PolicyId** 屬性更新成唯一值。 例如： `SignUpSignInMSA`。
3. 將 **PublicPolicyUri** 的值更新成原則的 URI。 例如：`http://contoso.com/B2C_1A_signup_signin_msa`
4. 更新 **DefaultUserJourney** 中 **ReferenceId** 屬性的值，以符合您所建立新使用者旅程圖 (SignUpSignInMSA) 的識別碼。
5. 儲存您的變更、上傳檔案，然後選取清單中的新原則。
6. 確定 [選取應用程式] 欄位中已選取您所建立的 Azure AD B2C 應用程式，然後按一下 [立即執行] 來進行測試。
