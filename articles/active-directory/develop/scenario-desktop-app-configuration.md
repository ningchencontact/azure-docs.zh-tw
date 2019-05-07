---
title: 傳統型應用程式呼叫 web Api （程式碼設定）-Microsoft 身分識別平台
description: 了解如何建置傳統型應用程式呼叫 web Api （應用程式的程式碼組態）
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
ms.date: 05/o7/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c52f6fc66187d961dc93089a9f81f6de4d67fe41
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075936"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>傳統型應用程式呼叫 web Api-程式碼組態

既然您已建立您的應用程式，您將了解如何使用應用程式的協調設定程式碼。

## <a name="msal-libraries"></a>MSAL 程式庫

唯一的 MSAL 程式庫現在支援桌面應用程式是 MSAL.NET

## <a name="public-client-application"></a>公開用戶端應用程式

從程式碼的觀點，桌面應用程式公開用戶端應用程式，而且這就是為什麼您會建置並操作 MSAL.NET `IPublicClientApplication`。 一次項目會有點不同您是否使用互動式驗證與否。

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>以獨佔方式透過程式碼

下列程式碼會具現化是公用的用戶端應用程式中，在 Microsoft Azure 公用雲端中，使用其公司和學校帳戶或其個人的 Microsoft 帳戶登入的使用者。

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

如果您想要使用互動式驗證，如上所示，您想要使用`.WithRedirectUri`修飾詞：

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithRedirectUri(PublicClientApplicationBuilder.DefaultInteractiveDesktopRedirectUri)
         .Build();
```

### <a name="using-configuration-files"></a>使用組態檔

下列程式碼會具現化的組態物件，這可能會填滿單元以程式設計方式或從組態檔讀取公用用戶端應用程式

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="more-elaborated-configuration"></a>更複雜的組態

您可以詳細說明建置加上幾個修飾詞的應用程式。 比方說，如果您想在國家雲端 （這裡的美國政府） 中的多租用戶應用程式的應用程式時，您可以撰寫：

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET 也包含 ADFS 2019 修飾詞：

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

最後，如果您想要取得 Azure AD B2C 租用戶的權杖，可以指定您的租用戶中的下列程式碼片段所示：

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>深入了解

若要深入了解如何設定 MSAL.NET 桌面應用程式：

- 如需所有修飾詞上可用的清單`PublicClientApplicationBuilder`，請參閱參考文件[PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.appconfig.publicclientapplicationbuilder?view=azure-dotnet-preview#methods)
- 如需中公開的所有選項的描述`PublicClientApplicationOptions`請參閱 < [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.appconfig.publicclientapplicationoptions?view=azure-dotnet-preview)，參考文件中

## <a name="complete-example-with-configuration-options"></a>使用的組態選項的完整範例

想像有.NET Core 主控台應用程式具有下列`appsettings.json`組態檔：

```JSon
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

您有一些程式碼來讀取這個檔案，使用.NET 提供組態架構;

```CSharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
 /// in Microsoft Azure public clouds or national / sovereign clouds
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a json file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint config
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

現在，若要建立您的應用程式，您將只需要撰寫下列程式碼：

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

和之前呼叫`.Build()`方法中，您可以覆寫您的設定，透過呼叫`.WithXXX`方法先前所示。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [取得傳統型應用程式的權杖](scenario-desktop-acquire-token.md)
