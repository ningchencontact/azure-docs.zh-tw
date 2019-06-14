---
title: 單一頁面應用程式 （應用程式的程式碼設定）-Microsoft 身分識別平台
description: 了解如何建置單一頁面應用程式 （應用程式的程式碼組態）
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b71454fc553a0f81c26426a6a9588f15d5311e38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65406426"
---
# <a name="single-page-application---code-configuration"></a>單一頁面應用程式-程式碼組態

了解如何設定您的單一頁面應用程式 (SPA) 的程式碼。

## <a name="msal-libraries-supporting-implicit-flow"></a>支援隱含流程的 MSAL 程式庫

Microsoft 身分識別平台提供 MSAL.js 程式庫，以支援使用業界隱含流程建議的安全的作法。  

支援隱含流程的程式庫是：

| MSAL 程式庫 | 描述 |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | 用於建置使用 JavaScript 或 SPA 架構，例如 Angular、 Vue.js、 React.js 等任何用戶端 web 應用程式中的純 JavaScript 程式庫。 |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | 核心 MSAL.js 程式庫，以簡化使用 Angular framework 建置的單一頁面應用程式中使用的包裝函式。 此程式庫目前為預覽狀態，且[已知問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular)特定 Angular 的版本與瀏覽器。 |

## <a name="application-code-configuration"></a>應用程式程式碼組態

在 MSAL 程式庫，應用程式註冊資訊會傳遞做為程式庫初始化期間設定。

### <a name="javascript"></a>JavaScript

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```
如需有關可用之可設定選項的詳細資訊，請參閱 <<c0> [ 初始化應用程式與 MSAL.js](msal-js-initializing-client-applications.md)。

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientId: 'your_app_id'
            })]
         })

  export class AppModule { }
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [登入和登出](scenario-spa-sign-in.md)
