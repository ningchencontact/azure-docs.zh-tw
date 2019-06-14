---
title: Web 應用程式呼叫 web Api （程式碼設定）-Microsoft 身分識別平台
description: 了解如何建置 Web 應用程式呼叫 web Api （應用程式的程式碼組態）
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
ms.openlocfilehash: bd7f393f889facf147cf25625d5c3b20f886ddf5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65784946"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Web 應用程式呼叫 web Api-程式碼組態

中所示[Web 應用程式登入的使用者案例](scenario-web-app-sign-user-overview.md)，假設使用者在登入委派給 Open ID connect (OIDC) 中介軟體，您想要攔截總 OIDC 程序中。 若要這麼做的方式是根據的架構不同您使用 （這裡 ASP.NET 和 ASP.NET Core），但在結束時，您將訂閱 OIDC 中介軟體的事件。 原則是：

- 您會讓 ASP.NET 或 ASP.NET core 要求授權碼。 透過這個 ASP.NET/ASP.NET core 將會讓使用者登入並同意，
- 您將訂閱的授權碼接收的 Web 應用程式。
- 當收到的驗證程式碼時，您將使用 MSAL 程式庫來兌換的程式碼，所產生的存取權杖及重新整理權杖快取中的權杖存放區。 從該處，快取可用的應用程式其他部分以無訊息方式取得其他權杖。

## <a name="libraries-supporting-web-app-scenarios"></a>支援 Web 應用程式案例的程式庫

支援的 Web 應用程式的授權碼流程的程式庫是：

| MSAL 程式庫 | 描述 |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 是.NET Framework 和.NET Core 的平台，支援的平台 （不 UWP、 Xamarin.iOS 和為這些平台 Xamarin.Android 來建置公用用戶端應用程式） |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | 進度-公開預覽中的程式開發 |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | 進度-公開預覽中的程式開發 |

## <a name="aspnet-core-configuration"></a>ASP.NET Core 組態

在 ASP.NET Core 中發生`Startup.cs`檔案。 您會想要訂閱`OnAuthorizationCodeReceived`開啟識別碼連線事件，並從這個事件中，呼叫 MSAL。NET 的方法`AcquireTokenFromAuthorizationCode`具有將儲存在權杖快取、 存取權杖要求的範圍，以及將用來重新整理存取權杖，它即將到期日時，或取得代表相同的使用者權杖的重新整理權杖的效果但不同的資源。

下列程式碼中的註解會協助您了解一些棘手層面編排 MSAL.NET 和 ASP.NET Core

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add(OidcConstants.ScopeOfflineAccess);
   options.Scope.Add("user.read"); // for instance

   // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
   // where it will be usable from Controllers later (through the TokenAcquisition service)
   var handler = options.Events.OnAuthorizationCodeReceived;
   options.Events.OnAuthorizationCodeReceived = async context =>
   {
    // As AcquireTokenByAuthorizationCode is asynchronous we want to tell ASP.NET core
    // that we are handing the code even if it's not done yet, so that it does 
    // not concurrently call the Token endpoint.
    context.HandleCodeRedemption();

    // Call MSAL.NET AcquireTokenByAuthorizationCode
    var application = BuildConfidentialClientApplication(context.HttpContext,
                                                         context.Principal);
    var result = await application.AcquireTokenByAuthorizationCode(scopes.Except(scopesRequestedByMsalNet), context.ProtocolMessage.Code)
                                  .ExecuteAsync();

    // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it
    // and will not send the OAuth 2.0 request in case a further call to
    // AcquireTokenByAuthorizationCodeAsync in the future for incremental consent 
    // (getting a code requesting more scopes)
    // Share the ID Token so that the identity of the user is known in the application (in 
    // HttpContext.User)
    context.HandleCodeRedemption(null, result.IdToken);

    // Call the previous handler if any
    await handler(context);
   };
```

ASP.NET Core 中建置機密用戶端應用程式，將會使用 HttpContext 中的資訊。 此 HttpContext Web 應用程式，和登入的使用者知道 URL (在`ClaimsPrincipal`)。 它也會使用 ASP.NET Core 組態，其中包含 「 AzureAD 」 一節中，與它繫結至`_applicationOptions`資料結構。 最後，應用程式需要維護語彙基元的快取。

```CSharp
/// <summary>
/// Creates an MSAL Confidential client application
/// </summary>
/// <param name="httpContext">HttpContext associated with the OIDC response</param>
/// <param name="claimsPrincipal">Identity for the signed-in user</param>
/// <returns></returns>
private IConfidentialClientApplication BuildConfidentialClientApplication(HttpContext httpContext, ClaimsPrincipal claimsPrincipal)
{
 var request = httpContext.Request;

 // Find the URI of the application)
 string currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, azureAdOptions.CallbackPath ?? string.Empty);

 // Updates the authority from the instance (including national clouds) and the tenant
 string authority = $"{azureAdOptions.Instance}{azureAdOptions.TenantId}/";

 // Instantiates the application based on the application options (including the client secret)
 var app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
               .WithRedirectUri(currentUri)
               .WithAuthority(authority)
               .Build();

 // Initialize token cache providers. In the case of Web applications, there must be one
 // token cache per user (here the key of the token cache is in the claimsPrincipal which
 // contains the identity of the signed-in user)
 if (this.UserTokenCacheProvider != null)
 {
  this.UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (this.AppTokenCacheProvider != null)
 {
  this.AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

`AcquireTokenByAuthorizationCode` 真正兌換授權碼要求的 ASP.NET，並取得會新增至 MSAL.NET 使用者權杖快取的權杖。 在這裡，然後是用於 ASP.NET Core 控制器。

## <a name="aspnet-configuration"></a>ASP.NET 組態

ASP.NET 處理動作的方式很類似，不同之處在於 OpenIdConnect 和訂用帳戶的組態`OnAuthorizationCodeReceived`事件發生在`App_Start\Startup.Auth.cs`檔案。 您會發現概念類似，不同之處在於您要指定在組態檔中，這是有點較少的 RedirectUri 的以下強大：

```CSharp
private void ConfigureAuth(IAppBuilder app)
{
 app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

 app.UseCookieAuthentication(new CookieAuthenticationOptions { });

 app.UseOpenIdConnectAuthentication(
 new OpenIdConnectAuthenticationOptions
 {
  Authority = Globals.Authority,
  ClientId = Globals.ClientId,
  RedirectUri = Globals.RedirectUri,
  PostLogoutRedirectUri = Globals.RedirectUri,
  Scope = Globals.BasicSignInScopes, // a basic set of permissions for user sign in & profile access
  TokenValidationParameters = new TokenValidationParameters
  {
  // We'll inject our own issuer validation logic below.
  ValidateIssuer = false,
  NameClaimType = "name",
  },
  Notifications = new OpenIdConnectAuthenticationNotifications()
  {
   AuthorizationCodeReceived = OnAuthorizationCodeReceived,
  }
 });
}
```

```CSharp
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
{
 // Upon successful sign in, get & cache a token using MSAL
 string userId = context.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
 IConfidentialClientApplication clientapp;
 clientApp = ConfidentialClientApplicationBuilder.Create(Globals.ClientId)
                  .WithClientSecret(Globals.ClientSecret)
                  .WithRedirectUri(Globals.RedirectUri)
                  .WithAuthority(new Uri(Globals.Authority))
                  .Build();

  EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);

 AuthenticationResult result = await clientapp.AcquireTokenByAuthorizationCode(scopes, context.Code)
                                              .ExecuteAsync();
}
```

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>MSAL.NET 權杖快取 （核心） 的 ASP.NET Web 應用程式

在 web 應用程式 （或 web Api 的事實） 的權杖快取實作是不同於傳統型應用程式權杖快取實作 (通常[檔案型](scenario-desktop-acquire-token.md#file-based-token-cache)。 它可以使用 ASP.NET/ASP.NET 核心工作階段中，或 Redis 快取，或資料庫或甚至是 Azure Blob 儲存體。 在程式碼中這個上面的程式碼片段是物件`EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);`方法呼叫，後者則繫結的快取服務。 這裡已超出本指南的範圍案例，但下面提供的連結，就會發生什麼的詳細資料。

> [!IMPORTANT]
> 非常重要的是，web 應用程式和 web Api，應該會有每位使用者 （每個帳戶） 的一個權杖快取。 您需要將每個帳戶的權杖快取序列化。

如何使用權杖快取的 Web 應用程式和 web Api 的範例可用於[ASP.NET Core Web 應用程式教學課程](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)在階段[2-2 權杖快取](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)。 如需實作，請查看 [microsoft-authentication-extensions-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) 程式庫中的下列資料夾 [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) (在 [Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) 資料夾中)。

## <a name="next-steps"></a>後續步驟

此時，當使用者登入權杖的動作就會儲存在權杖快取。 我們來看看如何接著會使用它的 Web 應用程式其他部分。

> [!div class="nextstepaction"]
> [登入 Web 應用程式](scenario-web-app-call-api-sign-in.md)
