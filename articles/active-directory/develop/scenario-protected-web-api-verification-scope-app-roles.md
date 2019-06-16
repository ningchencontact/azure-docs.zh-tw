---
title: 受保護的 web API-應用程式程式碼設定 |Azure Active Directory
description: 了解如何建立受保護的 Web API，並設定您的應用程式程式碼。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: b700825be9a7fe23fe4b50a2d69d4de71f7dc038
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116450"
---
# <a name="protected-web-api---adding-authorization-to-your-api"></a>受保護的 web API-將授權新增至您的 API

本文說明如何將授權新增至您的 Web API。 這項保護，以確保只會呼叫：

- 代表具有正確範圍的使用者的應用程式 
- 或精靈應用程式，以正確的應用程式角色。

Asp.net / ASP.NET Core Web API，可受到保護，您必須新增`[Authorize]`屬性：

- 如果您想要保護的控制器的所有動作本身的控制站
- 或個別的控制器動作，為您的 API。

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

但這項保護是不夠的。 它只 guaranties 該 ASP.NET / ASP.NET Core 會驗證權杖。 您的 API 需要驗證語彙基元，用來呼叫 Web API 以宣告它預期，特別是要求：

- **範圍**如果代表使用者呼叫 API
- **應用程式角色**如果可以從精靈應用程式呼叫 API。

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>正在驗證中代表使用者呼叫 Api 的範圍

如果您的 API 呼叫的用戶端應用程式代表使用者，則需要要求 API 的特定範圍的持有人權杖 (請參閱[程式碼組態 |持有人權杖](scenario-protected-web-api-app-configuration.md#bearer-token))

```CSharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The Web API will only accept tokens 1) for users, 2) having the `access_as_user` scope for
    /// this API
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result
        ...
    }
...
}
```

`VerifyUserHasAnyAcceptedScope`方法會執行類似下列內容：

- 確認沒有名為宣告`http://schemas.microsoft.com/identity/claims/scope`或 `scp`
- 確認宣告具有值，包含 API 所預期的範圍。

```CSharp
    /// <summary>
    /// When applied to an <see cref="HttpContext"/>, verifies that the user authenticated in the 
    /// Web API has any of the accepted scopes.
    /// If the authenticated user does not have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized with the message telling which scopes are expected in the token
    /// </summary>
    /// <param name="acceptedScopes">Scopes accepted by this API</param>
    /// <exception cref="HttpRequestException"/> with a <see cref="HttpResponse.StatusCode"/> set to 
    /// <see cref="HttpStatusCode.Unauthorized"/>
    public static void VerifyUserHasAnyAcceptedScope(this HttpContext context,
                                                     params string[] acceptedScopes)
    {
        if (acceptedScopes == null)
        {
            throw new ArgumentNullException(nameof(acceptedScopes));
        }
        Claim scopeClaim = HttpContext?.User
                                      ?.FindFirst("http://schemas.microsoft.com/identity/claims/scope");
        if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            string message = $"The 'scope' claim does not contain scopes '{string.Join(",", acceptedScopes)}' or was not found";
            throw new HttpRequestException(message);
        }
    }
```

此範例程式碼是 ASP.NET core。 ASP.NET 只取代`HttpContext.User`所`ClaimsPrincipal.Current`，和宣告類型`"http://schemas.microsoft.com/identity/claims/scope"`由`"scp"`（請參閱下列程式碼片段）

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>確認在精靈應用程式所呼叫的 Api 應用程式角色

如果您的 Web API 會呼叫[精靈應用程式](scenario-daemon-overview.md)，則該應用程式需要有您的 Web API 應用程式權限。 我們看到 [scenario-protected-web-api-app-registration.md#how-to-expose-application-permissions--app-roles-] 中，您的 API 會公開這類權限 (例如為`access_as_application`應用程式角色)。
您現在必須能夠確認它所收到的權杖包含 Api`roles`宣告與此宣告具有它所預期的值。 驗證委派的權限，差別在於，而不是測試的程式碼執行這項驗證的程式碼大致`scopes`，將測試控制器動作`roles`:

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

`ValidateAppRole()`方法可以是如下：

```CSharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case we look for a `role` value of `access_as_application`
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage 
        { StatusCode = HttpStatusCode.Unauthorized, 
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found" 
        });
    }
}
}
```

此範例程式碼是 asp.net。 針對 ASP.NET Core，只要取代`ClaimsPrincipal.Current`所`HttpContext.User`並`"roles"`宣告的名稱`"http://schemas.microsoft.com/identity/claims/roles"`（請參閱上述的程式碼片段）

### <a name="accepting-app-only-tokens-if-the-web-api-should-only-be-called-by-daemon-apps"></a>接受應用程式的唯一權杖，如果只應該由精靈應用程式呼叫 Web API

`roles`宣告也適用於使用者指派模式中的使用者 (請參閱[How to:應用程式中加入應用程式角色，並在權杖中接收這些](howto-add-app-roles-in-azure-ad-apps.md))。 如果角色是指派給兩者，因此只核取角色可讓使用者和利用其他方式，以登入的應用程式。 我們建議有不同的角色宣告的使用者和應用程式，以避免混淆。

如果您想要只允許呼叫 Web API 的精靈應用程式，您會想要新增條件，當您驗證應用程式角色中，此語彙基元是僅限應用程式的權杖：

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid");
string sub = ClaimsPrincipal.Current.FindFirst("sub");
bool isAppOnlyToken = oid == sub;
```

核取反條件可讓使用者登入，以呼叫您的 API 應用程式。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至生產環境](scenario-protected-web-api-production.md)
