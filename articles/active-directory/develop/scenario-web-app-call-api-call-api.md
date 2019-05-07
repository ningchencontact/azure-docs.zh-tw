---
title: Web 應用程式呼叫 web Api （呼叫 web API）-Microsoft 身分識別平台
description: 了解如何建置 Web 應用程式呼叫 web Api （呼叫 web API）
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
ms.openlocfilehash: dd44dda06b2f6fc48538f2fb74c0bf8e04d0362b
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074631"
---
# <a name="web-app-that-calls-web-apis---call-a-web-api"></a>呼叫 web Api-的 web 應用程式呼叫 web API

有一個語彙基元之後，您可以呼叫受保護的 web API。

## <a name="aspnet-core"></a>ASP.NET Core

以下是簡化的程式碼的動作`HomeController`。 此程式碼取得權杖來呼叫 Microsoft Graph。 已新增這個時間程式碼，示範如何呼叫 Microsoft Graph 為 REST API。

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

 // Calls the web API (here the graph)
 HttpClient httpClient = new HttpClient();
 httpClient.DefaultRequestHeaders.Authorization =
     new AuthenticationHeaderValue(Constants.BearerAuthorizationScheme,accessToken);
 var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

 if (response.StatusCode == HttpStatusCode.OK)
 {
  var content = await response.Content.ReadAsStringAsync();

  dynamic me = JsonConvert.DeserializeObject(content);
  return me;
 }

 ViewData["Me"] = me;
 return View();
}
```

> [!NOTE]
> 若要呼叫的任何 web API，您可以使用相同的原則。
>
> 大部分的 Azure web Api 提供 SDK，以簡化呼叫它。 這也是 Microsoft Graph 的案例。 哪裡可以找到教學課程中說明這些層面，您將學習在下一篇文章中。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至生產環境](scenario-web-app-call-api-production.md)
