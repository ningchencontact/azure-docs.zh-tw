---
title: 具現化的機密用戶端應用程式，使用選項 (Microsoft Authentication Library for.NET) |Azure
description: 了解如何使用 Microsoft Authentication Library for.NET (MSAL.NET) 的設定選項，機密用戶端應用程式具現化。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60b3b2fa2cdb7808e45f9142f1c1a351f470af50
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158831"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>使用的組態選項使用 MSAL.NET，機密用戶端應用程式具現化

本文說明如何具現化[機密用戶端應用程式](msal-client-applications.md)使用 Microsoft Authentication Library for.NET (MSAL.NET)。  應用程式具現化的設定檔中定義的組態選項。

然後再初始化應用程式，您必須先[註冊](quickstart-register-app.md)它，讓您的應用程式可以與 Microsoft 身分識別平台整合。 註冊之後，您可能需要下列資訊 （這可以在 Azure 入口網站中找到）：

- 用戶端識別碼 （代表 GUID 的字串）
- （具名執行個體） 的身分識別提供者 URL 和您的應用程式的登入對象。 這兩個參數統稱為授權單位。
- 如果您正在撰寫企業營運應用程式僅供您的組織 （也命名單一租用戶的應用程式） 租用戶識別碼。
- 應用程式祕密 （用戶端祕密的字串） 或憑證 （屬於類型 X509Certificate2） 如果它是機密用戶端應用程式。
- 針對 web 應用程式，以及有時公用用戶端應用程式 （尤其當您的應用程式需要使用訊息代理程式），您必須也會將 redirectUri 身分識別提供者，請連絡後利用安全性權杖的應用程式。

## <a name="configure-the-application-from-the-config-file"></a>設定應用程式，從組態檔
MSAL.NET 中的選項屬性的名稱相符的屬性名稱`AzureADOptions`在 ASP.NET Core 中，因此您不需要撰寫任何黏附程式碼。

ASP.NET Core 應用程式組態所述*appsettings.json*檔案：

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

您可以從 MSAL.NET v3.x，來設定您的機密用戶端應用程式，從組態檔。 應用程式組態相關的類別位於`Microsoft.Identity.Client.AppConfig`命名空間。

在類別中，您要設定並具現化您的應用程式，您需要宣告`ConfidentialClientApplicationOptions`物件。  應用程式選項的執行個體，繫結來源 （包括 appconfig.json 檔案） 中讀取的組態：

```csharp
using Microsoft.Identity.Client.AppConfig;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

這可讓 「 AzureAD 」 一節的內容*appsettings.json*檔案，以繫結至的對應屬性`ConfidentialClientApplicationOptions`物件。  接下來，建置`ConfidentialClientApplication`物件：

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>加入執行階段組態
機密用戶端應用程式中，您通常會有每位使用者的快取。 因此，您必須取得使用者相關聯的快取，並告知您想要使用它的應用程式產生器。 在相同的方式，您可能必須動態計算的重新導向 URI。 在此情況下的程式碼如下：

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

