---
title: 單一頁面應用程式 (取得權杖以呼叫 API)-Microsoft 身分識別平臺
description: 瞭解如何建立單一頁面應用程式 (取得權杖以呼叫 API)
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f49a6093194ef76a895f2a54f8a78a55da73e7e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135701"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>單一頁面應用程式-取得權杖以呼叫 API

使用 MSAL 來取得 api 之權杖的模式是先嘗試使用`acquireTokenSilent`方法的無訊息權杖要求。 呼叫這個方法時, 程式庫會先檢查瀏覽器儲存體中的快取, 以查看是否有有效的權杖, 並將它傳回。 當快取中沒有有效的權杖時, 它會從隱藏的 iframe 將無訊息 token 要求傳送至 Azure Active Directory (Azure AD)。 此方法也可讓程式庫更新權杖。 如需 Azure AD 中的單一登入會話和權杖存留期值的詳細資訊, 請參閱[權杖存留期](active-directory-configurable-token-lifetimes.md)。

Azure AD 的無訊息 token 要求可能會因為某些原因而失敗, 例如過期的 Azure AD 會話或密碼變更。 在這種情況下, 您可以叫用其中一個互動式方法 (這會提示使用者) 取得權杖。

* 使用[快顯視窗取得權杖](#acquire-token-with-a-pop-up-window)`acquireTokenPopup`
* 使用重新[導向取得權杖](#acquire-token-with-redirect)`acquireTokenRedirect`

**在快顯或重新導向體驗之間進行選擇**

 您不能在應用程式中同時使用快顯和重新導向方法的組合。 快顯或重新導向體驗之間的選擇取決於您的應用程式流程。

* 如果您不想讓使用者在驗證期間離開主應用程式頁面, 建議使用快顯方法。 由於驗證重新導向會在快顯視窗中進行, 因此會保留主應用程式的狀態。

* 在某些情況下, 您可能需要使用重新導向方法。 如果您應用程式的使用者具有瀏覽器條件約束或停用快顯視窗的原則, 您可以使用重新導向方法。 此外, 也建議使用重新導向方法與 Internet Explorer 瀏覽器, 因為在處理快顯視窗時, [Internet explorer 有某些已知問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)。

您可以設定您想要在建立存取權杖要求時包含存取權杖的 API 範圍。 請注意, 在存取權杖中可能不會授與所有要求的範圍, 並取決於使用者的同意。

## <a name="acquire-token-with-a-pop-up-window"></a>使用快顯視窗取得權杖

### <a name="javascript"></a>JavaScript

上述模式使用快顯體驗的方法:

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

MSAL 角度包裝函式提供新增 HTTP 攔截器的便利性, 這會自動以無訊息方式取得存取權杖, 並將其附加至 Api 的 HTTP 要求。

您可以在 [ `protectedResourceMap`設定] 選項中指定 api 的範圍, MsalInterceptor 會在自動取得權杖時要求它。

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

針對無訊息權杖取得成功和失敗, MSAL 角度會提供您可以訂閱的回呼。 也請務必記得取消訂閱。

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

或者, 您也可以使用「核心 MSAL」程式庫中所述的取得權杖方法來明確取得權杖。

## <a name="acquire-token-with-redirect"></a>使用重新導向取得權杖

### <a name="javascript"></a>JavaScript

如上面所述, 這種模式會以互動方式取得權杖的重新導向方法顯示。 您將需要註冊重新導向回呼, 如上所述。

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

## <a name="request-for-optional-claims"></a>選擇性宣告的要求
您可以在應用程式中要求選擇性宣告, 以指定要包含在應用程式權杖中的其他宣告。 若要在 id_token 中要求選擇性宣告, 您可以將 stringified 宣告物件傳送至 AuthenticationParameters 類別的 claimsRequest 欄位。

您可以使用選擇性宣告來進行下列目的:

- 在應用程式的權杖中加入其他宣告。
- 變更 Azure AD 在權杖中傳回之特定宣告的行為。
- 新增和存取應用程式的自訂宣告。


### <a name="javascript"></a>JavaScript
```javascript
"optionalClaims":  
   {
      "idToken": [
            {
                  "name": "auth_time", 
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```
若要深入瞭解選擇性宣告, 請簽出[選擇性宣告](active-directory-optional-claims.md)


### <a name="angular"></a>Angular

這與上面所述的相同。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫 Web API](scenario-spa-call-api.md)
