---
title: 呼叫 web Api 的 web API-Microsoft 身分識別平臺 |Azure
description: 瞭解如何建立呼叫 web Api 的 Web API。
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
ms.openlocfilehash: 6063d143e2f217426bdf1db217fde46f8542d314
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965206"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>呼叫 web Api 的 web API-呼叫 API

有了權杖之後，您就可以呼叫受保護的 Web API。 這是從您 ASP.NET/ASP.NET 核心 Web API 的控制器來完成。

## <a name="controller-code"></a>控制器程式碼

以下是受保護 Web API 中顯示的範例程式碼接續，會[呼叫 Web api-取得權杖](scenario-web-api-call-api-acquire-token.md)（在 API 控制器的動作中呼叫），並呼叫下游 API （名為 todolist）。

一旦您取得權杖，請使用它做為持有人權杖來呼叫下游 API。

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

// Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至生產環境](scenario-web-api-call-api-production.md)
