---
title: 單一頁面應用程式 （呼叫 web API）-Microsoft 身分識別平台
description: 了解如何建置單一頁面應用程式 （呼叫 web API）
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77a4ed01ac55a1153a62c672b33056a543b912ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545603"
---
# <a name="single-page-application---call-a-web-api"></a>單一頁面應用程式層 web API 的呼叫

我們建議您呼叫`acquireTokenSilent`取得，或更新存取權杖呼叫 web API 之前的方法。 語彙基元之後，您可以呼叫受保護的 web API。

## <a name="call-a-web-api"></a>呼叫 Web API

### <a name="javascript"></a>JavaScript

請使用為 HTTP 要求中的持有人取得的存取權杖呼叫 Microsoft 圖形 API 等任何 web API。 例如:

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

### <a name="angular"></a>Angular

中所述[取得權杖 區段](scenario-spa-acquire-token.md)，MSAL Angular 的包裝函式會利用 HTTP 攔截器會自動以無訊息方式取得存取權杖，並將它們附加至 Api 的 HTTP 要求。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至生產環境](scenario-spa-production.md)
