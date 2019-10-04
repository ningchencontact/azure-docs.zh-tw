---
title: 呼叫下游 web Api 的 Web API (應用程式的程式碼設定)-Microsoft 身分識別平臺
description: 瞭解如何建立呼叫 web Api 的 Web API (應用程式的程式碼設定)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 529665a03d2203dcb501b59d7647f4390bdaeb78
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936749"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>呼叫 web Api 的 web API-程式碼設定

註冊 Web API 之後, 您可以設定應用程式的程式碼。

用來設定 Web API 的程式碼, 讓它呼叫下游 web Api 建置於用來保護 Web API 的程式碼之上。 如需詳細資訊, 請參閱[受保護的 Web API 應用程式](scenario-protected-web-api-app-configuration.md)設定。

## <a name="code-subscribed-to-ontokenvalidated"></a>已訂閱 OnTokenValidated 的程式碼

在任何受保護 web Api 的程式碼設定之上, 您必須訂閱在呼叫 API 時所收到的持有人權杖驗證:

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform (a.k.k AAD v2.0)
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">Service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it 
        // to MSAL.NET's cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache, and also handles the incremental consent 
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>代理者流程

AddAccountToCacheFromJwt () 方法必須:

- 具現化 MSAL 的機密用戶端應用程式。
- 呼叫`AcquireTokenOnBehalf`以交換用戶端為 Web API 所取得的持有人權杖, 針對相同使用者的持有人權杖, 但針對我們的 api 呼叫下游 API。

### <a name="instantiate-a-confidential-client-application"></a>具現化機密用戶端應用程式

此流程僅適用于機密用戶端流程, 因此受保護的 Web API 會透過`WithClientSecret`或`WithCertificate`方法提供用戶端認證 (用戶端密碼或憑證) 給[ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) 。各自.

![image](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```CSharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

最後, 機密用戶端應用程式也可以使用用戶端判斷提示來證明其身分識別, 而不是用戶端密碼或憑證。
[用戶端判斷](msal-net-client-assertions.md)提示中會詳細說明這個 advanced 案例

### <a name="how-to-call-on-behalf-of"></a>如何呼叫代理者

代理者 (OBO) 呼叫是藉由在`IConfidentialClientApplication`介面上呼叫[AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder)方法來完成。

`UserAssertion`是從本身用戶端的 Web API 收到的持有人權杖所建立。 有[兩個](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)函式, 一個接受 JWT 持有人權杖, 另一個則採用任何類型的使用者判斷提示 (其他類型的安全性權杖, 然後在名為`assertionType`的其他參數中指定)。

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

實際上, OBO 流程通常用來取得下游 API 的權杖, 並將它儲存在 MSAL.NET 的使用者權杖快取中, 讓 Web API 的其他部分稍後可以在的[覆寫](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) ``AcquireTokenOnSilent``上呼叫, 以呼叫下游 api。 此呼叫具有重新整理權杖的效果 (如有需要)。

```CSharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled-in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```

## <a name="protocol"></a>Protocol

如需代理者通訊協定的詳細資訊，請參閱[Microsoft 身分識別平臺和 OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)代理者流程。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [取得應用程式的權杖](scenario-web-api-call-api-acquire-token.md)
