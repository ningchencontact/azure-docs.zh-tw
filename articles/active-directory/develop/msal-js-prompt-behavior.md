---
title: 提示行為中的互動式要求 (Microsoft Authentication Library for JavaScript) |Azure
description: 了解如何自訂中使用的 Microsoft Authentication Library for JavaScript (MSAL.js) 的互動式呼叫提示的行為。
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
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
ms.openlocfilehash: dd0d736345f312f1a1d6f8f029b41429a3e5f0a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544277"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>MSAL.js 互動式要求中的提示行為

當使用者已建立作用中的 Azure AD 與多個使用者帳戶的工作階段時，Azure AD 登入頁面會依預設提示使用者選取帳戶，才能繼續登入。 使用者看不到如果只有單一驗證的工作階段與 Azure AD 會遇到帳戶選取項目。

MSAL.js 程式庫 （從 v0.2.4） 不會傳送互動式要求期間的提示參數 (`loginRedirect`， `loginPopup`，`acquireTokenRedirect`和`acquireTokenPopup`)，並因此不會強制使用任何提示的行為。 使用無訊息的權杖要求`acquireTokenSilent`方法，MSAL.js 傳遞提示的參數設定為`none`。

根據您的應用程式案例，您可以控制中的要求參數的提示參數傳遞給方法的互動式要求所設定的提示行為。 例如，如果您想要叫用的帳戶選取項目體驗：

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


向 Azure AD 進行驗證時，可以傳遞下列提示的值：

**登入：** 這個值會強制使用者在驗證要求上輸入認證。

**select_account:** 這個值會為使用者提供與工作階段中列出的所有帳戶的帳戶選取項目體驗。

**同意：** 這個值會叫用可讓使用者應用程式的權限授與 OAuth 同意對話方塊。

**None:** 此值可確保使用者不會看到任何互動式提示。 建議您未將此值傳遞至 MSAL.js 中的互動式方法，因為它會對非預期的行為。 請改用`acquireTokenSilent`達到無訊息呼叫的方法。

## <a name="next-steps"></a>後續步驟

深入了解`prompt`中的參數[OAuth 2.0 隱含授權](v2-oauth2-implicit-grant-flow.md)哪些 MSAL.js 程式庫會使用的通訊協定。
