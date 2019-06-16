---
title: 受保護的 web API-應用程式程式碼設定 |Azure
description: 了解如何建立受保護的 Web API，並設定您的應用程式程式碼。
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
ms.openlocfilehash: bdd68d9ec5d0dd83df4628f39785ce255482245d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111157"
---
# <a name="protected-web-api---code-configuration"></a>受保護的 web API-程式碼組態

若要成功設定您的受保護 web API 的程式碼，您需要了解如何成為受保護的 Api，您需要設定持有人權杖，以及如何驗證權杖。

## <a name="what-makes-aspnetaspnet-core-apis-protected"></a>讓受保護的 ASP.NET/ASP.NET Core Api？

例如在 web 應用程式，ASP.NET/ASP.NET core 的 web Api 「 受保護 」 因為它們的控制器動作前面會加上`[Authorize]`屬性。 因此，如果使用已獲授權的身分識別來呼叫 API，可以只呼叫控制器動作。

請考慮下列問題：

- Web API 是如何知道呼叫它 （應用程式可以呼叫 web API） 的應用程式的身分識別？
- 如果應用程式呼叫 web API 代表使用者，使用者的身分識別為何？

## <a name="bearer-token"></a>持有人權杖

身分識別的應用程式，以及使用者的相關資訊 （除非 web 應用程式會接受來自精靈應用程式的服務對服務呼叫），會保留在呼叫應用程式時，會將標頭中設定的持有人權杖。

以下是C#程式碼範例，示範之後使用 MSAL.NET 權杖呼叫 API 的用戶端。

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
> Microsoft 身分識別平台端點用戶端應用程式要求的持有人權杖**web API 的**。 Web API 中是唯一的應用程式應該驗證權杖並查看它所包含的宣告。 用戶端應用程式應該永遠不會檢查權杖中的宣告 (web API 可能會決定，在未來，它會要求加密的權杖，這會中斷用戶端應用程式便可以打開開啟存取權杖)。

## <a name="jwtbearer-configuration"></a>JwtBearer 組態

本章節會涵蓋您需要設定的持有人權杖。

### <a name="config-file"></a>組態檔

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line of business app)
      Otherwise you can leave them set to common
      This can be:
      - A guid (Tenant ID = Directory ID)
      - 'common' (Any organization and personal accounts)
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

控制器動作儲存在呼叫應用程式時`[Authorize]`屬性 ASP.NET/ASP.NET core 查看中呼叫的要求的 Authorization 標頭的持有人權杖，並擷取存取權杖，然後轉送到 JwtBearer中介軟體，它會呼叫適用於.NET 的 Microsoft 身分識別模型延伸模組。

在 ASP.NET Core 中，此中介軟體會在初始化`Startup.cs`檔案。

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

下列指示中介軟體新增至 web API:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 目前，ASP.NET Core 範本建立 Azure AD v1.0 web Api。 不過，您可以輕鬆地變更他們加入下列程式碼中的使用 Microsoft 身分識別平台 endpoint`Startup.cs`檔案。

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API
    options.Authority += "/v2.0";

    // The web API accepts as audiences are both the Client ID (options.Audience) and api://{ClientID}
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line of business apps),
    // we inject our own multi-tenant validation logic (which even accepts both V1 and V2 tokens)
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>權杖驗證

JwtBearer 中的介軟體，例如 web apps 中，OpenID Connect 中介軟體會導向依`TokenValidationParameters`來驗證權杖。 權杖被解密 （如有需要），擷取的宣告，並驗證簽章。 然後，會驗證權杖，藉由檢查下列資料：

- 它被針對 web API （對象）
- 它已發行的應用程式，可以呼叫 web API (sub)
- 它已發行的耐久的安全性權杖伺服器 (STS) （簽發者）
- 權杖的存留期是在範圍中 （到期）
- 它未曾遭到竄改 （簽章）

也會有特殊的驗證。 比方說，就可以驗證 （當內嵌在權杖中） 的簽署金鑰是受信任和權杖不在重新執行。 最後，有些通訊協定需要特定的驗證。

### <a name="validators"></a>驗證程式

驗證步驟會擷取到驗證程式，全部都在[適用於.NET 的 Microsoft 身分識別模型延伸](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)開放原始碼程式庫，在一個原始程式檔中：[Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)

下表中詳述的驗證程式：

| 驗證程式 | 描述 |
|---------|---------|
| `ValidateAudience` | 可確保權杖的應用程式驗證權杖 （適用於我）。 |
| `ValidateIssuer` | 可確保，受信任的 STS 所簽發的權杖 （從我所信任的人）。 |
| `ValidateIssuerSigningKey` | 可確保驗證權杖的信任的應用程式用來簽署權杖 （金鑰內嵌在權杖中，通常不需要的位置的特殊情況下） 的索引鍵。 |
| `ValidateLifetime` | 可確保權杖是仍然 （或已經） 有效。 完成藉由檢查權杖的存留期 (`notbefore`，`expires`宣告) 在範圍內。 |
| `ValidateSignature` | 可確保權杖尚未遭到竄改。 |
| `ValidateTokenReplay` | 可確保不重新執行權杖 （針對某些 onetime 使用通訊協定的特殊情況）。 |

驗證程式都關聯的屬性`TokenValidationParameters`類別本身從 ASP.NET/ASP.NET 核心組態初始化。 在大部分情況下，您就不必變更的參數。 有一個例外狀況的單一租用戶應用程式 （也就是接受使用者從任何組織或個人 Microsoft 帳戶的 web 應用程式）--在此情況下，必須驗證簽發者。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [請確認範圍和程式碼中的應用程式角色](scenario-protected-web-api-verification-scope-app-roles.md)
