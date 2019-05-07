---
title: Web API，其呼叫下游 web Api （應用程式的程式碼設定）-Microsoft 身分識別平台
description: 了解如何建置 web API 呼叫 web Api （應用程式的程式碼組態）
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 204baac37254592c0dc808af413fd8b3c6c79864
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074781"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>Web API 呼叫 web Api-程式碼組態

註冊您的 web API 之後，您可以設定應用程式的程式碼。

若要設定您的 web API，讓它呼叫下游 web Api 的程式碼會建置用於專案的 web API 的程式碼上。 如需詳細資訊，請參閱 <<c0> [ 保護的 web API-應用程式設定](scenario-protected-web-api-app-configuration.md)。

## <a name="code-subscribed-to-ontokenvalidated"></a>訂閱 OnTokenValidated 的程式碼

上方任何受保護的 web Api 的程式碼組態，您需要訂閱您的 API 呼叫時收到持有人權杖的驗證：

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform v2.0 (AAD v2.0)
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

AddAccountToCacheFromJwt() 方法需要：

- 具現化 MSAL 機密用戶端應用程式。
- 呼叫`AcquireTokenOnBehalf`交換已取得用戶端的 web API，針對相同的使用者，但我們的 API 來呼叫下游 API 的持有人權杖的持有人權杖。

### <a name="instantiate-a-confidential-client-application"></a>具現化的機密用戶端應用程式

此流程僅供以機密用戶端流程，因此受保護的 web API 會提供用戶端認證 （用戶端密碼或憑證） 來[ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.appconfig.confidentialclientapplicationbuilder?view=azure-dotnet-preview)透過`WithClientSecret`或`WithCertificate`方法，分別。

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

### <a name="how-to-call-on-behalf-of"></a>如何代表的呼叫

代表的 (OBO) 呼叫是藉由呼叫[AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.apiconfig.acquiretokenonbehalfofparameterbuilder?view=azure-dotnet-preview)方法`IConfidentialClientApplication`介面。

`ClientAssertion`建置從 web API 與自己的用戶端所收到的持有人權杖。 有[兩個建構函式](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)，另一個會採用 JWT 持有人權杖，以及另一個會採用任何一種使用者判斷提示 (另一種安全性權杖時，哪種類型則會指定名為做為其他參數`assertionType`)。

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

在實務上，OBO 流程通常用來取得下游 api 的權杖，並將它儲存在 MSAL.NET 使用者權杖快取，以便稍後可以呼叫 web API 的其他部分[會覆寫](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet)的``AcquireTokenOnSilent``來呼叫下游 Api。 如有需要這就會有重新整理權杖，的效果。

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

如需有關代表的通訊協定的詳細資訊，請參閱[Microsoft 身分識別平台和 OAuth 2.0 代理者流程](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [取得權杖的應用程式](scenario-web-api-call-api-acquire-token.md)
