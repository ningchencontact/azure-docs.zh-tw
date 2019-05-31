---
title: 避免頁面重新載入 (Microsoft Authentication Library for JavaScript) |Azure
description: 了解如何取得時，避免頁面重新載入和續訂的權杖，以無訊息方式使用 Microsoft Authentication Library for JavaScript (MSAL.js)。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 162811221e6dde89ad11f358b2ec8f32f3c82522
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420464"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>避免頁面重新載入時取得和續訂的權杖，以無訊息模式使用 MSAL.js
Microsoft Authentication Library for JavaScript (MSAL.js) 使用隱藏`iframe`來取得和更新權杖，以無訊息方式在背景中的項目。 Azure AD 傳回權杖傳回給權杖要求中指定的已註冊 redirect_uri （預設情況下這是應用程式的根頁面）。 由於回應 302，它會產生對應至的 HTML`redirect_uri`開始載入`iframe`。 通常應用程式的`redirect_uri`是 [根] 頁面，因而導致它重新載入。

在其他情況下，如果瀏覽至應用程式的根頁面需要驗證，它可能會導致為巢狀`iframe`項目或`X-Frame-Options: deny`時發生錯誤。

MSAL.js 無法解除 302，Azure AD 所簽發，然後才能處理程序傳回的權杖，因為它無法防止`redirect_uri`從開始載入`iframe`。

若要避免再次重新載入整個應用程式或其他基於此原因而造成的錯誤，請遵循這些因應措施。

## <a name="specify-different-html-for-the-iframe"></a>請指定不同的 HTML iframe

設定`redirect_uri`簡單的頁面上，不需要驗證的設定上的屬性。 您必須先確定它符合與`redirect_uri`在 Azure 入口網站中註冊。 這不會影響使用者的登入體驗，因為當使用者開始登入程序，並將重新導向至正確的位置登入完成之後，MSAL 會儲存 [開始] 頁面。

## <a name="initialization-in-your-main-app-file"></a>在您的主要應用程式檔案的初始設定

如果您的應用程式的結構，因此會有一個中央的 Javascript 檔案，可定義應用程式的初始化、 路由及其他項目，您可以有條件地載入根據應用程式中是否正在載入您的應用程式模組`iframe`與否。 例如:

在 AngularJS: app.js

```javascript
// Check that the window is an iframe and not popup
if (window !== window.parent && !window.opener) {
angular.module('todoApp', ['ui.router', 'MsalAngular'])
    .config(['$httpProvider', 'msalAuthenticationServiceProvider','$locationProvider', function ($httpProvider, msalProvider,$locationProvider) {
        msalProvider.init(
            // msal configuration
        );

        $locationProvider.html5Mode(false).hashPrefix('');
    }]);
}
else {
    angular.module('todoApp', ['ui.router', 'MsalAngular'])
        .config(['$stateProvider', '$httpProvider', 'msalAuthenticationServiceProvider', '$locationProvider', function ($stateProvider, $httpProvider, msalProvider, $locationProvider) {
            $stateProvider.state("Home", {
                url: '/Home',
                controller: "homeCtrl",
                templateUrl: "/App/Views/Home.html",
            }).state("TodoList", {
                url: '/TodoList',
                controller: "todoListCtrl",
                templateUrl: "/App/Views/TodoList.html",
                requireLogin: true
            })

            $locationProvider.html5Mode(false).hashPrefix('');

            msalProvider.init(
                // msal configuration
            );
        }]);
}
```

在 Angular: app.module.ts

```javascript
// Imports...
@NgModule({
  declarations: [
    AppComponent,
    MsalComponent,
    MainMenuComponent,
    AccountMenuComponent,
    OsNavComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }),
    MsalModule.forRoot(environment.MsalConfig),
    SuiModule,
    PagesModule
  ],
  providers: [
    HttpServiceHelper,
    {provide: HTTP_INTERCEPTORS, useClass: MsalInterceptor, multi: true},
    AuthService
  ],
  entryComponents: [
    AppComponent,
    MsalComponent
  ]
})
export class AppModule {
  constructor() {
    console.log('APP Module Constructor!');
  }

  ngDoBootstrap(ref: ApplicationRef) {
    if (window !== window.parent && !window.opener)
    {
      console.log("Bootstrap: MSAL");
      ref.bootstrap(MsalComponent);
    }
    else
    {
    //this.router.resetConfig(RouterModule);
      console.log("Bootstrap: App");
      ref.bootstrap(AppComponent);
    }
  }
}
```

MsalComponent:

```javascript
import { Component} from '@angular/core';
import { MsalService } from '@azure/msal-angular';

// This component is used only to avoid Angular reload
// when doing acquireTokenSilent()

@Component({
  selector: 'app-root',
  template: '',
})
export class MsalComponent {
  constructor(private Msal: MsalService) {
  }
}
```

## <a name="next-steps"></a>後續步驟
深入了解[建置單一頁面應用程式 (SPA)](scenario-spa-overview.md)使用 MSAL.js。