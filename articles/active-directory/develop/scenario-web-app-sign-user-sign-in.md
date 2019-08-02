---
title: 登入使用者的 Web 應用程式 (登入)-Microsoft 身分識別平臺
description: 瞭解如何建立登入使用者的 web 應用程式 (登入)
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
ms.openlocfilehash: be7801515355452306cd5e7afa709a0681c7c314
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562201"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>使用者登入的 Web 應用程式

瞭解如何針對登入使用者的 web 應用程式, 將登入新增至程式碼。

## <a name="sign-in"></a>登入

我們在前一篇文章中所擁有的[程式碼](scenario-web-app-sign-user-app-configuration.md)設定, 就是您在執行登出的所有需求。一旦使用者登入您的應用程式, 您可能會想要讓他們登出。ASP.NET 核心會為您處理登出。

## <a name="what-sign-out-involves"></a>登出牽涉到的內容

登出 web 應用程式是關於從 web 應用程式的狀態中移除登入帳戶的相關資訊。
Web 應用程式也必須將使用者重新導向至 Microsoft 身分識別`logout`平臺端點, 才能登出。當您的 web 應用程式將使用者重新`logout`導向至端點時, 此端點會清除瀏覽器中的使用者會話。 如果您的應用程式未移`logout`至端點, 則使用者會重新驗證您的應用程式, 而不會再次輸入其認證, 因為他們會擁有與 Microsoft 身分識別平臺端點的有效單一登入會話。

若要深入瞭解, 請參閱[Microsoft 身分識別平臺和 OpenID connect 通訊協定](v2-protocols-oidc.md)概念檔中的[傳送登出要求](v2-protocols-oidc.md#send-a-sign-out-request)一節。

## <a name="application-registration"></a>應用程式註冊

在應用程式註冊期間, 您會註冊**登出後的 URI**。 在我們的教學課程中`https://localhost:44321/signout-oidc` , 您已在 [**驗證**] 頁面的 [ **Advanced Settings** ] 區段的 [**登出 URL** ] 欄位中註冊。 如需詳細資訊, 請參閱[註冊 webApp 應用程式](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

## <a name="aspnet-core-code"></a>ASP.NET Core 程式碼

### <a name="signout-button"></a>Signout 按鈕

[登出] 按鈕會在中`Views\Shared\_LoginPartial.cshtml`公開, 而且只有在有已驗證的帳戶時才會顯示 (也就是當使用者先前已登入時)。

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

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()`動作`AccountController`

按下 web 應用程式上的 [**登出**] 按鈕`SignOut` , 會在`Account`控制器上觸發動作。 在舊版的 ASP.NET core 範本中, `Account`控制器已內嵌在 web 應用程式中, 但這已不再是 ASP.NET Core 架構本身的一部分。 

的`AccountController`程式碼可從[AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)的 ASP.NET core 存放庫取得。 帳戶控制:

- 將 OpenID 重新導向 URI 設定`/Account/SignedOut`為, 以便在 Azure AD 執行登出時回呼控制器
- 呼叫`Signout()`, 可讓 OpenIdConnect 中介軟體連線到 Microsoft 身分識別`logout`平臺端點, 這會:

  - 從瀏覽器清除會話 cookie, 然後
  - 呼叫最後會呼叫 [**登出 URL**], 預設會顯示 [已登出] 視圖頁面[SignedOut](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) , 同時也會提供 ASP.NET Core 的一部分。

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>攔截對`logout`端點的呼叫

ASP.NET Core OpenIdConnect 中介軟體可讓您的應用程式藉由提供名為`logout` `OnRedirectToIdentityProviderForSignOut`的 OpenIdConnect 事件, 攔截對 Microsoft 身分識別平臺端點的呼叫。 Web 應用程式會使用它來嘗試避免在登出時向使用者顯示 [選取帳戶] 對話方塊。此攔截作業會在`AddAzureAdV2Authentication` `Microsoft.Identity.Web`可重複使用之程式庫的中完成。 請參閱[StartupHelpers.cs L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

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

## <a name="aspnet-code"></a>ASP.NET 程式碼

在 ASP.NET 中, 登出是由控制器 (例如 AccountController) 上的 SignOut () 方法所觸發。 這個方法不是 ASP.NET 架構的一部分 (相對於 ASP.NET Core 所發生的動作), 而且也不會使用 async, 這也是為什麼會這麼做:

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

如果您不想要使用 ASP.NET Core 或 ASP.NET, 您可以查看可從[OPEN ID Connect](./v2-protocols-oidc.md)取得的通訊協定檔。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至生產環境](scenario-web-app-sign-user-production.md)
