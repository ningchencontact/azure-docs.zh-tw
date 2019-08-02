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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a96d17ae7fbe94877032e7b4b2aacb63f6e070ca
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562261"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>登入使用者的 Web 應用程式-程式碼設定

瞭解如何為您的 Web 應用程式設定登入使用者的程式碼。

## <a name="libraries-used-to-protect-web-apps"></a>用來保護 Web Apps 的程式庫

<!-- This section can be in an include for Web App and Web APIs -->
用來保護 Web 應用程式 (和 Web API) 的程式庫包括:

| 平台 | 程式庫 | 描述 |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [適用于 .NET 的識別模型延伸模組](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | 適用于 .NET 的 Microsoft 身分識別延伸模組是由 ASP.NET 和 ASP.NET Core 直接使用, 它會建議一組在 .NET Framework 和 .NET Core 上執行的 Dll。 從 ASP.NET/ASP.NET 核心 Web 應用程式, 您可以使用**TokenValidationParameters**類別來控制權杖驗證 (尤其是在某些 ISV 案例中) |

## <a name="aspnet-core-configuration"></a>ASP.NET Core 設定

本文中的程式碼片段和下列專案會從[ASP.NET Core Web 應用程式累加式教學課程 (第1章)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)中解壓縮。 您可能想要參考該教學課程, 以取得完整的執行詳細資料。

### <a name="application-configuration-files"></a>應用程式佈建檔

在 ASP.NET Core 中, 使用 Microsoft 身分識別平臺登入使用者的 Web 應用程式會透過`appsettings.json`檔案進行設定。 您需要填寫的設定如下:

- 如果您`Instance`想要讓應用程式在國家雲端中執行
- 中的物件`tenantId`
- 從`clientId` Azure 入口網站複製的應用程式的。

```JSon
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

在 ASP.NET Core 中, 還有另一個檔案, 其中包含您`applicationUrl`應用程式的 URL ()`sslPort`和 SSL 埠 (), 以及各種不同的設定檔。

```JSon
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
  
以同樣的方式, 登出 URI 會設定為`https://localhost:44321/signout-callback-oidc`。

### <a name="initialization-code"></a>初始化程式碼

在 ASP.NET Core Web Apps (和 Web api) 中, 執行應用程式初始化的程式碼位於檔案`Startup.cs`中, 而若要使用 Microsoft 身分識別平臺 (先前為 Azure AD v2.0) 新增驗證, 您必須新增下列程式碼。 程式碼中的批註應該一目了然。

  > [!NOTE]
  > 如果您使用 Visual studio 內的預設 ASP.NET core Web 專案來啟動專案, `dotnet new mvc`或使用`AddAzureAD`方法, 則預設會提供, 因為相關的套件會自動載入。 不過, 如果您從頭開始建立專案, 並嘗試使用下面的程式碼, 建議您將 NuGet 套件 **"AspNetCore** " 新增至您的`AddAzureAD`專案, 讓方法可供使用。
  
```CSharp
 services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
         .AddAzureAD(options => configuration.Bind("AzureAd", options));

 services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
 {
  // The ASP.NET core templates are currently using Azure AD v1.0, and compute
  // the authority (as {Instance}/{TenantID}). We want to use the Microsoft identity platform endpoint
  options.Authority = options.Authority + "/v2.0/";

  // If you want to restrict the users that can sign-in to specific organizations
  // Set the tenant value in the appsettings.json file to 'organizations', and add the
  // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection.
  // Otherwise validate the issuer
  options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ForAadInstance(options.Authority).ValidateAadIssuer;

  // Set the nameClaimType to be preferred_username.
  // This change is needed because certain token claims from Azure AD v1.0 endpoint
  // (on which the original .NET core template is based) are different in Microsoft identity platform endpoint.
  // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
  // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
  options.TokenValidationParameters.NameClaimType = "preferred_username";
  ...
```

## <a name="aspnet-configuration"></a>ASP.NET 設定

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

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [登入和登出](scenario-web-app-sign-user-sign-in.md)
