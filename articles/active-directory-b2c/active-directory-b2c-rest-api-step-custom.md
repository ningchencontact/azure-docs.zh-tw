---
title: REST API 宣告交換-Azure Active Directory B2C
description: 加入在 Active Directory B2C 自訂原則中的 REST API 宣告交換。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0bdef508e12a3b11143149b330da73838b53f860
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439004"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>若要在 Azure Active Directory B2C 自訂原則新增 REST API 宣告交換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

您可以新增至支援的 RESTful API 的互動您[自訂原則](active-directory-b2c-overview-custom.md)Azure Active Directory (Azure AD) B2C 中。 這篇文章會示範如何建立與 RESTful 服務互動的 Azure AD B2C 使用者旅程圖。

互動包括宣告交換的 REST API 宣告與 Azure AD B2C 之間的資訊。 宣告交換具有下列特性：

- 可以設計成協調流程步驟。
- 可以觸發外部動作。 例如，它可以在外部資料庫中記錄一個事件。
- 可用來擷取值，然後將它存放在使用者資料庫中。
- 可以變更執行流程。

表示在這篇文章中的案例包含下列動作：

1. 查閱外部系統中的使用者。
2. 取得註冊該使用者的城市。
3. 以宣告形式將該屬性傳回應用程式。

## <a name="prerequisites"></a>必要條件

- 完成[開始使用自訂原則](active-directory-b2c-get-started-custom.md)中的步驟。
- 要互動的 REST API 端點。 簡單的 Azure 函式，例如此發行項使用。 若要建立 Azure 函式，請參閱[在 Azure 入口網站中建立您的第一個函式](../azure-functions/functions-create-first-azure-function.md)。

## <a name="prepare-the-api"></a>準備 API

在本節中，您準備 Azure 的函式，來獲得價值`email`，然後傳回的值`city`，可供 Azure AD B2C 做為宣告。

變更您要使用下列程式碼建立 Azure 函式的 run.csx 檔案：

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
  string email = req.Query["email"];
  string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
  dynamic data = JsonConvert.DeserializeObject(requestBody);
  email = email ?? data?.email;

  return email != null
    ? (ActionResult)new OkObjectResult(
      new ResponseContent
      {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        city = "Redmond"
      })
      : new BadRequestObjectResult("Please pass an email on the query string or in the request body");
}

public class ResponseContent
{
    public string version { get; set; }
    public int status { get; set; }
    public string city {get; set; }
}
```

## <a name="configure-the-claims-exchange"></a>宣告交換設定

技術設定檔提供宣告交換的設定。

開啟*TrustFrameworkExtensions.xml*檔案，並新增下列**ClaimsProvider**內的 XML 項目**ClaimsProviders**項目。

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

**InputClaims**元素定義傳送至 REST 服務的宣告。 在此範例中，宣告值`givenName`傳送至 REST 服務來作為宣告`email`。 **OutputClaims**元素定義預期來自 REST 服務的宣告。

## <a name="add-the-claim-definition"></a>新增宣告定義

加入定義`city`內**BuildingBlocks**項目。 您可以在 TrustFrameworkExtensions.xml 檔案開頭處找到此元素。

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="city">
      <DisplayName>City</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your city</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

## <a name="add-an-orchestration-step"></a>新增協調流程步驟

有許多使用案例都可將 REST API 呼叫用來作為協調流程步驟。 作為協調流程步驟，它可在使用者成功完成工作 (例如首次註冊) 後作為外部系統的更新，或作為設定檔更新以讓資訊保持同步。 在此情況下，它會用來加強設定檔編輯之後提供給應用程式的資訊。

加入設定檔編輯使用者旅程圖中的步驟。 使用者之後驗證 （協調流程步驟 1-4 中下列 XML 程式碼），而且使用者已提供更新的設定檔資訊 （步驟 5）。 複製設定檔編輯使用者旅程圖 XML 程式碼，從*TrustFrameworkBase.xml*的檔案您*TrustFrameworkExtensions.xml*檔案**UserJourneys**項目。 然後進行步驟 6 所作的修改。

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

使用者旅程圖的最終 XML 看起來應該如下列範例：

```XML
<UserJourney Id="ProfileEdit">
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
      <ClaimsProviderSelections>
        <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
        <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
      </ClaimsProviderSelections>
    </OrchestrationStep>
    <OrchestrationStep Order="2" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
        <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>localAccountAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="4" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>socialIdpAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="5" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <!-- Add a step 6 to the user journey before the JWT token is created-->
    <OrchestrationStep Order="6" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>新增宣告

編輯*ProfileEdit.xml*檔案，並新增`<OutputClaim ClaimTypeReferenceId="city" />`要**OutputClaims**項目。

加入新的宣告之後，技術設定檔看起來像此範例中：

```XML
<TechnicalProfile Id="PolicyProfile">
  <DisplayName>PolicyProfile</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
    <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    <OutputClaim ClaimTypeReferenceId="city" />
  </OutputClaims>
  <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>上傳您的變更並測試

1. (選擇性：)儲存現有的版本 （透過下載） 的檔案再繼續進行。
2. 上傳*TrustFrameworkExtensions.xml*並*ProfileEdit.xml* ，然後選取要覆寫現有的檔案。
3. 選取  **B2C_1A_ProfileEdit**。
4. 針對**選取 應用程式**自訂原則的 概觀 頁面上選取 web 應用程式名稱*webapp1*先前登錄。 請確定**回覆 URL**是`https://jwt.ms`。
4. 選取 **立即執行**。 使用您的帳戶認證登入，然後按一下**繼續**。

如果所有項目已正確設定時，此權杖還包含新的宣告`city`，以值`Redmond`。

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_profileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>後續步驟

- 您也可以將互動設計成驗證設定檔。 如需詳細資訊，請參閱[逐步解說：將 REST API 宣告交換整合到 Azure AD B2C 使用者旅程圖中以作為對使用者輸入的驗證](active-directory-b2c-rest-api-validation-custom.md)。
- [修改設定檔編輯以從使用者收集其他資訊](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
