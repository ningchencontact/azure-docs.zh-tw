---
title: 初始化用戶端應用程式 (Microsoft Authentication Library for JavaScript) |Azure
description: 深入了解初始化用戶端應用程式使用的 Microsoft Authentication Library for JavaScript (MSAL.js)。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7990566ca9cd93e79b8356cfd15fda03a7469695
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138308"
---
# <a name="initialize-client-applications-using-msaljs"></a>初始化使用 MSAL.js 的用戶端應用程式
本文會說明初始化 Microsoft Authentication Library for JavaScript (MSAL.js) 與使用者代理程式應用程式的執行個體。 使用者代理程式應用程式是一種用戶端程式碼執行在例如網頁瀏覽器使用者代理程式所在的公用用戶端應用程式。 由於瀏覽器內容是可公開存取，這些用戶端就不會儲存祕密。 若要了解用戶端應用程式類型與應用程式組態選項的詳細資訊，請閱讀[概觀](msal-client-applications.md)。

## <a name="prerequisites"></a>必要條件
然後再初始化應用程式，您必須先[使用 Azure 入口網站註冊](scenario-spa-app-registration.md)，讓您的應用程式可以與 Microsoft 身分識別平台整合。 註冊之後，您可能需要下列資訊 （這可以在 Azure 入口網站中找到）：

- 用戶端識別碼 （字串，表示您的應用程式的 GUID）
- （具名執行個體） 的身分識別提供者 URL 和您的應用程式的登入對象。 這兩個參數統稱為授權單位。
- 如果您正在撰寫的特定業務應用程式僅供您的組織 （也命名單一租用戶的應用程式） 租用戶識別碼。
- 針對 web 應用程式，您必須也設定 redirectUri 身分識別提供者會傳回您的應用程式，利用安全性權杖的位置。

## <a name="initializing-applications"></a>初始化應用程式

您可以在純文字 JavaScript/Typescript 應用程式，如下所示使用 MSAL.js。 具現化，以初始化 MSAL 驗證內容`UserAgentApplication`組態物件。 最小的必要的組態，來初始化 MSAL.js 是您應該從應用程式註冊入口網站取得應用程式的 clientID。

針對使用的驗證方法將流程重新導向 (`loginRedirect`並`acquireTokenRedirect`)，您必須明確註冊成功，或透過錯誤的回呼`handleRedirectCallback()`方法。 因為重新導向流程不會傳回承諾的快顯的體驗的方法一樣，會需要此項目。

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

MSAL.js 設計為具有單一執行個體和設定`UserAgentApplication`來代表單一驗證內容。 如有衝突的快取項目和行為會在瀏覽器中，不建議您使用多個執行個體。

## <a name="configuration-options"></a>組態選項

MSAL.js 具有組態，如下所示的物件提供的可設定的選項可用來建立的執行個體群組`UserAgentApplication`。

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

以下是總一組可設定的組態物件中目前支援的選項：

- **clientID**:必要。 您的應用程式的 clientID，您應該從取得此應用程式註冊入口網站。

- **授權單位**:選用。 URL，表示 MSAL 可以要求權杖的目錄。 預設值為：`https://login.microsoftonline.com/common`。
    * 在 Azure AD 中，它是 https://&lt;執行個體&gt;/&lt;觀眾&gt;，其中&lt;執行個體&gt;是身分識別提供者網域 (例如`https://login.microsoftonline.com`) 和&lt;觀眾&gt;是代表登入對象的識別碼。 這可以是下列值：
        * `https://login.microsoftonline.com/<tenant>`-租用戶是租用戶，例如 contoso.onmicrosoft.com 或 GUID，表示相關聯的網域`TenantID`屬性只能用來登入使用者的特定組織的目錄。
        * `https://login.microsoftonline.com/common`-用來登入公司及學校帳戶或 Microsoft 個人帳戶的使用者。
        * `https://login.microsoftonline.com/organizations/`-用來登入的使用者工作和學校帳戶。
        * `https://login.microsoftonline.com/consumers/` -用來登入的使用者只有個人的 Microsoft 帳戶 （即時）。
    * 在 Azure AD B2C 中，它是表單`https://<instance>/tfp/<tenant>/<policyName>/`、 執行個體所在的 Azure AD B2C 網域中，租用戶是 Azure AD B2C 租用戶的名稱、 policyName 是要套用的 B2C 原則的名稱。


- **validateAuthority**:選用。  驗證權杖的簽發者。 預設值為 `true`。 對於 B2C 應用程式，因為授權值已知，而且可能會不同，每個原則，授權單位驗證將無法運作且已設為`false`。

- **redirectUri**:選用。  應用程式的重新導向 URI，您的應用程式可在此傳送及接收驗證回應。 它必須與您在入口網站中註冊的其中一個重新導向 URI 完全相符，只是它必須是採用 URL 編碼。 預設為 `window.location.href`。

- **postLogoutRedirectUri**:選用。  若要將使用者重新導向`postLogoutRedirectUri`登出後。預設值為 `redirectUri`。

- **navigateToLoginRequestUrl**:選用。 若要關閉 預設瀏覽至登入之後起始頁的能力。 預設值為 true。 這僅用於重新導向流程。

- **cacheLocation**:選用。  將瀏覽器儲存體設定為`localStorage`或`sessionStorage`。 預設值為 `sessionStorage`。

- **storeAuthStateInCookie**:選用。  這個旗標中引進了 MSAL.js v0.2.2 為修正[迴圈的驗證問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones)上 Microsoft Internet Explorer 和 Microsoft Edge。 啟用旗標`storeAuthStateInCookie`true 會充分利用此修正。 當啟用此選項時，MSAL.js 將儲存在瀏覽器 cookie 的驗證流程的驗證所需的驗證要求狀態。 根據預設，這個旗標設定為`false`。

- **記錄器**:選用。  回呼執行個體，可供使用，並將記錄發行以自訂方式開發人員所使用的記錄器物件。 如需傳遞記錄器物件的詳細資訊，請參閱[記錄 msal.js](msal-logging.md)。

- **loadFrameTimeout**:選用。  一段時間的 Azure AD 之權杖的更新回應應該視為已逾時之前的毫秒數。預設值為 6 秒。

- **tokenRenewalOffsetSeconds**:選用。 的毫秒數的設定更新權杖到期之前所需的位移的視窗。 預設值是 300 毫秒。

這些只是適用於從 MSAL Angular 的包裝函式程式庫，向下傳遞：
- **unprotectedResources**:選用。  未受保護資源的 Uri 的陣列。 MSAL 會附加到具有下列 URI 的連出要求的語彙基元。 預設為 `null`。

- **protectedResourceMap**:選用。  這對應的資源給 MSAL 用於自動附加在 web API 呼叫中的存取權杖的範圍。 單一的存取權杖取得資源。 因此您可以將特定的資源路徑，如下所示: {"https://graph.microsoft.com/v1.0/me」，["user.read"]}，或做為資源的應用程式 URL: {"https://graph.microsoft.com/」，["user.read"，"mail.send"]}。 這是必要的 CORS 呼叫。 預設為 `null`。
