---
title: 受保護的 web API-應用程式程式碼設定 |Azure
description: 了解如何建置受保護的 web API，並設定您的應用程式程式碼。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa262c1c6a091575a70c5670b1c7a7c96e8e2128
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536892"
---
# <a name="protected-web-api-code-configuration"></a>受保護的 web API:程式碼設定

若要設定您的受保護 web API 的程式碼，您需要了解如何定義為受保護的 Api、 如何設定持有人權杖，以及如何驗證權杖。

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>如何定義 ASP.NET/ASP.NET 核心 Api，為受保護？

Web 應用程式，例如 ASP.NET/ASP.NET Core web Api 「 受保護 」 因為它們的控制器動作前面會加上`[Authorize]`屬性。 因此已獲授權的身分識別呼叫 API 時，才可以呼叫控制器動作。

請考慮下列問題：

- Web API 是如何知道呼叫它的應用程式的身分識別？ （只有應用程式可以呼叫 web API）。
- 如果應用程式呼叫 web API 代表使用者，使用者的身分識別為何？

## <a name="bearer-token"></a>持有人權杖

身分識別的應用程式，以及使用者的相關資訊 （除非 web 應用程式會接受來自精靈應用程式的服務對服務呼叫），會保留在應用程式呼叫時，會將標頭中設定的持有人權杖。

以下是C#顯示的用戶端呼叫 API，它會取得權杖，以使用 Microsoft Authentication Library for.NET (MSAL.NET) 之後的程式碼範例：

```CSharp
var scopes = new[] {$"api://.../access_as_user}";
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Microsoft 身分識別平台端點用戶端應用程式要求的持有人權杖*web API 的*。 Web API 中是唯一的應用程式應該驗證權杖，並檢視它所包含的宣告。 用戶端應用程式應該永遠不會嘗試檢查權杖中的宣告。 （web API 可能要求，在未來，加密的權杖。 這項需求會防止存取可以檢視的存取權杖的用戶端應用程式。）

## <a name="jwtbearer-configuration"></a>JwtBearer 組態

本節說明如何設定持有人權杖。

### <a name="config-file"></a>組態檔

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="code-initialization"></a>程式碼初始化

當應用程式呼叫控制器動作，以保存`[Authorize]`ASP.NET/ASP.NET 核心的屬性，查看在呼叫要求的 Authorization 標頭的持有人權杖，並擷取存取權杖。 語彙基元，然後轉送到 JwtBearer 中介軟體，它會呼叫適用於.NET 的 Microsoft IdentityModel 擴充功能。

在 ASP.NET Core，此中介軟體會初始化在 Startup.cs 檔案：

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

此指令中, 介軟體會新增至 web API:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 目前的 ASP.NET Core 範本會在您的組織或不使用個人帳戶的任何組織內的使用者，建立 Azure Active Directory (Azure AD) 的 web 登入的 Api。 不過，您可以輕鬆地變更他們使用的 Microsoft 身分識別平台端點 Startup.cs 檔案中新增此程式碼：

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>權杖驗證

JwtBearer 中的介軟體，例如 web apps 中，OpenID Connect 中介軟體會導向依`TokenValidationParameters`來驗證權杖。 權杖被解密 （視需要），擷取的宣告，並驗證簽章。 然後中, 介軟體會藉由檢查這項資料驗證權杖：

- 它被針對 web API （對象）。
- 它已發行的應用程式，可以呼叫 web API (sub)。
- 它是由受信任的安全性權杖服務 (STS) （簽發者） 所核發的。
- 其存留期是在範圍內 （到期）。
- 它未曾遭到竄改 （簽章）。

也會有特殊的驗證。 比方說，就可以驗證 （當內嵌在權杖中） 的簽署金鑰是受信任和權杖不在重新執行。 最後，有些通訊協定需要特定的驗證。

### <a name="validators"></a>驗證程式

驗證步驟所擷取的驗證程式，全部都在[適用於.NET 的 Microsoft IdentityModel Extensions](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)開放原始碼程式庫，在一個原始程式檔中：[Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

此表說明驗證程式：

| 驗證程式 | 描述 |
|---------|---------|
| `ValidateAudience` | 可確保權杖是驗證權杖 （適用於 me)，應用程式。 |
| `ValidateIssuer` | 可確保受信任的 STS 所簽發的權杖 （從我所信任的人）。 |
| `ValidateIssuerSigningKey` | 可確保驗證權杖的信任的應用程式用來簽署權杖的金鑰。 （特殊案例索引鍵內嵌權杖中的位置。 通常不需要。） |
| `ValidateLifetime` | 可確保權杖是仍然 （或已經） 有效。 驗證程式會檢查權杖的存留期 (`notbefore`和`expires`宣告) 在範圍內。 |
| `ValidateSignature` | 可確保權杖未遭到竄改。 |
| `ValidateTokenReplay` | 可確保權杖不重新執行。 （針對某些 onetime 使用通訊協定的特殊案例。） |

驗證程式都關聯的屬性`TokenValidationParameters`類別本身從 ASP.NET/ASP.NET 核心組態初始化。 在大部分情況下，您就不必變更的參數。 沒有一個例外狀況，不是單一租用戶的應用程式。 (也就是 web 應用程式接受使用者從任何組織或個人 Microsoft 帳戶。)在此情況下，您必須驗證簽發者。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [請確認範圍和程式碼中的應用程式角色](scenario-protected-web-api-verification-scope-app-roles.md)
