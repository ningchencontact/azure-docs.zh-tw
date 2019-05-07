---
title: 單一頁面應用程式 （取得權杖來呼叫的 API）-Microsoft 身分識別平台
description: 了解如何建置單一頁面應用程式 （取得權杖來呼叫 API）
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
ms.openlocfilehash: f4c842db8a0874d3619e0dc59b90aa12226cb984
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138807"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>單一頁面應用程式取得權杖來呼叫 API

取得權杖，MSAL.js Api 第一次嘗試無訊息的權杖要求使用的模式`acquireTokenSilent`方法。 呼叫這個方法時，程式庫會先檢查是否有效的語彙基元存在，並傳回它的瀏覽器儲存體中的快取。 當快取中沒有任何有效的語彙基元時，它會傳送無訊息的權杖要求至 Azure Active Directory (Azure AD) 從隱藏的 iframe。 這個方法也可讓要更新權杖的程式庫。 如需進一步瞭解單一登入工作階段和在 Azure AD 中的權杖存留期值的詳細資訊，請參閱[權杖存留期](active-directory-configurable-token-lifetimes.md)。

Azure ad 無訊息的權杖要求可能會基於某些原因，例如過期的 Azure AD 工作階段或密碼變更失敗。 在此情況下，您可以啟動其中一種互動式的方法 （這會提示使用者） 來取得權杖。

* [取得與快顯視窗的語彙基元](#acquire-token-with-a-pop-up-window)使用 `acquireTokenPopup`
* [取得權杖，使用重新導向](#acquire-token-with-redirect)使用 `acquireTokenRedirect`

**選擇快顯視窗或重新導向的體驗**

 您無法在您的應用程式中使用快顯視窗和 重新導向方法的組合。 快顯視窗或重新導向的體驗之間的選擇取決於您的應用程式的流程。

* 如果您不想離開主應用程式頁面，在驗證期間的使用者，建議使用的快顯的方法。 因為驗證重新導向發生在快顯視窗中，會保留主要的應用程式的狀態。

* 有某些情況下，您可能需要使用重新導向方法。 如果您的應用程式的使用者有瀏覽器的條件約束或原則會在停用快顯視窗，您可以使用重新導向方法。 也建議使用 Internet Explorer 瀏覽器的重新導向方法，因為某些[已知問題與 Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)處理快顯視窗時。

您可以設定您想要建置的存取權杖要求時包含的存取權杖的 API 範圍。 請注意，所有要求的範圍不會授與存取權杖中，取決於使用者的同意。

## <a name="acquire-token-with-a-pop-up-window"></a>取得權杖，以快顯視窗

### <a name="javascript"></a>JavaScript

上述模式的方式，以快顯的體驗：

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

### <a name="angular"></a>Angular

MSAL Angular 的包裝函式提供了新增 HTTP 攔截器的便利性`MsalInterceptor`這會自動以無訊息方式取得存取權杖，並將它們附加至 Api 的 HTTP 要求。

您可以指定的範圍中的 Api`protectedResourceMap`自動取得權杖時，會要求 MsalInterceptor 組態選項。

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                protectedResourceMap: {"https://graph.microsoft.com/v1.0/me", ["user.read", "mail.send"]}
            })]
         })

providers: [ ProductService, {
        provide: HTTP_INTERCEPTORS,
        useClass: MsalInterceptor,
        multi: true
    }
   ],
```

為成功和失敗的無訊息的權杖，MSAL Angular 提供您可以訂閱的回呼。 它也是務必記得要取消訂閱。

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if(this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

或者，您可以明確地取得權杖核心 MSAL.js 程式庫中所述，使用取得權杖方法。

## <a name="acquire-token-with-redirect"></a>取得權杖，使用重新導向

### <a name="javascript"></a>JavaScript

模式是為上面所述但與重新導向方法來取得權杖以互動方式顯示。 請注意，您必須註冊重新導向回撥，如先前所述。

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

### <a name="angular"></a>Angular

這是與上述相同。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 Web API](scenario-spa-call-api.md)
