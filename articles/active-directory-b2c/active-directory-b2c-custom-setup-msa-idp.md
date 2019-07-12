---
title: 新增 Microsoft 帳戶 (MSA) 作為身分識別提供者在 Azure Active Directory B2C 中使用自訂原則
description: 透過 OpenID 連線 (OIDC) 通訊協定使用 Microsoft 作為識別提供者的範例。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9ae944a9d587e71c4be83bd524cf3875a7b52dd1
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654160"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定以 Microsoft 帳戶進行登入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文說明如何在 Azure Active Directory (Azure AD) B2C 中藉由使用[自訂原則](active-directory-b2c-overview-custom.md)，讓使用者能夠從 Microsoft 帳戶登入。

## <a name="prerequisites"></a>先決條件

- 完成在 [Azure Active Directory B2C 中開始使用自訂原則](active-directory-b2c-get-started-custom.md)中的步驟。
- 如果您還沒有 Microsoft 帳戶，請前往 [https://www.live.com/](https://www.live.com/) 來建立。

## <a name="add-an-application"></a>新增應用程式

若要啟用的使用者具有 Microsoft 帳戶登入，您需要註冊 Azure AD 租用戶內的應用程式。 此 Azure AD 租用戶與您的 Azure AD B2C 租用戶不同。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 按一下頂端功能表中的 [目錄和訂用帳戶篩選]  ，然後選擇包含您 Azure AD 租用戶的目錄，以確定您使用的是包含 Azure AD 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]  ，然後搜尋並選取 [應用程式註冊]  。
1. 選取 [新增註冊]  。
1. 輸入應用程式的**名稱**。 例如 *MSAapp1*。
1. 底下**支援的帳戶類型**，選取**中任何組織的目錄和個人 Microsoft 帳戶 (例如 Skype、 Xbox、 Outlook.com) 帳戶**。
1. 底下 **（選擇性） 的 重新導向 URI**，選取**Web** ，然後輸入`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`在文字方塊中。 取代`your-tenant-name`以您的 Azure AD B2C 租用戶名稱。
1. 選取**註冊**
1. 資料錄**應用程式 （用戶端） 識別碼**應用程式的 [概觀] 頁面上所示。 您需要此後續小節中設定宣告提供者時。
1. 選取**憑證與密碼**
1. 按一下 **新的用戶端祕密**
1. 請輸入**描述**祕密，例如*MSA 應用程式用戶端祕密*，然後按一下**新增**。
1. 記錄中顯示的應用程式密碼**值**資料行。 您可以使用此值在下一節。

## <a name="create-a-policy-key"></a>建立原則金鑰

既然您已在 Azure AD 租用戶中建立應用程式，您需要將該應用程式的用戶端祕密儲存在 Azure AD B2C 租用戶。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 請確定您使用包含 Azure AD B2C 租用戶的目錄。 選取 **目錄和訂用帳戶篩選**上方功能表中，然後選擇包含您的租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
1. 在 [概觀] 頁面上，選取 [識別體驗架構]  。
1. 選取 [原則金鑰]  ，然後選取 [新增]  。
1. 針對 [選項]  選擇 `Manual`。
1. 輸入原則金鑰的 [名稱]  。 例如： `MSASecret` 。 金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
1. 在 **祕密**，輸入您在上一節中記錄用戶端祕密。
1. 針對 [金鑰使用方法]  ，選取 `Signature`。
1. 按一下 [建立]  。

## <a name="add-a-claims-provider"></a>新增宣告提供者

若要讓使用者使用 Microsoft 帳戶登入，您需要帳戶定義為宣告提供者，Azure AD B2C 可透過端點通訊。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。

您可以藉由在原則擴充檔中新增 **ClaimsProvider** 元素，將 Azure AD 定義成宣告提供者。

1. 開啟*TrustFrameworkExtensions.xml*原則檔案。
1. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增。
1. 新增新的 **ClaimsProvider**，如下所示：

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
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
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

1. 值取代**client_id**與 Azure AD 應用程式*應用程式 （用戶端） 識別碼*稍早所記錄。
1. 儲存檔案。

您現在已設定您的原則，讓 Azure AD B2C 知道如何與 Azure AD 中的 Microsoft 帳戶應用程式進行通訊。

### <a name="upload-the-extension-file-for-verification"></a>上傳擴充檔案準備驗證

在繼續之前，將上傳已修改的原則，以確認它不會有任何問題為止。

1. 瀏覽至您的 Azure AD B2C 租用戶，在 Azure 入口網站，然後選取**身分識別體驗架構**。
1. 在 **自訂原則**頁面上，選取**上傳自訂原則**。
1. 啟用 [覆寫現有的原則]  ，然後瀏覽並選取 *TrustFrameworkExtensions.xml* 檔案。
1. 按一下 [上傳]  。

如果在入口網站中沒有顯示任何錯誤，繼續進行下一節。

## <a name="register-the-claims-provider"></a>註冊宣告提供者

此時，您已設定的身分識別提供者，但它尚無法在任何註冊或登入畫面。 若要提供，建立重複的現有範本使用者旅程圖，然後修改它，使它也有 Microsoft 帳戶識別提供者。

1. 從 Starter Pack 開啟 TrustFrameworkBase.xml  檔案。
1. 尋找並複製包含 `Id="SignUpOrSignIn"` 之 **UserJourney** 元素的整個內容。
1. 開啟 *TrustFrameworkExtensions.xml*，並尋找 **UserJourneys** 元素。 如果此元素不存在，請新增。
1. 貼上您複製的整個 **UserJourney** 元素內容作為 **UserJourneys** 元素的子系。
1. 重新命名使用者旅程圖的識別碼。 例如： `SignUpSignInMSA` 。

### <a name="display-the-button"></a>顯示按鈕

**ClaimsProviderSelection** 元素類似於註冊或登入畫面上的識別提供者按鈕。 如果您加入**ClaimsProviderSelection**當使用者登陸頁面時，會顯示為 Microsoft 帳戶，新增按鈕的項目。

1. 在 TrustFrameworkExtensions.xml  檔案中，於您所建立的使用者旅程圖中尋找包含 `Order="1"` 的 **OrchestrationStep** 元素。
1. 在 **ClaimsProviderSelects** 底下新增下列元素。 將 **TargetClaimsExchangeId** 的值設定成適當的值，例如 `MicrosoftAccountExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>將按鈕連結至動作

現在已備妥按鈕，您需要將它連結至動作。 動作在此情況下，是讓 Azure AD B2C 與 Microsoft 帳戶通訊以接收權杖。

1. 在使用者旅程圖中，尋找包含 `Order="2"` 的 **OrchestrationStep**。
1. 新增下列 **ClaimsExchange** 元素，請確定用於 ID 的值與用於 **TargetClaimsExchangeId** 的值相同：

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    更新的值**TechnicalProfileReferenceId**以符合`Id`中的值**TechnicalProfile**您稍早新增的宣告提供者的項目。 例如： `MSA-OIDC` 。

1. 儲存 TrustFrameworkExtensions.xml  檔案，並再次上傳它以供驗證。

## <a name="create-an-azure-ad-b2c-application"></a>建立 Azure AD B2C 應用程式

使用 Azure AD B2C 的通訊是透過您在 Azure AD B2C 租用戶中建立的應用程式。 此節會列出您可以視需要完成以建立測試應用程式的步驟 (如果您尚未這麼做)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 請確定您使用包含 Azure AD B2C 租用戶的目錄。 選取 **目錄和訂用帳戶篩選**上方功能表中，然後選擇包含您的租用戶的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
1. 選取 [應用程式]  ，然後選取 [新增]  。
1. 輸入應用程式的名稱，例如 testapp1  。
1. 針對 [Web 應用程式 / Web API]  ，選取 `Yes`，然後y在 [回覆 URL]  欄位輸入 `https://jwt.ms`。
1. 按一下 [建立]  。

## <a name="update-and-test-the-relying-party-file"></a>更新並測試信賴憑證者檔案

更新信賴憑證者 (RP) 檔案，此檔案將起始您剛才建立的使用者旅程圖。

1. 在您的工作目錄中建立一份 SignUpOrSignIn.xml  複本，並將它重新命名。 例如，將它重新命名為 SignUpSignInMSA.xml  。
1. 開啟新檔案，並將 **TrustFrameworkPolicy** 的 **PolicyId** 屬性更新成唯一值。 例如： `SignUpSignInMSA` 。
1. 將 **PublicPolicyUri** 的值更新成原則的 URI。 例如：`http://contoso.com/B2C_1A_signup_signin_msa`
1. 更新的值**ReferenceId**屬性中**DefaultUserJourney**以符合您建立舊版 (SignUpSignInMSA) 使用者旅程圖的識別碼。
1. 儲存您的變更、上傳檔案，然後選取清單中的新原則。
1. 請確定您在上一節中建立的 Azure AD B2C 應用程式 (或藉由完成必要的元件，例如*webapp1*或是*testapp1*) 中選取**選取應用程式**欄位，然後再按一下 測試**立即執行**。
1. 選取 **的 Microsoft 帳戶**按鈕，然後登入。

    如果在登入作業成功時，將您重新導向至`jwt.ms`其中顯示已解碼的語彙基元，類似於：

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```
