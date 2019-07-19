---
title: 呼叫 web Api 的 web 應用程式 (程式碼設定)-Microsoft 身分識別平臺
description: 瞭解如何建立呼叫 web Api 的 Web 應用程式 (應用程式的程式碼設定)
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
ms.openlocfilehash: 15c12aebccf34957db8442034ebbcd6ac7c107e1
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276721"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>呼叫 web Api 的 web 應用程式-程式碼設定

如[Web 應用程式登入使用者案例](scenario-web-app-sign-user-overview.md)所示, 假設登入使用者已委派給 Open ID CONNECT (OIDC) 中介軟體, 您想要在 OIDC 程式中連結。 執行此動作的方式不同于您所使用的架構 (這裡 ASP.NET 和 ASP.NET Core), 但在最後, 您會訂閱中介軟體 OIDC 事件。 原則是:

- 您會讓 ASP.NET 或 ASP.NET core 要求授權碼。 藉由執行此 ASP.NET/ASP.NET 核心, 可讓使用者登入和同意,
- 您將會訂閱 Web 應用程式接收授權碼。
- 收到驗證碼時, 您將使用 MSAL 程式庫來兌換程式碼, 以及在權杖快取中產生的存取權杖和重新整理權杖存放區。 從該處, 快取可用於應用程式的其他部分, 以無訊息方式取得其他標記。

## <a name="libraries-supporting-web-app-scenarios"></a>支援 Web 應用程式案例的程式庫

支援 Web Apps 授權碼流程的程式庫包括:

| MSAL 程式庫 | 描述 |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 支援的平臺為 .NET Framework 和 .NET Core 平臺 (而不是 UWP、Xamarin 和 Xamarin, 因為這些平臺是用來建立公用用戶端應用程式) |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | 進行中的開發-現已公開預覽 |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | 進行中的開發-現已公開預覽 |

## <a name="aspnet-core-configuration"></a>ASP.NET Core 設定

在 ASP.NET Core 中, 專案會發生`Startup.cs`在檔案中。 您會想要訂閱`OnAuthorizationCodeReceived` open ID connect 事件, 而在此事件中, 請呼叫 MSAL。NET 的方法`AcquireTokenFromAuthorizationCode` , 其具有在權杖快取中儲存的效果、要求的範圍的存取權杖, 以及將在接近到期時用來重新整理存取權杖的重新整理權杖, 或代表相同的使用者取得權杖, 但適用于不同的資源。

下列程式碼中的批註將協助您瞭解 MSAL.NET 和 ASP.NET Core 的一些棘手層面。 [ASP.NET Core Web 應用程式累加教學](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)課程中提供完整的詳細資料, 第2章

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

在 ASP.NET Core 中, 建立機密用戶端應用程式會使用 HttpCoNtext 中的資訊。 此 HttpCoNtext 知道 Web 應用程式的 URL, 以及已登入的使用者 (在中`ClaimsPrincipal`)。 它也會使用具有 "AzureAD" 區段且系結至`_applicationOptions`資料結構的 ASP.NET Core 設定。 最後, 應用程式必須維護權杖快取。

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

`AcquireTokenByAuthorizationCode`確實贖回 ASP.NET 所要求的授權碼, 並取得新增至 MSAL.NET 使用者權杖快取的權杖。 然後在 ASP.NET Core 控制器中使用它們。

## <a name="aspnet-configuration"></a>ASP.NET 設定

ASP.NET 處理專案的方式類似, 不同之處在于 OpenIdConnect 的設定和`OnAuthorizationCodeReceived`事件的訂閱會`App_Start\Startup.Auth.cs`在檔案中發生。 您會發現類似的概念, 但在這裡, 您將需要在設定檔案中指定 RedirectUri, 這有點不健全:

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

最後, 機密用戶端應用程式也可以使用用戶端憑證或用戶端判斷提示來證明其身分識別, 而不是用戶端密碼。
使用用戶端判斷提示是一個先進的案例,[用戶端判斷](msal-net-client-assertions.md)提示中有詳細說明

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>ASP.NET (核心) Web 應用程式的 MSAL.NET Token cache

在 web 應用程式 (或 web Api) 中, 權杖快取的執行方式與桌面應用程式權杖快取實現不同 (通常是以檔案為[基礎](scenario-desktop-acquire-token.md#file-based-token-cache))。 它可以使用 ASP.NET/ASP.NET 核心會話、Redis 快取或資料庫, 甚至是 Azure Blob 儲存體。 在上述的程式碼片段中, 這是`EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);`方法呼叫的物件, 它會系結快取服務。 此處所發生狀況的詳細資料已超出本案例指南的範圍, 但以下提供連結。

> [!IMPORTANT]
> 值得注意的是, 對於 web 應用程式和 web Api 而言, 每個使用者都應該有一個權杖快取 (每個帳戶)。 您需要將每個帳戶的權杖快取序列化。

在階段[2-2 權杖](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)快取中, [ASP.NET Core web 應用程式教學](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)課程中提供如何使用 web 應用程式和 web api 之權杖快取的範例。 如需實作，請查看 [microsoft-authentication-extensions-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) 程式庫中的下列資料夾 [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) (在 [Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) 資料夾中)。

## <a name="next-steps"></a>後續步驟

此時, 當使用者登入時, 權杖會儲存在權杖快取中。 讓我們看看它如何用於 Web 應用程式的其他部分。

> [!div class="nextstepaction"]
> [登入 Web 應用程式](scenario-web-app-call-api-sign-in.md)
