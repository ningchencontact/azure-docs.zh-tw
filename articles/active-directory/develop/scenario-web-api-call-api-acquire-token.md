---
title: Web API 呼叫其他 web 的 Api （取得應用程式的權杖）-Microsoft 身分識別平台
description: 了解如何建置 web API 呼叫其他 web Api （取得應用程式權杖）。
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
ms.openlocfilehash: ada4323b0e61c6dc9fb87af133c40ec9c35e3834
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074976"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>Web API 呼叫 web Api-取得應用程式的權杖

一旦建置用戶端應用程式物件，請使用它來取得權杖，您可以用來呼叫 web API。

## <a name="code-in-the-controller"></a>在控制器中的程式碼

以下是將呼叫的 API 控制器，呼叫下游 API （名為 todolist） 動作中的程式碼範例。

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

`BuildConfidentialClient()` 類似於您已了解文中所[Web API 呼叫 web Api-應用程式設定](scenario-web-api-call-api-app-configuration.md)。 `BuildConfidentialClient()` 具現化`IConfidentialClientApplication`快取，其中包含只針對一個帳戶的資訊。 此帳戶是由`GetAccountIdentifier`方法。

`GetAccountIdentifier`方法會使用 web API 收到的 JWT 之使用者的身分識別相關聯的宣告：

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
> [呼叫 web API](scenario-web-api-call-api-call-api.md)
