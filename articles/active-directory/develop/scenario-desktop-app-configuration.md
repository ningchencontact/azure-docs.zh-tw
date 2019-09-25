---
title: 呼叫 web Api 的傳統型應用程式（程式碼設定）-Microsoft 身分識別平臺
description: 瞭解如何建立桌面應用程式來呼叫 web Api （應用程式的程式碼設定）
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
ms.openlocfilehash: 0926e6800dbcd81d2e542e27afe3afb1240cff22
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268423"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>呼叫 web Api 的桌面應用程式-程式碼設定

既然您已建立應用程式，您將瞭解如何使用應用程式的座標來設定程式碼。

## <a name="msal-libraries"></a>MSAL 程式庫

目前在多個平臺上支援桌面應用程式的 MSAL 程式庫是 MSAL.NET。

適用于 iOS 和 macOS 的 MSAL 僅支援在 macOS 上執行的桌面應用程式。 

## <a name="public-client-application-with-msalnet"></a>具有 MSAL.NET 的公用用戶端應用程式

就程式碼的觀點而言，桌面應用程式是公用用戶端應用程式，因此您將建立和操作 MSAL.NET `IPublicClientApplication`。 無論您使用的是互動式驗證，都同樣會有點不同。

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>由程式碼獨佔

下列程式碼會具現化公用用戶端應用程式、Microsoft Azure 公用雲端中的登入使用者、工作和學校帳戶，或個人 Microsoft 帳戶。

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

如果您想要使用互動式驗證或裝置程式碼流程（如上所示），您會想`.WithRedirectUri`要使用修飾詞：

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>使用設定檔

下列程式碼會從設定物件具現化公用用戶端應用程式，這可以用程式設計方式填入或從設定檔讀取

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>更複雜的設定

您可以藉由加入數個修飾詞來更精細地建立應用程式。 比方說，如果您想要讓應用程式成為國家雲端中的多租使用者應用程式（在美國政府），您可以撰寫：

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET 也包含 ADFS 2019 的修飾詞：

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

最後，如果您想要取得 Azure AD B2C 租使用者的權杖，可以指定您的租使用者，如下列程式碼片段所示：

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>更多資訊

若要深入瞭解如何設定 MSAL.NET 桌面應用程式：

- 如需所有可用`PublicClientApplicationBuilder`的修飾詞清單，請參閱參考檔[PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- 如需中`PublicClientApplicationOptions`公開之所有選項的說明，請參閱參考檔中的[PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)。

## <a name="complete-example-with-configuration-options"></a>含設定選項的完整範例

假設有一個具有下列`appsettings.json`設定檔的 .net Core 主控台應用程式：

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

您有少段程式碼可以使用 .NET 提供的設定架構來讀取此檔案;

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

現在，若要建立您的應用程式，您只需要撰寫下列程式碼：

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

在呼叫`.Build()`方法之前，您可以使用先前所見的方法呼叫來`.WithXXX`覆寫設定。

## <a name="public-client-application-with-msal-for-ios-and-macos"></a>使用 MSAL for iOS 和 macOS 的公用用戶端應用程式

下列程式碼會具現化公用用戶端應用程式、Microsoft Azure 公用雲端中的登入使用者、工作和學校帳戶，或個人 Microsoft 帳戶。

### <a name="quick-configuration"></a>快速設定

Objective-C：

```objc
NSError *msalError = nil;
    
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

快速
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>更複雜的設定

您可以藉由加入數個修飾詞來更精細地建立應用程式。 比方說，如果您想要讓應用程式成為國家雲端中的多租使用者應用程式（在美國政府），您可以撰寫：

Objective-C：

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];
                                                          
MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];
                                                                  
NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

快速

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [取得傳統型應用程式的權杖](scenario-desktop-acquire-token.md)
