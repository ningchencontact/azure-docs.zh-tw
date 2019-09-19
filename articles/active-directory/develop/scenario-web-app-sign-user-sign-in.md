---
title: 登入使用者的 Web 應用程式（登入）-Microsoft 身分識別平臺
description: 瞭解如何建立登入使用者的 web 應用程式（登入）
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ed61a09ffc76b4813dcb97330d3a1a436aa16eb
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086465"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>使用者登入的 Web 應用程式

瞭解如何針對登入使用者的 web 應用程式，將登入新增至程式碼。

## <a name="sign-in"></a>登入

我們在前一篇文章[應用程式的程式碼](scenario-web-app-sign-user-app-configuration.md)設定中所複習的程式碼，就是您在執行登入時所需的一切。
一旦使用者登入您的應用程式，您可能會想要讓他們登出。ASP.NET 核心會為您處理登出。

## <a name="what-sign-out-involves"></a>登出牽涉到的內容

登出 web 應用程式是關於從 web 應用程式的狀態中移除登入帳戶的相關資訊。
Web 應用程式也必須將使用者重新導向至 Microsoft 身分識別`logout`平臺端點，才能登出。當您的 web 應用程式將使用者重新`logout`導向至端點時，此端點會清除瀏覽器中的使用者會話。 如果您的應用程式未移`logout`至端點，則使用者會重新驗證您的應用程式，而不會再次輸入其認證，因為他們會擁有與 Microsoft 身分識別平臺端點的有效單一登入會話。

若要深入瞭解，請參閱[Microsoft 身分識別平臺和 OpenID connect 通訊協定](v2-protocols-oidc.md)概念檔中的[傳送登出要求](v2-protocols-oidc.md#send-a-sign-out-request)一節。

### <a name="application-registration"></a>應用程式註冊

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在應用程式註冊期間，您會註冊**登出後的 URI**。 在我們的教學課程中`https://localhost:44321/signout-oidc` ，您已在 [**驗證**] 頁面的 [ **Advanced Settings** ] 區段的 [**登出 URL** ] 欄位中註冊。 如需詳細資訊，請參閱[註冊 webApp 應用程式](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在應用程式註冊期間，您會註冊**登出後的 URI**。 在我們的教學課程中`https://localhost:44308/Account/EndSession` ，您已在 [**驗證**] 頁面的 [ **Advanced Settings** ] 區段的 [**登出 URL** ] 欄位中註冊。 如需詳細資訊，請參閱[註冊 webApp 應用程式](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)

# <a name="javatabjava"></a>[Java](#tab/java)

在應用程式註冊期間，您將註冊**登出後的 URI**。 在我們的教學課程中`http://localhost:8080/msal4jsample/` ，您已在 [**驗證**] 頁面的 [ **Advanced Settings** ] 區段的 [**登出 URL** ] 欄位中註冊。

# <a name="pythontabpython"></a>[Python](#tab/python)

在應用程式註冊期間，您不需要註冊登出 URL。 此範例不會執行全域登出

---

### <a name="sign-out-button"></a>登出按鈕

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core 中，[登出] 按鈕會在中`Views\Shared\_LoginPartial.cshtml`公開，而且只有在有已驗證的帳戶時才會顯示（也就是當使用者先前已登入時）。

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET MVC 中，[登出] 按鈕會在中`Views\Shared\_LoginPartial.cshtml`公開，而且只有在有已驗證的帳戶時才會顯示（也就是當使用者先前已登入時）。

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

在我們的 JAVA 快速入門中，[登出] 按鈕位於 main/resources/templates/auth_page.html 檔案中。

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="pythontabpython"></a>[Python](#tab/python)

在 Python 快速入門中，[登出] 按鈕位於[templates/display.html](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/templates/display.html#L18-L20)檔案中。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Acquire Token Result </title>
</head>
<body>
    {% if cond  %}
        <p1><b>Your information</b> </p1>
        <table>
        {% for key, value in auth_result.items() %}
           <tr>
                <th> {{ key }} </th>
                <td> {{ value }} </td>
           </tr>
        {% endfor %}
        </table>
        <form action="/logout" >
            <input type="submit" value=" Logout"/>
        </form>
    {% else %}
        <p1><b> {{auth_result}} </b> </p1>
        <form action="/authenticate" >
            <input type="submit" value=" Sign-in"/>
        </form>
    {% endif %}
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`Signout()`控制器的動作

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET 中，按 web 應用程式上的 [**登出**] 按鈕會`SignOut`在`AccountController`控制器上觸發動作。 在舊版的 ASP.NET core 範本中， `Account`控制器已內嵌在 web 應用程式中，但已不再是 ASP.NET Core 架構本身的一部分。

的`AccountController`程式碼可從[AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)的 ASP.NET core 存放庫取得。 帳戶控制：

- 將 OpenID 重新導向 URI 設定`/Account/SignedOut`為，以便在 Azure AD 執行登出時回呼控制器
- 呼叫`Signout()`，可讓 OpenIdConnect 中介軟體連線到 Microsoft 身分識別`logout`平臺端點，這會：

  - 從瀏覽器清除會話 cookie，然後
  - 最後，會呼叫**登出 URL**，其預設會顯示 SignedOut 的已登出視圖頁面[。 html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml)也提供 ASP.NET Core。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，會從控制器上的`SignOut()`方法（例如 AccountController）觸發登出。 這個方法不是 ASP.NET 架構的一部分（相對於 ASP.NET Core 所發生的情況）。 它不會`async`使用，因此它的原因如下：

- 傳送 OpenId 登出挑戰
- 清除快取
- 重新導向至它想要的頁面

```CSharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 Response.Redirect("/");
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

在 JAVA 中，登出的處理方式是直接呼叫 Microsoft 身分識別平臺登出端點，並提供 post_logout_redirect_uri。

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
@app.route("/logout")
def logout():
    return flask.redirect(flask.url_for('index'))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>攔截對`logout`端點的呼叫

登出後的 URI 可讓應用程式參與全域登出。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core OpenIdConnect 中介軟體可讓您的應用程式藉由提供名為`logout` `OnRedirectToIdentityProviderForSignOut`的 OpenIdConnect 事件，攔截對 Microsoft 身分識別平臺端點的呼叫。 如需如何訂閱此事件（以清除權杖快取）的範例，請參閱[WebAppServiceCollectionExtensions L156 # L151-。](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156)

```CSharp
               // Handling the global sign-out
                options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
                {
                    // Forget about the signed-in user
                };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，您會委派至中介軟體來執行登出，並清除會話 cookie：

```CSharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

在我們的 JAVA 快速入門中，post 登出重新導向 URI 只會顯示 [.html] 頁面 

# <a name="pythontabpython"></a>[Python](#tab/python)

在 Python 快速入門中，post 登出重新導向 URI 只會顯示 [.html] 頁面。

---

## <a name="protocol"></a>Protocol

如果您想要深入瞭解登出，請閱讀可從[OPEN ID Connect](./v2-protocols-oidc.md)取得的通訊協定檔。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至生產環境](scenario-web-app-sign-user-production.md)
