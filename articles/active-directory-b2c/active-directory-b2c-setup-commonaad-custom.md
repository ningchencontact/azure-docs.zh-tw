---
title: 在 Azure Active Directory B2C 中使用自訂原則來設定多租用戶 Azure AD 識別提供者的登入 | Microsoft Docs
description: 使用自訂原則新增多租用戶 Azure AD 識別提供者 - Azure Active Directory B2C。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d341f7328eb4a977d266c25f6746d4173393b54e
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887207"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來設定多租用戶 Azure Active Directory 的登入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

此文章說明如何在 Azure AD B2C 中透過使用[自訂原則](active-directory-b2c-overview-custom.md)，讓使用 Azure Active Directory (Azure AD) 多租用戶端點的使用者能夠登入。 這可讓使用者從多個 Azure AD 租用戶登入 Azure AD B2C，而不需設定每個租用戶的技術提供者。 不過，所有這些租用戶中的來賓成員並**不能**登入。 因此，您必須[個別設定每個租用戶](active-directory-b2c-setup-aad-custom.md)。

>[!NOTE]
>在下列指示中，會使用 `Contoso.com` 作為組織的 Azure AD 租用戶，以及使用 `fabrikamb2c.onmicrosoft.com` 作為 Azure AD B2C 租用戶。

## <a name="prerequisites"></a>先決條件

完成[在 Azure Active Directory B2C 中開始使用自訂原則](active-directory-b2c-get-started-custom.md)中的步驟。

## <a name="register-an-application"></a>註冊應用程式

若要讓特定 Azure AD 組織的使用者登入，您需要在組織 Azure AD 租用戶內註冊應用程式。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含組織 Azure AD B2C 租用戶 (contoso.com) 的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [應用程式註冊]。
4. 選取 [新增應用程式註冊]。
5. 輸入應用程式的名稱。 例如： `Azure AD B2C App`。
6. 針對 [應用程式類型]，選取 `Web app / API`。
7. 針對 [登入 URL]，以全小寫字母輸入下列 URL，其中以您 Azure AD B2C 租用戶的名稱 (fabrikamb2c.onmicrosoft.com) 取代 `your-tenant`：

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```
    
8. 按一下頁面底部的 [新增] 。 複製 [應用程式識別碼] 以供稍後使用。
9. 選取應用程式，然後選取 [設定]。
10. 選取 [金鑰]輸入金鑰描述、選取持續時間，然後按一下 [儲存]。 複製所顯示的金鑰值以供稍後使用。
11. 在 [設定] 底下，選取 [屬性]、將 [多重租用戶] 設定為 `Yes`，然後按一下 [儲存]

## <a name="create-a-policy-key"></a>建立原則金鑰

您必須將所建立的應用程式金鑰儲存在 Azure AD B2C 租用戶中。

1. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
2. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
3. 在 [概觀] 頁面上，選取 [識別體驗架構 - 預覽]。
4. 選取 [原則金鑰]，然後選取 [新增]。
5. 針對 [選項] 選擇 `Manual`。
6. 輸入原則金鑰的 [名稱]。 例如： `ContosoAppSecret`。  金鑰名稱前面會自動新增前置詞 `B2C_1A_`。
7. 在 [祕密] 中，輸入您先前記錄的應用程式金鑰。
8. 針對 [金鑰使用方法]，選取 `Signature`。
9. 按一下頁面底部的 [新增] 。

## <a name="add-a-claims-provider"></a>新增宣告提供者

如果想要讓使用者使用 Azure AD 進行登入，您必須將 Azure AD 定義成 Azure AD B2C 能夠透過端點與之通訊的宣告提供者。 此端點會提供一組宣告，由 Azure AD B2C 用來確認特定使用者已驗證。 

您可以藉由將 Azure AD 新增至原則擴充檔中的 **ClaimsProvider** 元素，將 Azure AD 定義成宣告提供者。

1. 開啟 *TrustFrameworkExtensions.xml*。
2. 尋找 **ClaimsProviders** 元素。 如果不存在，請在根元素下新增它。
3. 新增新的 **ClaimsProvider**，如下所示：

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
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

4. 在 **ClaimsProvider** 元素底下，將 **Domain** 的值更新成可用來與其他識別提供者做區別的唯一值。
5. 在 **TechnicalProfile** 元素底下，更新 **DisplayName** 的值。 這個值會顯示在您登入畫面的登入按鈕上。
6. 將 **client_id** 設定為來自 Azure AD 多租用戶應用程式註冊的應用程式識別碼。

### <a name="restrict-access"></a>限制存取

> [!NOTE]
> 使用 `https://sts.windows.net` 作為 **ValidTokenIssuerPrefixes** 的值時，可允許所有 Azure AD 使用者登入您的應用程式。

您必須更新有效權杖簽發者清單，並將存取權僅限於能夠登入的特定 Azure AD 租用戶使用者清單。 若要取得這些值，您必須查看您想要讓使用者從中登入之每個特定 Azure AD 租用戶的中繼資料。 資料的格式看起來如下：`https://login.windows.net/your-tenant/.well-known/openid-configuration`，其中 `your-tenant` 是 Azure AD 租用戶名稱 (contoso.com 或任何其他 Azure AD 租用戶)。

1. 開啟您的瀏覽器並移至 **METADATA** URL，然後尋找 **issuer** 物件並複製其值。 如下所示：`https://sts.windows.net/tenant-id/`。
2. 複製並貼上 **ValidTokenIssuerPrefixes** 索引鍵的值。 您可以使用逗號分隔來新增多個值。 上面的 XML 範例中有將這一點的範例標記為註解。

### <a name="upload-the-extension-file-for-verification"></a>上傳擴充檔案準備驗證

現在，您應該已設定原則，所以 Azure AD B2C 知道如何與 Azure AD 目錄進行通訊。 嘗試上傳原則的擴充檔案，這只是為了確認它到目前為止沒有任何問題。

1. 在 Azure AD B2C 租用戶的 [自訂原則] 頁面上，選取 [上傳原則]。
2. 啟用 [覆寫現有的原則]，然後瀏覽並選取 [TrustFrameworkExtensions.xml] 檔案。
3. 按一下 [上傳] 。

## <a name="register-the-claims-provider"></a>註冊宣告提供者

目前，識別提供者已設定，但還未出現在任何註冊/登入畫面中。 若要讓它可供使用，您必須建立現有範本使用者旅程圖的複本，然後修改它，讓它也包含 Azure AD 識別提供者。

1. 從 Starter Pack 開啟 *TrustFrameworkBase.xml* 檔案。
2. 尋找並複製包含 `Id="SignUpOrSignIn"` 之 **UserJourney** 元素的整個內容。
3. 開啟 *TrustFrameworkExtensions.xml*，並尋找 **UserJourneys** 元素。 如果此元素不存在，請新增。
4. 貼上您複製的整個 **UserJourney** 元素內容作為 **UserJourneys** 元素的子系。
5. 重新命名使用者旅程圖的識別碼。 例如： `SignUpSignInContoso`。

### <a name="display-the-button"></a>顯示按鈕

**ClaimsProviderSelection** 元素類似於註冊/登入畫面上的識別提供者按鈕。 如果您為 Azure AD 新增 **ClaimsProviderSelection** 元素，當使用者登陸頁面時，就會出現新按鈕。

1. 在您建立的使用者旅程圖中，尋找包含 `Order="1"` 的 **OrchestrationStep** 元素。
2. 在 **ClaimsProviderSelects** 底下，新增下列元素。 將 **TargetClaimsExchangeId** 的值設定成適當的值，例如 `AzureADExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>將按鈕連結至動作

現在已備妥按鈕，您需要將它連結至動作。 在此案例中，動作是用來讓 Azure AD B2C 與 Azure AD 進行通訊以接收權杖。 藉由連結 Azure AD 宣告提供者的技術設定檔，將按鈕連結至動作。

1. 在使用者旅程圖中，尋找包含 `Order="2"` 的 **OrchestrationStep**。
2. 新增下列 **ClaimsExchange** 元素，請確定用於 **Id** 的值與用於 **TargetClaimsExchangeId** 的值相同：

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```
    
    將 **TechnicalProfileReferenceId** 的值更新成您稍早所建立技術設定檔的 **Id**。 例如： `Common-AAD`。

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

1. 在您的工作目錄中建立一份 *SignUpOrSignIn.xml* 複本，並將它重新命名。 例如，將它重新命名為 *SignUpSignContoso.xml*。
2. 開啟新檔案，並將 **TrustFrameworkPolicy** 的 **PolicyId** 屬性更新成唯一值。 例如： `SignUpSignInContoso`。
3. 將 **PublicPolicyUri** 的值更新成原則的 URI。 例如 `http://contoso.com/B2C_1A_signup_signin_contoso`
4. 更新 **DefaultUserJourney** 中 **ReferenceId** 屬性的值，以符合您所建立新使用者旅程圖 (SignUpSignContoso) 的識別碼。
5. 儲存您的變更、上傳檔案，然後選取清單中的新原則。
6. 確定 [選取應用程式] 欄位中已選取您所建立的 Azure AD B2C 應用程式，然後按一下 [立即執行]來進行測試。
