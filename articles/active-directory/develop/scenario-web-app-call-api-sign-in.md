---
title: 呼叫 web Api （登入）-Microsoft 身分識別平台的 web 應用程式
description: 了解如何建置呼叫 web Api （登入） 的 Web 應用程式
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
ms.openlocfilehash: 663cea72eb620217ad5fa8925d3bb00eedbf890c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074556"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Web 應用程式呼叫 web Api-登入

您已經知道如何將登入新增至您的 web 應用程式。 您將了解，在[登入使用者-新增登入的 Web 應用程式](scenario-web-app-sign-user-sign-in.md)。

什麼不同這裡，是當使用者已簽署時，此應用程式，或任何應用程式，您想移除權杖快取中，與使用者相關聯的權杖。

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>登出後-單一登出攔截回呼

您的應用程式可以攔截之後`logout`事件，例如清除權杖快取已登出的帳戶相關聯的項目。我們會看到在本教學課程 （關於呼叫 Web API 的 Web 應用程式） 的第二部分中，web 應用程式，會在快取中儲存使用者的存取權杖。 攔截之後`logout`回呼可讓您的 web 應用程式，以從權杖快取中移除使用者。 這項機制所示`AddMsal()`方法的[StartupHelper.cs L137 143](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L137-L143)

**登出 Url**您已註冊您的應用程式可讓您實作單一登出。Microsoft 身分識別平台`logout`端點會呼叫**登出 URL**向您的應用程式。 如果登出起始從您的 web 應用程式，或從另一個 web 應用程式或瀏覽器，則會發生這個呼叫。 如需詳細資訊，請參閱 <<c0> [ 單一登出](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc#single-sign-out)概念文件。

```CSharp
public static IServiceCollection AddMsal(this IServiceCollection services, IEnumerable<string> initialScopes)
{
    services.AddTokenAcquisition();

    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
     ...
        // Handling the sign-out: removing the account from MSAL.NET cache
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            // Remove the account from MSAL.NET token cache
            var _tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
            await _tokenAcquisition.RemoveAccount(context);
        };
    });
    return services;
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [取得 web 應用程式的權杖](scenario-web-app-call-api-acquire-token.md)
