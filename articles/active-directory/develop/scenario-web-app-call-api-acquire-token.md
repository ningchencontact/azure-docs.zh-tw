---
title: 呼叫 web Api 的 web 應用程式（取得應用程式的權杖）-Microsoft 身分識別平臺
description: 瞭解如何建立呼叫 web Api 的 Web 應用程式（取得應用程式的權杖）
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
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3aa144c76fb0a8e479658efdb5d43361fbbc085c
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860619"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>呼叫 web Api 的 web 應用程式-取得應用程式的權杖

現在您已建立用戶端應用程式物件，您將使用它來取得權杖以呼叫 Web API。 在 ASP.NET 或 ASP.NET Core 中，呼叫 Web API 就會在控制器中完成。 其相關資訊如下：

- 使用權杖快取取得 Web API 的權杖。 若要取得此權杖，請`AcquireTokenSilent`呼叫。
- 使用存取權杖呼叫受保護的 API。

## <a name="aspnet-core"></a>ASP.NET Core

控制器方法會受到強制驗證使用者`[Authorize]`使用 Web 應用程式的屬性所保護。 以下是呼叫 Microsoft Graph 的程式碼。

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

以下是 HomeController 動作的簡化程式碼，它會取得用來呼叫 Microsoft Graph 的 token。

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;
 ...
 // use the access token to call a web API
}
```

若要深入瞭解此案例所需的程式碼，請參閱[aspnetcore-webapp-教學](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)課程教學課程的第2階段（[2-1-Web 應用程式呼叫 Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)）步驟。

還有許多額外的複雜性，例如：

- 執行 Web 應用程式的權杖快取（本教學課程會呈現數個實現）
- 當使用者登出時，從快取中移除帳戶
- 呼叫數個 Api，包括具有累加式同意

## <a name="aspnet"></a>ASP.NET

ASP.NET 中的專案很類似：

- 受 [授權] 屬性保護的控制器動作會解壓縮該控制器`ClaimsPrincipal`成員的租使用者識別碼和使用者識別碼。 （ASP.NET 會`HttpContext.User`使用）。
- 它會建立一個 MSAL.NET `IConfidentialClientApplication`。
- 最後，它會呼叫`AcquireTokenSilent`機密用戶端應用程式的方法。

此程式碼類似于 ASP.NET Core 顯示的程式碼。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 Web API](scenario-web-app-call-api-call-api.md)
