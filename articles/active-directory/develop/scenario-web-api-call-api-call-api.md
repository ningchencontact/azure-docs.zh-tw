---
title: Web API 呼叫 web Api （呼叫 Api）-Microsoft 身分識別平台
description: 了解如何建置 web API，其呼叫下游 web Api （呼叫 web API）。
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
ms.openlocfilehash: 1cd093cc68a21558dc326221eeaa8c034c24f1c2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074721"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>呼叫 web Api-的 web API 呼叫的 API

語彙基元之後，您可以呼叫受保護的 web API。 這是從您 ASP.NET/ASP.NET Core web API 控制器。

## <a name="controller-code"></a>控制器程式碼

以下是範例程式碼所示的接續[受保護 web API 呼叫 web Api-權杖](scenario-web-api-call-api-acquire-token.md)，稱為 API 控制器的動作，在呼叫 （名為 todolist） 下游 API。

一旦您取得權杖，使用它做為持有人權杖來呼叫下游 API。

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
