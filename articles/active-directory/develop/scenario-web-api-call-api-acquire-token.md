---
title: 為呼叫 web Api 的 Web API 取得權杖 |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何建立 Web API，以呼叫需要取得應用程式權杖的 web Api。
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
ms.openlocfilehash: 276ff1e5e9f709aa5b38d1efa4055dfe3baf3cc5
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919778"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>呼叫 web Api 的 web API-取得應用程式的權杖

建立用戶端應用程式物件之後，請使用它來取得可用於呼叫 Web API 的權杖。

## <a name="code-in-the-controller"></a>控制器中的程式碼

以下是會在 API 控制器的動作中呼叫下游 API （名為 todolist）的程式碼範例。

```CSharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...
}
```

`BuildConfidentialClient()` 類似于您在[呼叫 Web api 的 WEB api （應用程式設定）一](scenario-web-api-call-api-app-configuration.md)文中看到的內容。 `BuildConfidentialClient()` 使用只包含一個帳戶資訊的快取來具現化 `IConfidentialClientApplication`。 此帳戶是由 `GetAccountIdentifier` 方法所提供。

`GetAccountIdentifier` 方法會使用與 Web API 收到 JWT 之使用者的身分識別相關聯的宣告：

```CSharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 Web API](scenario-web-api-call-api-call-api.md)
