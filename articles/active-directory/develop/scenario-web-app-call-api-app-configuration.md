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
ms.openlocfilehash: 1131cba204b7b7af33cc0441ee455b6e333aba20
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310087"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>呼叫 web Api 的 web 應用程式-程式碼設定

如[web 應用程式登入使用者案例](scenario-web-app-sign-user-overview.md)中所示，web 應用程式會使用 oauth 2.0[授權碼流程](v2-oauth2-auth-code-flow.md)來登入使用者。 此流程分為兩個部分：

1. 要求授權碼。 此元件會將使用者的私用對話委派給 Microsoft 身分識別平臺。 使用者登入並同意使用 web Api。 當此私用對話成功結束時，應用程式會在其重新導向 URI 上接收授權碼。
1. 藉由兌換授權碼來要求 API 的存取權杖。

[Web 應用程式登入使用者案例](scenario-web-app-sign-user-overview.md)只會執行第一個階段。 在這裡瞭解如何修改登入使用者的 Web API，讓它現在呼叫 web API。

## <a name="libraries-supporting-web-app-scenarios"></a>支援 Web 應用程式案例的程式庫

支援 web 應用程式之授權碼流程的程式庫如下：

| MSAL 程式庫 | 描述 |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 支援的平臺為 .NET Framework 和 .NET Core 平臺 (而不是 UWP、Xamarin 和 Xamarin, 因為這些平臺是用來建立公用用戶端應用程式) |
| ![MSAL.Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | 進行中的開發-現已公開預覽 |
| ![MSAL.Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | 進行中的開發-現已公開預覽 |

選取對應至您感興趣之平臺的索引標籤：

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

假設使用者登入已委派給 Open ID connect （OIDC）中介軟體，您想要在 OIDC 流程中連結。 執行此動作的方式會根據您使用的架構而有所不同。
在 ASP.NET Core 的情況下，您會訂閱中介軟體 OIDC 事件。 原則是:

- 您將透過 Open ID connect 中介軟體，讓 ASP.NET core 要求授權碼。 藉由執行此 ASP.NET/ASP.NET 核心, 可讓使用者登入和同意,
- 您將會訂閱 Web 應用程式接收授權碼。 此訂用C#帳戶是透過委派完成。
- 收到驗證碼時，您會使用 MSAL 程式庫來兌換程式碼，而產生的存取權杖和重新整理權杖會儲存在權杖快取中。 從該處，快取可以用於應用程式的其他部分（例如，在控制器中），以無訊息方式取得其他標記。

本文和下列的程式碼片段會從[ASP.NET Core Web 應用程式累加式教學課程（第2章）](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)中解壓縮。 您可能想要參考此教學課程，以取得完整的執行詳細資料。

> [!NOTE]
> 若要完整瞭解以下的程式碼片段，您必須熟悉 ASP.NET Core 的[基本](https://docs.microsoft.com/aspnet/core/fundamentals)概念，以及特定的相依性[插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)和[選項](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

假設使用者登入已委派給 Open ID connect （OIDC）中介軟體，您想要在 OIDC 流程中連結。 執行此動作的方式會根據您使用的架構而有所不同。
在 ASP.NET 的案例中，您會訂閱中介軟體 OIDC 事件。 原則是:

- 您將透過 Open ID connect 中介軟體，讓 ASP.NET core 要求授權碼。 藉由執行此 ASP.NET/ASP.NET 核心, 可讓使用者登入和同意,
- 您將會訂閱 Web 應用程式接收授權碼。 這是C#委派。
- 收到驗證碼時，您將使用 MSAL 程式庫來兌換程式碼。 產生的存取權杖和重新整理權杖會儲存在權杖快取中。 從該處，快取可以用於應用程式的其他部分（例如，在控制器中），以無訊息方式取得其他標記。

本文中的程式碼片段和下列專案會從[ASP.NET Web 應用程式範例](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)中解壓縮。 如需完整的執行詳細資料，您可以參考此範例。

# <a name="javatabjava"></a>[Java](#tab/java)

本文和下列的程式碼片段會從呼叫 Microsoft graph msal4j web 應用程式範例的[JAVA web 應用程式](https://github.com/Azure-Samples/ms-identity-java-webapp)中解壓縮。
此範例目前可讓 msal4j 產生授權碼 URL，並處理流覽至 Microsoft 身分識別平臺授權端點。 也可以使用短期衝刺安全性來登入使用者。 如需完整的執行詳細資料，您可以參考此範例。

# <a name="pythontabpython"></a>[Python](#tab/python)

本文中的程式碼片段和下列專案會從[呼叫 Microsoft graph MSAL 的 Python web 應用程式](https://github.com/Azure-Samples/ms-identity-python-webapp)中解壓縮。Python web 應用程式範例。
此範例目前可讓您 MSAL。Python 會產生授權碼 URL，並處理流覽至 Microsoft 身分識別平臺授權端點。 如需完整的執行詳細資料，您可以參考此範例。

---

## <a name="code-that-redeems-the-authorization-code"></a>贖回授權碼的程式碼

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

在 ASP.NET Core 中，原則是在檔案中`Startup.cs` 。 您會想要訂閱`OnAuthorizationCodeReceived` open ID connect 事件，而在此事件中，請呼叫 MSAL。NET 的方法`AcquireTokenFromAuthorizationCode`，其具有將儲存在權杖快取中的效果、所要求`scopes`的存取權杖，以及將在接近到期時用來重新整理存取權杖的重新整理權杖，或代表相同的使用者取得權杖，但適用于不同的資源。

實際上， [ASP.NET Core web 應用程式教學](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)課程會嘗試為您的 web 應用程式提供可重複使用的程式碼。

以下是[L40 的 L42 程式](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42)代碼，其中的呼叫`AddMicrosoftIdentityPlatformAuthentication`會將驗證新增至 web 應用程式的方法，並`AddMsal`新增呼叫 web api 的功能。 對的呼叫`AddInMemoryTokenCaches`是關於在可能的情況下選擇權杖快取實作為：

```CSharp
public class Startup
{
  // Code not show here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not show here
}
```

`Constants.ScopeUserRead`定義于[常數中。 cs # L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5)

```CSharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

您已經研究過`AddMicrosoftIdentityPlatformAuthentication` [Web 應用程式中登入使用者-程式碼](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code)設定的內容

### <a name="the-addmsal-method"></a>AddMsal 方法

的`AddMsal`程式碼位於[Microsoft. Identity. Web/WebAppServiceCollectionExtensions .cs # L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159)。

```CSharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization.
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the Web App or Web API
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign-in with their Microsoft personal accounts
          // (it's required by MSAL.NET and automatically provided when users sign-in with work or school accounts)
          options.Scope.Add("offline_access");
          if (initialScopes != null)
          {
              foreach (string scope in initialScopes)
              {
                  if (!options.Scope.Contains(scope))
                  {
                      options.Scope.Add(scope);
                  }
              }
          }

          // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
          // where it will be usable from Controllers later (through the TokenAcquisition service)
          var handler = options.Events.OnAuthorizationCodeReceived;
          options.Events.OnAuthorizationCodeReceived = async context =>
          {
              var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
              await tokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync(context, options.Scope).ConfigureAwait(false);
              await handler(context).ConfigureAwait(false);
          };
      });
      return services;
  }
}
```

`AddMsal`方法可確保：

- ASP.NET Core Web 應用程式會要求使用者的 IDToken 和驗證碼（`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`）
- 已`offline_access`加入範圍。 使用者必須同意，讓應用程式取得重新整理權杖。
- 應用程式會訂閱 OIDC `OnAuthorizationCodeReceived`事件，並使用 MSAL.NET 贖回呼叫，這會封裝成可重複使用的`ITokenAcquisition`元件。

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>TokenAcquisition. AddAccountToCacheFromAuthorizationCodeAsync 方法

此`TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync`方法位於 TokenAcquisition 中，也就是[L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145)。 它可確保：

- ASP.NET 不會嘗試以平行方式將驗證碼兌換為 MSAL.NET （`context.HandleCodeRedemption();`）
- IDToken 中的宣告可供 MSAL 計算使用者帳戶的權杖快取金鑰
- 如有需要，MSAL.NET 應用程式會具現化
- MSAL.NET 應用程式會兌換此代碼
- 新的識別碼權杖會與 ASP.NET Core 共用（在呼叫`context.HandleCodeRedemption(null, result.IdToken);`期間）。 存取權杖不會與 ASP.NET Core 共用。 它會保留在與使用者相關聯的 MSAL.NET token 快取中，並可供在 ASP.NET Core 控制器中使用。

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };

  // Code omitted here for clarity


  public async Task AddAccountToCacheFromAuthorizationCodeAsync(AuthorizationCodeReceivedContext context, IEnumerable<string> scopes)
  {
   // Code omitted here for clarity

    try
    {
      // As AcquireTokenByAuthorizationCodeAsync is asynchronous we want to tell ASP.NET core that we are handing the code
      // even if it's not done yet, so that it does not concurrently call the Token endpoint. (otherwise there will be a
      // race condition ending-up in an error from Azure AD telling "code already redeemed")
      context.HandleCodeRedemption();

      // The cache will need the claims from the ID token.
      // If they are not yet in the HttpContext.User's claims, so adding them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it and will not send the OAuth 2.0 request in
      // case a further call to AcquireTokenByAuthorizationCodeAsync in the future is required for incremental consent (getting a code requesting more scopes)
      // Share the ID Token though
      var result = await application
          .AcquireTokenByAuthorizationCode(scopes.Except(_scopesRequestedByMsalNet), context.ProtocolMessage.Code)
          .ExecuteAsync()
          .ConfigureAwait(false);

      context.HandleCodeRedemption(null, result.IdToken);
  }
  catch (MsalException ex)
  {
      Debug.WriteLine(ex.Message);
      throw;
  }
 }
```

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>TokenAcquisition. BuildConfidentialClientApplication 方法

在 ASP.NET Core 中, 建立機密用戶端應用程式會使用 HttpCoNtext 中的資訊。 透過`CurrentHttpContext`屬性（HttpCoNtext）存取與要求相關聯的、知道 Web 應用程式的 URL，以及登入的使用者（ `ClaimsPrincipal`在中）。 `BuildConfidentialClientApplication`也會使用 ASP.NET Core 設定，其具有 "AzureAD" 區段，且兩者系結至：

- [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet) `_applicationOptions`類型的資料結構
- ASP.NET Core `azureAdOptions` 中`Authentication.AzureAD.UI`所定義之類型[AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs)的實例。 最後, 應用程式必須維護權杖快取。 您將在下一節中深入瞭解這項功能。

`GetOrBuildConfidentialClientApplication()`方法的程式碼位於 TokenAcquisition 中。 [.cs # L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333)。 它會使用由相依性插入所插入的成員（以 TokenAcquisition 中的函式在[L47](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)中傳遞）。

```CSharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL Confidential client application if needed
  /// </summary>
  private IConfidentialClientApplication GetOrBuildConfidentialClientApplication()
  {
    if (application == null)
    {
        application = BuildConfidentialClientApplication();
    }
    return application;
  }

  /// <summary>
  /// Creates an MSAL Confidential client application
  /// </summary>
  /// <param name="claimsPrincipal"></param>
  /// <returns></returns>
  private IConfidentialClientApplication BuildConfidentialClientApplication()
  {
    var request = CurrentHttpContext.Request;
    var azureAdOptions = _azureAdOptions;
    var applicationOptions = _applicationOptions;
    string currentUri = UriHelper.BuildAbsolute(
        request.Scheme,
        request.Host,
        request.PathBase,
        azureAdOptions.CallbackPath ?? string.Empty);

    string authority = $"{applicationOptions.Instance}{applicationOptions.TenantId}/";

    var app = ConfidentialClientApplicationBuilder
        .CreateWithApplicationOptions(applicationOptions)
        .WithRedirectUri(currentUri)
        .WithAuthority(authority)
        .Build();

    // Initialize token cache providers
    _appTokenCacheProvider?.InitializeAsync(app.AppTokenCache);
    _userTokenCacheProvider?.InitializeAsync(app.UserTokenCache);

    return app;
  }

```

### <a name="summary"></a>總結

若要加總， `AcquireTokenByAuthorizationCode`請確實贖回 ASP.NET 所要求的授權碼，並取得新增至 MSAL.NET 使用者權杖快取的權杖。 然後在 ASP.NET Core 控制器中使用它們。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 處理專案的方式與 ASP.NET Core 相似，不同之處在于 OpenIdConnect 的設定和`OnAuthorizationCodeReceived`事件的訂用帳戶會在[App_Start\Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs)檔案中發生。 您可以在 ASP.NET Core 中找到類似的概念，但在 ASP.NET 中，您必須在 web.config [# L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15)中指定 RedirectUri。 相較于 ASP.NET Core 中的設定，此設定比在中執行的設定來得低，因為您必須在部署應用程式時加以變更。

```CSharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the querystring (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // a basic set of permissions for user sign in & profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application you would use IssuerValidator for additional checks, like making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }
  
  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign in, get the access token & cache it using MSAL
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }
  
  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

請參閱[登入使用者程式碼設定的 Web 應用程式](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code)中的，以瞭解 JAVA 範例如何取得授權碼。 一旦應用程式收到， [AuthFilter # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56)會委派至`AuthHelper.processAuthenticationCodeRedirect` [AuthHelper](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97)中的方法，然後呼叫`getAuthResultByAuthCode`：

```Java
class AuthHelper {
  // code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// code omitted
  }
}
```

方法定義于[AuthHelper # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176)中。 `getAuthResultByAuthCode` 它會建立 MSAL `ConfidentialClientApplication` ，並`acquireToken()`使用`AuthorizationCodeParameters`從授權碼建立的呼叫。

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

在[登入使用者程式碼設定的 Web 應用程式](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code)中要求授權碼流程之後，就會在 Flask 從/getAToken URL 路由的函`authorized`式上收到授權碼。 請參閱[.py # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44)

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

機密用戶端應用程式也可以使用用戶端憑證或用戶端判斷提示來證明其身分識別，而不是用戶端密碼。
使用用戶端判斷提示是一個先進的案例,[用戶端判斷](msal-net-client-assertions.md)提示中有詳細說明

## <a name="token-cache"></a>權杖快取

> [!IMPORTANT]
> 在 web 應用程式（或 web Api）中，權杖快取的執行方式與桌面應用程式權杖快取（通常是以檔案為[基礎](scenario-desktop-acquire-token.md#file-based-token-cache)）不同。
> 基於安全性和效能的考慮，這一點很重要，為了確保 web 應用程式和 web Api 的每個使用者都應該有一個權杖快取（每個帳戶）。 您需要將每個帳戶的權杖快取序列化。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET core 教學課程會使用相依性插入，讓您在應用程式的 Startup.cs 檔中決定權杖快取的執行。 Microsoft 提供了一些預先建立的權杖快取序列化程式，並在權杖快取[序列化](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization)中說明。 有一個有趣的可能性是選擇 ASP.NET Core[分散式記憶體](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)快取：

```csharp
// or use a distributed Token Cache by adding
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// and then choose your implementation

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache()

// Or a Redis cache
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

如需權杖快取提供者的詳細資訊，另請參閱[ASP.NET Core Web 應用程式教學課程 |](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)教學課程的權杖快取階段

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 web 應用程式 (或 web Api) 中, 權杖快取的執行方式與桌面應用程式權杖快取實現不同 (通常是以檔案為[基礎](scenario-desktop-acquire-token.md#file-based-token-cache))。 它可以使用 ASP.NET 會話或伺服器記憶體。 如需相關範例，請參閱在 MsalAppBuilder 中建立 MSAL.NET 應用程式之後，如何連結快取實作為[L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();
  
      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);
  
      return clientapp;
  }
```

# <a name="javatabjava"></a>[Java](#tab/java)

msal4j 提供序列化和還原序列化權杖快取的方法。 JAVA 範例會處理來自會話的序列化，如 AuthHelper 中的`getAuthResultBySilentFlow`方法中所述（如[JAVA # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.ReadBasic.All"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  //update session with latest token cache
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

`SessionManagementHelper`類別的詳細資料提供于[](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)

# <a name="pythontabpython"></a>[Python](#tab/python)

在 Python 範例中，您可以針對每個要求重新建立機密用戶端應用程式，並在 Flask 會話快取中將其序列化，藉以確保在 [快取依據帳戶] 上：

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>後續步驟

此時, 當使用者登入時, 權杖會儲存在權杖快取中。 讓我們看看它如何用於 Web 應用程式的其他部分。

> [!div class="nextstepaction"]
> [登入 Web 應用程式](scenario-web-app-call-api-sign-in.md)
