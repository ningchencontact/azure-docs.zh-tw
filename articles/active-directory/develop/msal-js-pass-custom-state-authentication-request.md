---
title: 將驗證要求 (Microsoft Authentication Library for JavaScript) 中的自訂狀態傳遞 |Azure
description: 了解如何使用 Microsoft Authentication Library for JavaScript (MSAL.js) 的驗證要求中傳遞自訂的狀態參數的值。
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
ms.openlocfilehash: f29d84838ddb11ac359d7a04dbce8e39dd05ac01
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66420494"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>將使用 MSAL.js 的驗證要求中的自訂狀態傳遞
*狀態*參數所定義的 OAuth 2.0 包含在驗證要求和也會傳回權杖回應，以防止跨網站偽造要求攻擊。 根據預設，隨機產生的 Microsoft Authentication Library for JavaScript (MSAL.js) 傳遞唯一*狀態*驗證要求中的參數值。

State 參數也可用來編碼的之前重新導向的應用程式的狀態資訊。 您可以傳遞應用程式，例如他們先前所在，做為此參數的輸入的頁面或檢視中的使用者的狀態。 MSAL.js 程式庫可讓您將您的自訂狀態傳遞做為中的狀態參數`Request`物件：

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
};
```

例如:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: “page_url”
}

myMSALObj.loginPopup(loginRequest);
```

傳入的狀態會附加至傳送要求時，由 MSAL.js 設定的唯一 GUID。 MSAL.js 傳回回應時，檢查狀態相符項目，並接著會傳回傳入的狀態中的自訂`Response`物件做為`accountState`。

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

若要進一步了解，請參閱[建置單一頁面應用程式 (SPA)](scenario-spa-overview.md)使用 MSAL.js。