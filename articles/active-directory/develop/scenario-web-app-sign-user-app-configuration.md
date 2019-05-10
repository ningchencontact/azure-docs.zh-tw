---
title: Web 應用程式登入使用者 （程式碼設定）-Microsoft 身分識別平台
description: 了解如何建置 web 應用程式登入使用者 （程式碼組態）
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
ms.openlocfilehash: b2204fe3e08b3c4b909ddc8b7ade4cec219d34fb
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406640"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Web 應用程式，讓登入使用者-程式碼組態

了解如何設定 Web 應用程式的程式碼，讓登入使用者。

## <a name="libraries-used-to-protect-web-apps"></a>用來保護 Web 應用程式程式庫

<!-- This section can be in an include for Web App and Web APIs -->
是用來保護 Web 應用程式 （和 Web API） 的程式庫：

| 平台 | 程式庫 | 說明 |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [適用於.NET 的身分識別模型延伸模組](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | 直接使用 ASP.NET 和 ASP.NET Core，適用於.NET 的 Microsoft 身分識別延伸模組所提出一組執行.NET Framework 和.NET Core 的 Dll。 從 ASP.NET/ASP.NET Core Web 應用程式，您可以控制使用權杖驗證**TokenValidationParameters**類別 （尤其在某些 ISV 案例） |

## <a name="aspnet-core-configuration"></a>ASP.NET Core 組態

### <a name="application-configuration-files"></a>應用程式組態檔

在 ASP.NET Core Web 應用程式登入使用者與 Microsoft 身分識別平台透過設定`appsettings.json`檔案。 您需要填寫的設定如下：

- 雲端`Instance`如果您想要在國家雲端中執行的應用程式
- 在 對象 `tenantId`
- `clientId`應用程式，因為從 Azure 入口網站複製。

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
    // - the tenant Id as a a GUID obtained from the azure portal to sign-in users in your organization
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

在 ASP.NET Core 中，沒有包含 URL 的另一個檔案 (`applicationUrl`) 和 SSL 連接埠 (`sslPort`) 您的應用程式，以及各種設定檔。

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

在 Azure 入口網站中，回覆 Uri，您必須在註冊**驗證**應用程式的頁面必須符合這些 Url; 也就是如上述的兩個組態檔，它們會是`https://localhost:44321/signin-oidc`applicationUrl 為已`http://localhost:3110`但`sslPort`是指定 (44321) 和`CallbackPath`是`/signin-oidc`中所定義`appsettings.json`。
  
同樣地，在 URI 登出會設定為`https://localhost:44321/signout-callback-oidc`。

### <a name="initialization-code"></a>初始化程式碼

在 ASP.NET Core Web 應用程式 （和 Web Api），執行的應用程式初始化的程式碼位於`Startup.cs`檔案，而且，若要新增使用 Microsoft 身分識別平台 (先前稱為 Azure AD) v2.0 的驗證，您必須新增下列程式碼。 在程式碼中的註解應該一目了然。

```CSharp
 services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
         .AddAzureAD(options => configuration.Bind("AzureAd", options));

 services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
 {
  // The ASP.NET core templates are currently using Azure AD v1.0, and compute
  // the authority (as {Instance}/{TenantID}). We want to use the Microsoft Identity Platform v2.0 endpoint
  options.Authority = options.Authority + "/v2.0/";

  // If you want to restrict the users that can sign-in to specific organizations
  // Set the tenant value in the appsettings.json file to 'organizations', and add the
  // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection.
  // Otherwise validate the issuer
  options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ForAadInstance(options.Authority).ValidateAadIssuer;

  // Set the nameClaimType to be preferred_username.
  // This change is needed because certain token claims from Azure AD v1.0 endpoint
  // (on which the original .NET core template is based) are different in Azure AD v2.0 endpoint.
  // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
  // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
  options.TokenValidationParameters.NameClaimType = "preferred_username";
  ...
```

## <a name="aspnet-configuration"></a>ASP.NET 組態

在 ASP.NET 中，應用程式透過設定`Web.Config`檔案

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

與 ASP.NET Web 應用程式中的驗證相關的程式碼 / Web Api 位於`App_Start/Startup.Auth.cs`檔案。

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
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
