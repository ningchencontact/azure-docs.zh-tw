---
title: 呼叫 web Api （取得應用程式的權杖）-Microsoft 身分識別平台的 web 應用程式
description: 了解如何建置 Web 應用程式呼叫 web Api （取得應用程式權杖）
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
ms.openlocfilehash: 653db995000308bb3ef78a9183696cd9d8ed1056
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074796"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Web 應用程式呼叫 web Api-取得應用程式的權杖

現在您已建立用戶端應用程式物件，您將使用它來取得權杖，您將使用它來呼叫 web Api。 在 ASP.NET 或 ASP.NET Core 中，呼叫的 web API 則會完成控制站中。 它是關於：

- Web API 使用權杖快取中取得權杖。 這麼做，您呼叫 `AcquireTokenSilent`
- 呼叫受保護的 API 的存取權杖

## <a name="aspnet-core"></a>ASP.NET Core

控制器方法受到`[Authorize]`會強制使用者從正在驗證來使用 Web 應用程式的屬性。 以下是 呼叫 Microsoft Graph 的程式碼

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

以下是簡化的程式碼，HomeController，它會取得權杖來呼叫 Microsoft Graph 的動作。

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

如果您想要在詳細資料，了解此案例所需的程式碼的總計，請參閱第 2 階段[2-1-Web 應用程式呼叫 Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)的步驟[ms-身分識別-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)教學課程

如有多個額外的複雜性：

- （本教學課程會呈現數個實作） 的 Web 應用程式中實作權杖快取
- 使用者的符號出時，從快取移除帳戶
- 呼叫多個 Api，包括具有增量同意

## <a name="aspnet"></a>ASP.NET

在 ASP.NET 中項目如下：

- 控制器動作受 [Authorize] 屬性，就會擷取租用戶識別碼和使用者識別碼`ClaimsPrincipal`控制站的成員 (使用 ASP.NET `HttpContext.User`)
- 它會從該處建置 MSAL.NET `IConfidentialClientApplication`
- IT 呼叫`AcquireTokenSilent`機密用戶端應用程式方法 

程式碼是類似於 ASP.NET core 所顯示的程式碼

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 Web API](scenario-web-app-call-api-call-api.md)
