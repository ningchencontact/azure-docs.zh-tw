---
title: 呼叫 web Api 的桌面應用程式（呼叫 Web API）-Microsoft 身分識別平臺
description: 瞭解如何建立桌面應用程式來呼叫 web Api （呼叫 Web API）
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
ms.openlocfilehash: 56d3d01e39adfeb6bf2ef5e7e7d595f49c90f5a5
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268284"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>呼叫 web Api 的桌面應用程式-呼叫 Web API

現在您已有權杖，您可以呼叫受保護的 Web API。

## <a name="calling-a-web-api-from-net"></a>從 .NET 呼叫 Web API

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-a-web-api-in-msal-for-ios-and-macos"></a>針對 iOS 和 macOS 呼叫 MSAL 中的 Web API

取得權杖`MSALResult`的方法會傳回物件。 `MSALResult`公開可用於呼叫 Web API 的屬性。`accessToken` 存取權杖應新增至 HTTP 授權標頭，才能進行呼叫以存取受保護的 Web API。

Objective-C：

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

快速

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>呼叫數個 Api-累加同意和條件式存取

如果您需要為相同的使用者呼叫數個 api，一旦取得第一個 api 的權杖，您就可以只呼叫`AcquireTokenSilent`，而您會在大部分的時間內以無訊息方式取得其他 api 的權杖。

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

需要進行互動的情況如下：

- 使用者同意第一個 API，但現在需要同意更多範圍（增量同意）
- 第一個 API 不需要多重要素驗證，但下一個則是。

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至生產環境](scenario-desktop-production.md)
