---
title: 如何使用 Application Insights 中的事件從 Azure AD B2C 來追蹤使用者行為 | Microsoft Docs
description: 使用自訂原則，從 Azure AD B2C 使用者旅程圖，在 Application Insights 中啟用事件記錄的逐步指南 - 預覽
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 3/15/2018
ms.author: davidmu
ms.openlocfilehash: 1e8c4a53266072db71952ee35b15e5de54fabb95
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="tracking-user-behavior-inside-azure-ad-b2c-journeys-using-application-insights"></a>使用 Application Insights 在 Azure AD B2C 旅程圖內追蹤使用者行為

Azure Active Directory B2C 可搭配 Azure Application Insights 來運作。  它們可以為您自訂建立的使用者旅程圖提供詳細的自訂事件記錄。  本指南示範入門方法，讓您可以： 
* 深入了解使用者行為
* 在開發或實際執行時對您自己的原則進行疑難排解
* 測量效能
* 從 Application Insights 建立通知

## <a name="how-it-works"></a>運作方式
已將新的提供者新增至 Azure AD B2C 的識別體驗架構：`Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`。  它會使用提供給 Azure AD B2C 的檢測金鑰，將事件資料直接傳送到 Application Insights。  技術設定檔會使用此提供者來定義 B2C 的事件。  這個設定檔會指定事件的名稱、將記錄的宣告及檢測金鑰。  為了張貼事件，接著會在自訂的使用者旅程圖中新增技術設定檔以作為 `orchestration step` 或 `validation technical profile`。  事件可以由 Application Insights 使用相互關聯識別碼來整合，以記錄使用者工作階段。  Application Insights 會在數秒內使事件和工作階段成為可用狀態，並提供許多視覺效果、匯出及分析工具。



## <a name="prerequisites"></a>先決條件
完成[開始使用自訂原則](active-directory-b2c-get-started-custom.md)中的步驟。  本指南假設正在使用自訂原則入門套件。  但是，這並非必要條件。



## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>步驟 1. 建立 Application Insights 資源並取得檢測金鑰

Application Insights 是一個功能強大的工具。 搭配 Azure AD B2C 使用此工具時，唯一的需求就是建立資源並取得檢測金鑰。  Application Insights 必須建立於 [Azure 入口網站](https://portal.azure.com)中。

[Application Insights 的完整文件](https://docs.microsoft.com/azure/application-insights/)

1. 在 Azure 入口網站中，於您的訂用帳戶租用戶內按一下 `+ Create a resource`。  此租用戶不是您的 Azure AD B2C 租用戶。  
2. 搜尋並選取 `Application Insights`  
3. 在您偏好的訂用帳戶之下，建立 `Application Type` `ASP.NET web application` 的資源。
4. 建立之後，開啟您的 Application Insights 資源並記下 `Instrumentation Key` 

![Application Insights 概觀與檢測金鑰](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>步驟 2. 將新的 ClaimType 定義新增至您的信任架構擴充檔

### <a name="open-the-extension-file-from-the-starter-pack-and-add-the-following-elements-to-the-buildingblocks-node--the-extensions-filename-is-typically-yourtenantonmicrosoftcom-b2c1atrustframeworkextensionsxml"></a>從入門套件開啟擴充檔，並將下列元素新增至 `<BuildingBlocks>` 節點。  擴充檔檔名通常是 `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

```xml

    <ClaimsSchema>
      <ClaimType Id="EventType">
        <DisplayName>EventType</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="PolicyId">
        <DisplayName>PolicyId</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="Culture">
        <DisplayName>Culture</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="CorrelationId">
        <DisplayName>CorrelationId</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <!--Additional claims used for passing claims to Application Insights Provider -->
      <ClaimType Id="federatedUser">
        <DisplayName>federatedUser</DisplayName>
        <DataType>boolean</DataType>
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="parsedDomain">
        <DisplayName>Parsed Domain</DisplayName>
        <DataType>string</DataType>
        <UserHelpText>The domain portion of the email address.</UserHelpText>
      </ClaimType>
      <ClaimType Id="userInLocalDirectory">
        <DisplayName>userInLocalDirectory</DisplayName>
        <DataType>boolean</DataType>
        <UserHelpText />
      </ClaimType>
    </ClaimsSchema>

```

## <a name="step-3-add-new-technical-profiles-that-use-the-application-insights-provider"></a>步驟 3. 新增使用 Application Insights 提供者的新技術設定檔

技術設定檔可視為是 Azure AD B2C 識別體驗架構中的功能。  此範例中會定義五個技術設定檔，以開啟工作階段並張貼事件。

| 技術設定檔       | Task |
| ----------------------------- |:---------------------------------------------|
| AzureInsights-Common | 包含於所有 Azure-Insights 技術設定檔中的一組通用參數     | 
| JourneyContextForInsights   | 在 App Insights 中開啟工作階段，並傳送相互關聯識別碼 |
| AzureInsights-SignInRequest     | 收到登入要求之後，建立含有一組宣告的 "SignIn" 事件      | 
| AzureInsights-UserSignup | 當使用者在註冊/登入旅程圖中觸發註冊選項之後，建立名為 "UserSignup" 的事件     | 
| AzureInsights-SignInComplete | 將權杖傳送到信賴憑證者應用程式之後，記錄驗證已成功完成     | 

將這些元素新增至 `<ClaimsProviders>` 節點，以便從入門套件將設定檔新增至擴充檔。  擴充檔檔名通常是 `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

> [!IMPORTANT]
> 將 `ApplicationInsights-Common` 技術設定檔中的 `Instrumentation Key` 變更為 Application Insights 資源所提供的 GUID。

```xml
<ClaimsProvider>
      <DisplayName>Application Insights</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="JourneyContextForInsights">
          <DisplayName>Application Insights</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="CorrelationId" />
          </OutputClaims>
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-SignInRequest">
          <InputClaims>
            <!-- 
                            An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider
                            to create an event with the specified value.
                        -->
            <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
          </InputClaims>
          <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-SignInComplete">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
            <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
            <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
          </InputClaims>
          <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-UserSignup">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
          </InputClaims>
          <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-Common">
          <DisplayName>Alternate Email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
            <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
            <!-- 
                            A boolean indicating whether delevoper mode is enabled. This controls how events are buffered. In a development environment
                            with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights.
                        -->
            <Item Key="DeveloperMode">false</Item>
            <!-- 
                            A boolean indicating whether telemetry should be enabled or not.
                        -->
            <Item Key="DisableTelemetry ">false</Item>
          </Metadata>
          <InputClaims>
            <!--
                            Properties of an event are added using the syntax {property:NAME} where NAME is the name of the property being added
                            to the event. DefaultValue can be either a static value or one resolved by one of the supported DefaultClaimResolvers.
                        -->
            <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
            <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
            <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
          </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>步驟 4. 在現有的使用者旅程圖中，新增 Application-Insights 的技術設定檔作為協調流程步驟。

呼叫 `JournyeContextForInsights` 作為協調流程步驟 1

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

呼叫 `Azure-Insights-SignInRequest` 作為協調流程步驟 2，以追蹤收到的登入/註冊要求。

```xml
<!-- Track that we have received a sign in request -->
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

新增呼叫 `Azure-Insights-UserSignup` 的新步驟，**後面**緊接著 `SendClaims` 協調流程步驟。 在註冊/登入旅程圖中按一下註冊按鈕之後，即會觸發。

```xml
        <!-- Handles the user clicking the sign up link in the local account sign in page -->
        <OrchestrationStep Order="9" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>newUser</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
              <Value>newUser</Value>
              <Value>false</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
          </ClaimsExchanges>
```

緊接在 `SendClaims` 協調流程步驟之後，呼叫 `Azure-Insights-SignInComplete`。   此步驟將反映已成功完成的旅程圖。

```xml
        <!-- Track that we have successfully sent a token -->
        <OrchestrationStep Order="11" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
          </ClaimsExchanges>
        </OrchestrationStep>

```

> [!IMPORTANT]
> 新增協調流程步驟之後，循序將步驟重新編號，不要略過從 1 到 N 的任何整數


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>步驟 5。 上傳已修改的擴充檔、執行原則，並檢視 Application Insights 中的事件

儲存並上傳新的信任架構擴充檔。  然後，從您的應用程式或使用 Azure AD B2C 介面中的 `Run Now` 來呼叫信賴憑證者原則。  您的事件將在數秒內於 Application Insights 中變成可用狀態。

1. 在 Azure Active Directory 租用戶中開啟 Application Insights 資源。
2. 按一下 [`USAGE`] 子功能表中的 [`Events`]。
3. 將 `During` 設為 `Last hour`，並將 `By` 設為 `3 minutes`。  您可能需要按一下 [`Refresh`] 來檢視結果

![Application Insights 使用量事件刀鋒視窗](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)





##  <a name="next-steps"></a>後續步驟

將其他宣告類型和事件新增至您的使用者旅程圖，以符合您的需求。  以下是可能使用其他宣告解析程式的宣告清單。

### <a name="culture-specific-claims"></a>文化特性特定的宣告

```xml
Culture-specific Claims
            Referenced using {Culture:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="JourneyCultureDefaultClaimProcessor"/>
        public enum SupportedClaim
        {
             /// The two letter ISO code for the language i.e. en
            LanguageName
             
            /// The two letter ISO code for the region i.e. US
            RegionName,
 
            /// The RFC5646 language code i.e. en-US
            RFC5646,

            /// The LCID of language code i.e. 1033
            LCID
        }

```

### <a name="policy-specific-claims"></a>原則特定的宣告

```xml
Policy-specific Claims
Referenced using {Policy:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="PolicyDefaultClaimProcessor"/> 
        public enum SupportedClaim
        {
            /// The trustframework tenant id
            TrustFrameworkTenantId,
  
            /// The tenant id of the relying party
            RelyingPartyTenantId,
 
            /// The policy id of the policy
            PolicyId,
 
            /// The tenant object id of the policy
            TenantObjectId
}

```

### <a name="openidconnect-specific-claims"></a>OpenIDConnect 特定的宣告

```xml
OpenIDConnect Specific Claims
Referenced using {OIDC:One of the property names below}
 
/// 
        /// An enumeration of the claims supported by the <see cref="OpenIdConnectDefaultClaimProcessor"/>

        public enum SupportedClaim
        {
            /// The OpenIdConnect prompt parameter
            Prompt,
 
            /// The OpenIdConnect login_hint parameter
            LoginHint,

            /// The OpenIdConnect domain_hint parameter
            DomainHint,
 
             /// The OpenIdConnect max_age parameter
            MaxAge,
 
            /// The OpenIdConnect client_id parameter
            ClientId,
 
            /// The OpenIdConnect username parameter
            Username,

            /// The OpenIdConnect password parameter
            Password,
 
            /// The OpenIdConnect resource type parameter
            Resource,
 
            /// The OpendIdConext acr_values parameter
             AuthenticationContextReferences
        }
 
```

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>OIDC & OAuth2 要求所包含的非通訊協定參數

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

OIDC 或 OAuth2 要求中所包含的任何參數名稱均可對應至使用者旅程圖中的宣告。  接著就能在事件中記錄它。 例如，來自應用程式的要求可能包含名稱為 `app_session`、`loyalty_number` 或 `any_string` 的查詢字串參數。

來自應用程式的範例要求：
```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
接著可使用下列方式，將輸入宣告元素新增至 Application Insights 事件，藉以新增宣告：
```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="app_session" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="loyalty_number" DefaultValue="{OAUTH-KV:loyalty_number}" />
```

### <a name="other-system-claims"></a>其他系統宣告

某些系統宣告必須先新增至宣告包，然後才能用來記錄為事件。 您必須先呼叫技術設定檔 `SimpleUJContext` 作為協調流程步驟或驗證技術設定檔，然後才能使用這些宣告。

```xml
<ClaimsProvider>
    <DisplayName>User Journey Context Provider</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="SimpleUJContext">
                <DisplayName>User Journey Context Provide</DisplayName>
                <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="IP-Address" />
                    <OutputClaim ClaimTypeReferenceId="CorrelationId" />
                    <OutputClaim ClaimTypeReferenceId="DateTimeInUtc" />
                    <OutputClaim ClaimTypeReferenceId="Build" />
                 </OutputClaims>
            </TechnicalProfile>
        </TechnicalProfiles>
</ClaimsProvider>



