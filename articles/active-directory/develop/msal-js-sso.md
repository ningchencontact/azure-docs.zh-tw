---
title: 單一登入 (Microsoft Authentication Library for JavaScript) |Azure
description: 了解如何建置單一登入使用體驗 Microsoft Authentication Library for JavaScript (MSAL.js)。
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a0fb0731b7ac46210294e3766b33bd6a239dcc2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075846"
---
# <a name="single-sign-on-with-msaljs"></a>單一登入與 MSAL.js

單一登入 (SSO) 可讓使用者輸入其認證一次登入，並建立可重複使用多個應用程式而不需要重新驗證工作階段。 這對使用者提供順暢的體驗，並減少重複的提示認證。

Azure AD 會提供應用程式的 SSO 功能，藉由設定工作階段 cookie，當使用者進行驗證的第一次。 MSAL.js 程式庫可讓應用程式利用這個方法有幾個方法。

## <a name="sso-between-browser-tabs"></a>瀏覽器索引標籤之間的 SSO

當您的應用程式是在多個索引標籤中開啟，您先登入索引標籤上的使用者，使用者已也登入的其他索引標籤而不提示。 MSAL.js 會快取的使用者在瀏覽器中的 ID 語彙基元`localStorage`並將使用者登入其他開啟的索引標籤上的應用程式。

根據預設，使用 MSAL.js`sessionStorage`表示不允許在索引標籤之間共用的工作階段。 若要取得索引標籤之間的 SSO，請務必設定`cacheLocation`中以 MSAL.js `localStorage` ，如下所示。

```javascript
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>應用程式之間的 SSO

當使用者驗證時，工作階段 cookie 會設定瀏覽器中的 Azure AD 網域。 MSAL.js 依賴此工作階段 cookie，以不同的應用程式之間為使用者提供 SSO。 識別碼權杖和存取權杖的瀏覽器儲存體，每個應用程式網域中的使用者，也會快取 MSAL.js。 如此一來，不同的情況下會有所不同的 SSO 行為：  

### <a name="applications-on-the-same-domain"></a>在相同的網域上的應用程式

當應用程式裝載於相同的網域時，使用者可以一次登入應用程式，並再獲得驗證而不提示的其他應用程式。 MSAL.js 運用在網域上的使用者提供 SSO 快取的權杖。

### <a name="applications-on-different-domain"></a>在不同的網域上的應用程式

網域 b 中的 MSAL.js 時應用程式裝載於不同的網域時，無法存取網域 A 上快取的權杖

這表示當使用者登入網域的瀏覽至網域 B 上的應用程式，它們會被重新導向或與 Azure AD 頁面出現提示。 Azure AD 仍然會有使用者工作階段 cookie，因為它會將使用者登入，他們不需要重新輸入認證。 如果使用者有多個使用者帳戶與 Azure AD 的工作階段中，您就會提示使用者選擇相關的帳戶來登入。

### <a name="automatically-select-account-on-azure-ad"></a>自動選取 Azure AD 中的 帳戶

在某些情況下，應用程式可以存取使用者的驗證內容，而且想要避免 Azure AD 帳戶選取項目提示字元，當多個帳戶登入時。  這可以完成幾個不同的方式：

**使用工作階段識別碼 (SID)**

工作階段識別碼會[選擇性宣告](active-directory-optional-claims.md)，可以設定在識別碼權杖中。 這個宣告可讓應用程式，來識別使用者的 Azure AD 工作階段無關的使用者帳戶名稱或使用者名稱。 您可以傳入的要求參數中的 SID`acquireTokenSilent`呼叫。 這可讓 Azure AD，以略過選取的帳戶。 SID 是繫結至工作階段 cookie，而且將不會跨瀏覽器的內容。

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> SID 僅搭配所做的無訊息的驗證要求`acquireTokenSilent`MSAL.js 中呼叫。
您可以找到您的應用程式資訊清單中，設定選擇性宣告[此處](active-directory-optional-claims.md)。

**使用登入提示**

如果您沒有宣告設定，或需要略過帳戶選取項目提示字元中，互動式驗證呼叫中的 SID，則可以藉由提供`login_hint`中的要求參數，並選擇性地`domain_hint`做為`extraQueryParameters`MSAL.js 中互動式方法 (`loginPopup`， `loginRedirect`，`acquireTokenPopup`和`acquireTokenRedirect`)。 例如︰

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

您可以取得 login_hint 和 domain_hint 的值，藉由讀取使用者的識別碼權杖中傳回的宣告。

* **loginHint**應該設定為`preferred_username`識別碼權杖中宣告。

* **domain_hint**只有需要時使用 /common 來傳遞授權單位。 網域提示是由租用戶 ID(tid) 決定。  如果`tid`識別碼權杖中的宣告是`9188040d-6c67-4c5b-b112-36a304b66dad`是取用者。 否則，它是組織。

讀取[此處](v2-oauth2-implicit-grant-flow.md)如需有關登入提示和網域提示的值。

> [!Note]
> 您無法將 SID 和 login_hint 傳遞一次。 這會導致錯誤回應。

## <a name="sso-without-msaljs-login"></a>沒有 MSAL.js 登入的 SSO

根據設計，MSAL.js 需要登入方法呼叫以建立使用者內容，才能取得 api 的權杖。 因為互動式登入方法，使用者會看到提示。

某些情況下，在其中應用程式可以存取已驗證的使用者內容，或透過驗證識別碼權杖在另一個應用程式中起始，而且想要利用 SSO 來取得權杖，而不需要透過 MSAL.js 第一個登。

這個範例是：使用者已登入上層 web 應用程式裝載為附加元件或外掛程式執行的其他 JavaScript 應用程式。

在此案例中的 SSO 體驗可以達成，如下所示：

傳遞`sid`的話 (或`login_hint`並選擇性地`domain_hint`) 做為要求參數 MSAL.js`acquireTokenSilent`呼叫，如下所示：

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>在 ADAL.js MSAL.js 更新的 SSO

MSAL.js 將 ADAL.js 與 Azure AD 驗證案例的功能同位。 為了方便移轉從 ADAL.js MSAL.js 並避免提示您再次登入的使用者，程式庫會讀取 ADAL.js 快取中，代表使用者的工作階段的識別碼權杖，並順暢地登入 MSAL.js 中的使用者。  

若要利用單一登入 (SSO) 行為從 ADAL.js 更新時，您必須確定程式庫都使用`localStorage`快取權杖。 設定`cacheLocation`至`localStorage`同時 MSAL.js 和 ADAL.js 設定在初始化時，如下所示：


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

此設定之後，MSAL.js 將能夠讀取的已驗證的使用者在 ADAL.js 中快取的狀態，然後使用它來提供在 MSAL.js 的 SSO。

## <a name="next-steps"></a>後續步驟

深入了解[單一登入工作階段和權杖存留期](active-directory-configurable-token-lifetimes.md)在 Azure AD 中的值。
