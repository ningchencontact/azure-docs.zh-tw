---
title: 傳統型應用程式呼叫 web Api （呼叫 web API）-Microsoft 身分識別平台
description: 了解如何建置傳統型應用程式呼叫 web Api （呼叫 web API）
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
ms.openlocfilehash: 4abaf234d3b216e0f67501e5d2f2f5c3f874c5d7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111251"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>呼叫 web Api-的傳統型應用程式呼叫 web API

有一個語彙基元之後，您可以呼叫受保護的 web API。

## <a name="calling-a-web-api-from-net"></a>從.NET 呼叫 web API

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>呼叫多個 Api-增量同意和條件式存取

如果您需要針對相同的使用者，呼叫數個 Api，一旦您取得權杖的第一個 API，您可以直接呼叫`AcquireTokenSilent`，就會出現一個語彙基元的其他 api 以無訊息方式大部分的情況。

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

互動所需的案例是當：

- 使用者同意的第一個 API，但現在必須同意更多範圍 （增量同意）
- 第一個 API 不需要多重要素驗證，但下一個。

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至生產環境](scenario-desktop-production.md)
