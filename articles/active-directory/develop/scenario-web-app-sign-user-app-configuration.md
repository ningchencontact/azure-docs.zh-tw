---
title: 登入使用者的 Web 應用程式 (程式碼設定)-Microsoft 身分識別平臺
description: 瞭解如何建立可登入使用者的 web 應用程式 (程式碼設定)
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a746b0f6d85e3f012cdd2e78fff8cd10a586950
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086744"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>登入使用者的 Web 應用程式-程式碼設定

瞭解如何為您的 Web 應用程式設定登入使用者的程式碼。

## <a name="libraries-used-to-protect-web-apps"></a>用來保護 Web Apps 的程式庫

<!-- This section can be in an include for Web App and Web APIs -->
用來保護 Web 應用程式 (和 Web API) 的程式庫包括:

| 平台 | 程式庫 | 描述 |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [適用于 .NET 的識別模型延伸模組](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | 適用于 .NET 的 Microsoft 身分識別延伸模組是由 ASP.NET 和 ASP.NET Core 直接使用, 它會建議一組在 .NET Framework 和 .NET Core 上執行的 Dll。 從 ASP.NET/ASP.NET 核心 Web 應用程式, 您可以使用**TokenValidationParameters**類別來控制權杖驗證 (尤其是在某些 ISV 案例中) |
| ![Java](media/sample-v2-code/logo_java.png) | [msal4j](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | 適用于 JAVA 的 MSAL-目前為公開預覽狀態 |
| ![Python](media/sample-v2-code/logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | 適用于 Python 的 MSAL-目前為公開預覽狀態 |

本文中的程式碼片段和下列專案會從中解壓縮：

- [ASP.NET Core Web 應用程式累加式教學課程，第1章](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)。
- [ASP.NET Web 應用程式範例](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)
- 呼叫 Microsoft graph msal4j web 應用程式的[JAVA web 應用程式](https://github.com/Azure-Samples/ms-identity-java-webapp)範例
- [呼叫 Microsoft graph MSAL 的 Python web 應用程式](https://github.com/Azure-Samples/ms-identity-python-webapp)。Python web 應用程式範例

您可能想要參考這些教學課程和範例，以取得完整的執行詳細資料。

## <a name="configuration-files"></a>設定檔

使用 Microsoft 身分識別平臺登入使用者的 Web 應用程式，通常是透過設定檔來設定。 您需要填寫的設定如下:

- 如果您`Instance`想要讓應用程式執行（例如在國家雲端中）
- 中的物件`tenantId`
- 從`clientId` Azure 入口網站複製的應用程式的。

有時候，應用程式可以由`authority`參數化，這是`instance`和的串連。`tenantId`

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core 中，這些設定位於`appsettings.json`檔案的 "AzureAD" 區段中。

```Json
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

在 ASP.NET Core 中，還有另一個檔案`properties\launchSettings.json`（），其中包含您`applicationUrl`的應用程式和各種配置`sslPort`檔的 URL （）和 SSL 埠（）。

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

在 Azure 入口網站中, 您必須在應用程式的**驗證**頁面中註冊的回復 uri, 必須符合這些 url;也就是說, 在上述兩個配置`https://localhost:44321/signin-oidc`檔中, 它們會是`http://localhost:3110` applicationUrl, 但`sslPort`會指定 (44321) `appsettings.json`, 而`CallbackPath`則會`/signin-oidc`如中所定義。
  
以同樣的方式，登出 URI 會設定為`https://localhost:44321/signout-callback-oidc`。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中, 應用程式是透過`Web.Config`檔案進行設定

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

在 Azure 入口網站中，您必須在應用程式的**驗證**頁面中註冊的回復 uri，必須符合這些 url;也就`https://localhost:44326/`是。

# <a name="javatabjava"></a>[Java](#tab/java)

在 JAVA 中， `application.properties`設定位於`src/main/resources`

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraphUsers=http://localhost:8080/msal4jsample/graph/users
```

在 Azure 入口網站中，您必須在應用程式的**驗證**頁面中註冊的回復 uri，必須符合應用程式所定義的 redirectUris，也就是`http://localhost:8080/msal4jsample/secure/aad`和`http://localhost:8080/msal4jsample/graph/users`

# <a name="pythontabpython"></a>[Python](#tab/python)

以下是[app_config. .py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/web_app_sample/app_config.py)中的 Python 設定檔

```Python
AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
CLIENT_ID = "Enter_the_Application_Id_here"
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
SCOPE = ["https://graph.microsoft.com/User.Read"]
REDIRECT_URI = "http://localhost:5000/getAToken"
```

---

## <a name="initialization-code"></a>初始化程式碼

初始化程式碼會依平臺而有所不同。 對於 ASP.NET Core 和 ASP.NET，登入使用者會被委派給 OpenIDConnect 中介軟體。 現今，ASP.NET/ASP.NET Core 範本會為 Azure AD v1.0 端點產生 web 應用程式。 因此，需要進行一些設定，才能將它們調整成 Microsoft 身分識別平臺（v2.0）端點。 就 JAVA 而言，它會以應用程式的合作，由春季處理。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core Web Apps （和 Web api）中，應用程式會受到保護，因為`[Authorize]`您在控制器或控制器動作上有一個屬性。 這個屬性會檢查使用者是否已驗證。 執行應用程式初始化的程式碼位於檔案中`Startup.cs` ，若要使用 Microsoft 身分識別平臺（先前為 Azure AD v2.0）新增驗證，您必須新增下列程式碼。 程式碼中的批註應該一目了然。

  > [!NOTE]
  > 如果您使用 Visual studio 內的預設 ASP.NET core Web 專案來啟動專案, `dotnet new mvc`或使用`AddAzureAD`方法, 則預設會提供, 因為相關的套件會自動載入。
  > 不過, 如果您從頭開始建立專案, 並嘗試使用下面的程式碼, 建議您將 NuGet 套件 **"AspNetCore** " 新增至您的`AddAzureAD`專案, 讓方法可供使用。
  
下列程式碼可從啟動中取得[。 cs # L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34)

```CSharp
public class Startup
{
 ...

  // This method gets called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign-in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);
  
      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

是在 WebAppServiceCollectionExtensions 中定義的擴充方法[（Identity](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23)）。 `AddMicrosoftIdentityPlatformAuthentication` 這樣

- 新增驗證服務
- 設定讀取設定檔的選項
- 設定 OpenID connect 選項，讓使用的授權單位是 Microsoft 身分識別平臺（先前為 Azure AD v2.0）端點
- 已驗證權杖的簽發者
- 對應至名稱的宣告會從識別碼權杖中的 "preferred_username" 宣告進行對應 

除了設定之外，您還可以在呼叫`AddMicrosoftIdentityPlatformAuthentication`時指定：

- 設定區段的名稱（預設為 AzureAD）
- 如果您想要追蹤 OpenIdConnect 中介軟體事件，這可協助您在驗證無法執行時疑難排解 Web 應用程式： `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents`將`true`設定為會向您顯示資訊集的詳細資料，ASP.NET Core中介軟體，因為它從 HTTP 回應到中`HttpContext.User`的使用者身分識別。

```CSharp
/// <summary>
/// Add authentication with Microsoft identity platform.
/// This method expects the configuration file will have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenIdConnect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the code below, this application signs in users in any Work and School
      // accounts and any Microsoft Personal Accounts.
      // If you want to direct Azure AD to restrict the users that can sign-in, change
      // the tenant value of the appsettings.json file in the following way:
      // - only Work and School accounts => 'organizations'
      // - only Microsoft Personal accounts => 'consumers'
      // - Work and School and Personal accounts => 'common'
      // If you want to restrict the users that can sign-in to only one tenant
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users that can sign-in to several organizations
      // Set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set the nameClaimType to be preferred_username.
      // This change is needed because certain token claims from Azure AD V1 endpoint
      // (on which the original .NET core template is based) are different than Microsoft identity platform endpoint.
      // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

`AadIssuerValidator`類別可讓權杖的簽發者在許多情況下進行驗證（v1.0 或 v2.0 權杖、單一租使用者，或在 Azure 公用雲端或國家/地區以其個人 Microsoft 帳戶登入使用者的多租使用者應用程式或應用程式）雲端）。 可從[Microsoft. Identity. Web/Resource/AadIssuerValidator 取得。](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

與 ASP.NET Web 應用程式/Web api 中的驗證相關的程式碼位於`App_Start/Startup.Auth.cs`檔案中。

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="javatabjava"></a>[Java](#tab/java)

JAVA 範例會使用春季架構。 應用程式會受到保護`Filter`，因為您會執行，以取得每個 HTTP 回應。 在 JAVA Web 應用程式快速入門中， `AuthFilter`這`src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`是在中。 篩選器會處理 OAuth 2.0 授權碼流程，因此：

- 驗證使用者是否已驗證（`isAuthenticated()`方法）
- 如果使用者未經過驗證，則會計算 Azure AD 授權端點的 url，並將瀏覽器重新導向至此 URI
- 回應抵達時，如果包含驗證程式代碼流程，它會讓 msal4j 取得權杖。
- 當它最後從權杖端點（在重新導向 URI 上）收到權杖時，使用者就已登入。

如需詳細資訊`doFilter()` ，請參閱 AuthFilter 中的方法[。](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)

> [!NOTE]
> 的`doFilter()`程式碼會以稍有不同的順序撰寫，但是流程就是其中所述。

如需此方法所觸發之授權碼流程的詳細資訊，請參閱[Microsoft 身分識別平臺和 OAuth 2.0 授權碼流程](v2-oauth2-auth-code-flow.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 範例會使用 Flask。 初始化 flask 和 MSAL。Python 是在[應用程式中完成。 .py # L1-L17](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/app.py#L1-L17)

```Python
import uuid
import flask
import requests
from flask import Flask, render_template, session, request
from flask_session import Session
import msal
import app_config

sess = Session()
app = Flask(__name__)
app.config.from_object('config.Config')
sess.init_app(app)
cache = msal.SerializableTokenCache()
application = msal.ConfidentialClientApplication(
    app_config.CLIENT_ID, authority=app_config.AUTHORITY,
    client_credential=app_config.CLIENT_SECRET,
    token_cache=cache)
```

這是 MSAL。會負責讓使用者登入的 Python。 請參閱[.py # L74-84](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/app.py#L74-84)

```Python
@app.route('/authenticate')
def authenticate():
    # Call to the authorize endpoint
    auth_state = str(uuid.uuid4())
    session[(request.cookies.get("session")+'state')] = auth_state
    authorization_url = application.get_authorization_request_url(app_config.SCOPE, state=auth_state,
                                                                  redirect_uri=app_config.REDIRECT_URI)
    resp = flask.Response(status=307)
    resp.headers['location'] = authorization_url
    return resp
```

---

## <a name="next-steps"></a>後續步驟

在下一篇文章中，您將瞭解如何觸發登入和登出。

> [!div class="nextstepaction"]
> [登入和登出](scenario-web-app-sign-user-sign-in.md)
