---
title: 登入使用者 （登入）-Microsoft 身分識別平台的 web 應用程式
description: 了解如何建置 web 應用程式的登入使用者 （登入）
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
ms.openlocfilehash: 3fb7fbba7ec48da580d2a630ae51aa20b3307848
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074616"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>網頁登入使用者的應用程式-登入

了解如何將登入新增到您的 web 應用程式的程式碼，讓登入使用者。

## <a name="sign-in"></a>登入

我們在上一篇文章中的程式碼[應用程式的程式碼設定](scenario-web-app-sign-user-app-configuration.md)是您只需要實作登出。一旦使用者已登入您的應用程式，您可能會想要讓他們登出。ASP.NET core 會為您處理登出。

## <a name="what-sign-out-involves"></a>什麼登出牽涉到

從 web 應用程式登出，即將於多個 web 應用程式的狀態中移除的登入帳戶的相關資訊。
Web 應用程式必須也將使用者重新導向到 Microsoft 身分識別平台 v2.0`logout`端點才能登出。當您的 web 應用程式將使用者重新導向至`logout`端點，此端點會清除瀏覽器中的使用者工作階段。 如果您的應用程式未移至`logout`端點，使用者會重新驗證您的應用程式不需要輸入其認證，同樣地，因為它們都必須有效單一登入工作階段與 Microsoft 身分識別平台 v2.0 端點。

若要進一步了解，請參閱[傳送登出要求](v2-protocols-oidc.md#send-a-sign-out-request)一節[Microsoft 身分識別平台 v2.0 和 OpenID Connect 通訊協定](v2-protocols-oidc.md)概念文件。

## <a name="application-registration"></a>應用程式註冊

應用程式註冊期間，您會註冊**張貼登出 URI**。 在本教學課程中，您註冊`https://localhost:44321/signout-oidc`中**登出 URL**欄位**進階設定**一節中**驗證**頁面。 如需詳細資訊，請參閱[註冊 webApp 應用程式](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

## <a name="aspnet-core-code"></a>ASP.NET Core 程式碼

### <a name="signout-button"></a>登出按鈕

登出按鈕會在`Views\Shared\_LoginPartial.cshtml`和經過驗證的帳戶 （也就是當使用者先前已登入） 時，才顯示。

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

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()` 動作 `AccountController`

按下**登出**web 應用程式觸發程序上的按鈕`SignOut`動作`Account`控制站。 在舊版的 ASP.NET core 範本`Account`控制器已內嵌 web 應用程式，但這已經不再的問題，因為它現在是 ASP.NET Core 架構本身的一部分。 

程式碼`AccountController`就可使用的 ASP.NET core 存放庫從[AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)。 帳戶控制：

- OpenID 設定重新導向 URI `/Account/SignedOut` ，讓 Azure AD 已執行登出時，會呼叫控制器
- 呼叫`Signout()`，以讓連絡 Microsoft 身分識別平台 OpenIdConnect 中介軟體`logout`端點的：

  - 清除瀏覽器中，從工作階段 cookie 和
  - 呼叫最後會回呼**登出 URL**，它) 根據預設，會顯示已簽署檢視頁面[SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml)也提供做為 ASP.NET Core 的一部分。

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>攔截呼叫`logout`端點

ASP.NET Core OpenIdConnect 的中介軟體可讓您的應用程式，來攔截呼叫至 Microsoft 身分識別平台`logout`端點，藉由提供一個名稱為的 OpenIdConnect 事件`OnRedirectToIdentityProviderForSignOut`。 Web 應用程式會使用它來嘗試避免登出時，要向使用者的 [選取的帳戶] 對話方塊。完成這項攔截`AddAzureAdV2Authentication`的`Microsoft.Identity.Web`可重複使用程式庫。 請參閱[StartupHelpers.cs L58 L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

```CSharp
public static IServiceCollection AddAzureAdV2Authentication(this IServiceCollection services,
                                                            IConfiguration configuration)
{
    ...
    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
        ...
        options.Authority = options.Authority + "/v2.0/";
        ...
        // Attempt to avoid displaying the select account dialog when signing out
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            var user = context.HttpContext.User;
            context.ProtocolMessage.LoginHint = user.GetLoginHint();
            context.ProtocolMessage.DomainHint = user.GetDomainHint();
            await Task.FromResult(0);
        };
    }
}
```

## <a name="aspnet-code"></a>ASP.NET Code

在 ASP.NET 中，從 signout （） 方法 (例如 AccountController) 控制站上觸發登出。 與這個方法不是 ASP.NET framework （相對於該怎麼辦 ASP.NET Core 中） 的一部分，但未使用非同步處理，這就是為什麼它：

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
 MsalAppBuilder.ClearUserTokenCache();
 Response.Redirect("/");
}
```

## <a name="protocol"></a>Protocol

如果您不想要使用 ASP.NET Core 或 ASP.NET，您可以查看所提供的通訊協定文件[Open ID Connect](./v2-protocols-oidc.md)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至生產環境](scenario-web-app-sign-user-production.md)
