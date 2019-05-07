---
title: 單一頁面應用程式 （登入）-Microsoft 身分識別平台
description: 了解如何建置單一頁面應用程式 （登入）
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc9c46ae28960387e6f8efc1ade20afa1c77ef55
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138793"
---
# <a name="single-page-application---sign-in"></a>單一頁面應用程式-登入

了解如何將登入新增至您的單一頁面應用程式的程式碼。

您可以取得權杖來存取您的應用程式中的 Api 之前，您必須將已驗證的使用者內容。 您可以登入使用者 MSAL.js 應用程式有兩種：

* [使用快顯視窗登入](#sign-in-with-a-pop-up-window)使用`loginPopup`方法
* [登入重新導向](#sign-in-with-redirect)使用`loginRedirect`方法

您可以選擇性地傳遞 Api 需要使用者同意在登入的時間的範圍。

> [!NOTE]
> 如果您的應用程式已經可以存取已驗證的使用者內容或識別碼權杖，您可以略過登入步驟，並直接取得權杖。 如需詳細資訊，請參閱 <<c0> [ 沒有 msal.js 登入的 sso](msal-js-sso.md#sso-without-msaljs-login)。

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>選擇快顯視窗或重新導向的體驗

您無法在您的應用程式中使用快顯視窗和 重新導向方法的組合。 快顯視窗或重新導向的體驗之間的選擇取決於您的應用程式的流程。

* 如果您不想離開主應用程式頁面，在驗證期間的使用者，建議使用的快顯的方法。 因為驗證重新導向發生在快顯視窗中，會保留主要的應用程式的狀態。

* 有某些情況下，您可能需要使用重新導向方法。 如果您的應用程式的使用者有瀏覽器的條件約束或原則會在停用快顯視窗，您可以使用重新導向方法。 使用 Internet Explorer 瀏覽器的重新導向方法，因為某些[已知問題與 Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)處理快顯視窗時。

## <a name="sign-in-with-a-pop-up-window"></a>使用快顯視窗登入

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

### <a name="angular"></a>Angular

MSAL Angular 的包裝函式可讓您保護您的應用程式中的特定路由，只要加入`MsalGuard`路由定義。 這個防護會叫用的方法來存取該路由時，登入。

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

快顯視窗中體驗，請啟用`popUp`config 選項。 您也可以傳遞需要同意，如下所示的範圍：

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["user.read", "user.write"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>使用重新導向登入

### <a name="javascript"></a>JavaScript

重新導向方法不會傳回因為巡覽離開主應用程式的承諾。 若要處理及存取傳回的權杖，您必須先呼叫重新導向方法註冊成功和錯誤回呼。

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Angular

此處的程式碼相當於上面所述的快顯視窗區段使用登入 下。 預設流程是重新導向。

> [!NOTE]
> 識別碼權杖不包含已獲得同意的範圍，且只代表已驗證的使用者。 您將在下一個步驟中取得的存取權杖中，會傳回已獲得同意的範圍。

## <a name="sign-out"></a>登出

MSAL 程式庫提供`logout`方法，將會清除瀏覽器的儲存體中的快取，並傳送至 Azure AD 的登出要求。 之後登出，它會重新導向回到應用程式起始頁的預設值。

您可以設定的 URI，它應該重新導向號之後登出設定`postLogoutRedirectUri`。 此 URI 也應該註冊為您的應用程式註冊中登出 URI。

### <a name="javascript"></a>JavaScript

```javascript
const config = {

    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            })]
         })

// In app.component.ts
this.authService.logout();
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [取得權杖的應用程式](scenario-spa-acquire-token.md)
