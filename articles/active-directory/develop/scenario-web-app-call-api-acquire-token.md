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
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f30194592989b74aca96a5a483e9128cd3a86eb5
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274481"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>呼叫 web Api 的 web 應用程式-取得應用程式的權杖

現在您已建立用戶端應用程式物件，您將使用它來取得權杖以呼叫 Web API。 在 ASP.NET 或 ASP.NET Core 中，呼叫 Web API 就會在控制器中完成。 其相關資訊如下：

- 使用權杖快取取得 Web API 的權杖。 若要取得此權杖，您必須呼叫 `AcquireTokenSilent`。
- 使用存取權杖呼叫受保護的 API。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

控制器方法會受到 @no__t 0 屬性的保護，強制使用者進行驗證以使用 Web 應用程式。 以下是呼叫 Microsoft Graph 的程式碼。

```CSharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions(see code below)

}
```

ASP.NET 會透過相依性插入來插入 `ITokenAcquisition` 服務。


以下是 HomeController 動作的簡化程式碼，它會取得用來呼叫 Microsoft Graph 的 token。

```CSharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // use the access token to call a protected web API
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

若要深入瞭解此案例所需的程式碼，請參閱[aspnetcore-webapp-教學](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)課程教學課程的第2階段（[2-1-Web 應用程式呼叫 Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)）步驟。

還有許多額外的複雜性，例如：

- 呼叫數個 Api，
- 處理累加同意和條件式存取。

這些 advanced 步驟會在教學課程[3-WebApp-多重 api](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs)的第3章中處理

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 中的專案很類似：

- 受 [授權] 屬性保護的控制器動作會解壓縮該控制器的 @no__t 0 成員的租使用者識別碼和使用者識別碼。 （ASP.NET 使用 `HttpContext.User`。）
- 它會建立一個 MSAL.NET `IConfidentialClientApplication`。
- 最後，它會呼叫機密用戶端應用程式的 `AcquireTokenSilent` 方法。

此程式碼類似于 ASP.NET Core 顯示的程式碼。

# <a name="javatabjava"></a>[Java](#tab/java)

在 JAVA 範例中，呼叫 API 的程式碼位於 getUsersFromGraph 方法[AuthPageController. JAVA # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62)。

它會嘗試呼叫 `getAuthResultBySilentFlow`。 如果使用者需要同意更多的範圍，程式碼會處理 `MsalInteractionRequiredException`，以挑戰使用者。

```java
@RequestMapping("/msal4jsample/graph/users")
    public ModelAndView getUsersFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
            throws Throwable {

        IAuthenticationResult result;
        ModelAndView mav;
        try {
            result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
        } catch (ExecutionException e) {
            if (e.getCause() instanceof MsalInteractionRequiredException) {

                // If silent call returns MsalInteractionRequired, then redirect to Authorization endpoint
                // so user can consent to new scopes
                String state = UUID.randomUUID().toString();
                String nonce = UUID.randomUUID().toString();

                SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

                String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                        httpRequest.getParameter("claims"),
                        "User.ReadBasic.all",
                        authHelper.getRedirectUriGraphUsers(),
                        state,
                        nonce);

                return new ModelAndView("redirect:" + authorizationCodeUrl);
            } else {

                mav = new ModelAndView("error");
                mav.addObject("error", e);
                return mav;
            }
        }
    // Code omitted here.
```

# <a name="pythontabpython"></a>[Python](#tab/python)

在 python 範例中，呼叫 Microsoft graph 的程式碼位於[.py # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62)。

它會嘗試從權杖快取取得權杖，然後在設定 authorization 標頭之後呼叫 eb API。 如果無法這麼做，則會重新登入使用者。

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 Web API](scenario-web-app-call-api-call-api.md)
