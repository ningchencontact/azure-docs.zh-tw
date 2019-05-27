---
title: 了解如何使用 Microsoft 驗證程式庫 (MSAL) 來與 Azure AD B2C 整合
description: Microsoft 驗證程式庫 (MSAL) 可讓應用程式開發人員與 Azure AD B2C 整合並取得權杖，以便呼叫安全的 Web API。 這些 Web API 可以是 Microsoft Graph、其他 Microsoft API、第三方 Web API 或您自己的 Web API。
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6af78a593fb8fadb836d2dc4c02115d95a7f2712
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546023"
---
# <a name="integrate-microsoft-authentication-library-msal-with-azure-active-directory-b2c"></a>整合 Microsoft 驗證程式庫 (MSAL) 與 Azure Active Directory B2C

Microsoft 驗證程式庫 (MSAL) 可讓應用程式開發人員使用 [Azure Active Directory (Azure AD) B2C](https://docs.microsoft.com/azure/active-directory-b2c/)，以社交和本機身分識別驗證使用者。 Azure Active Directory (Azure AD) B2C 是一項身分識別管理服務，可讓您自訂和控制客戶在使用您的應用程式時如何註冊、登入及管理其設定檔。

Azure Active Directory (Azure AD) B2C 也可讓您為您的應用程式設定品牌及自訂其使用者介面 (UI)，進而為您的客戶提供順暢的體驗。

本教學課程會示範如何使用 Microsoft 驗證程式庫 (MSAL) 來與 Azure Active Directory (Azure AD) B2C 整合。


## <a name="prerequisites"></a>必要條件

如果您尚未建立自己的 [Azure AD B2C 租用戶](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)，請立即建立一個。 您可以使用現有的 Azure AD B2C 租用戶。 

## <a name="javascript"></a>Javascript

下列步驟示範單頁應用程式如何使用 Azure AD B2C 進行使用者註冊、登入及呼叫受保護的 Web API。

### <a name="step-1-register-your-application"></a>步驟 1：註冊您的應用程式

若要實作驗證，首先需要註冊您的應用程式。 若要註冊您的應用程式，請遵循[註冊您的應用程式](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c)中的詳細步驟。

### <a name="steps-2-download-applications"></a>步驟 2：下載應用程式

下載 zip 檔案，或從 GitHub 複製範例。
>git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git

### <a name="steps-3-authentication"></a>步驟 3：Authentication

1. 開啟範例中的 index.html 檔案。

2. 使用您稍早在註冊應用程式時所記錄的應用程式識別碼與金鑰來設定範例。 變更下列幾行程式碼，做法是將其中的值取代為您的目錄和 API 名稱：

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>",
        b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/hello/demo.read"],
        webApi: 'http://localhost:5000/hello',
  };

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

本教學課程中使用的[使用者流程](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies)名稱是 B2C_1_signupsignin1。 如果您使用不同的使用者流程名稱，請在 authority 值中使用您的使用者流程名稱。


### <a name="configure-application-to-use-b2clogincom"></a>將應用程式設定為使用 `b2clogin.com`

當您為 Azure Active Directory (Azure AD) B2C 應用程式中的註冊和登入設定識別提供者時，您可以使用 `b2clogin.com` 取代 `login.microsoftonline.com` 作為重新導向 url。

**`b2clogin.com`** 亦即 
`https://your-tenant-name.b2clogin.com/your-tenant-guid` 用於下列項目：

- Microsoft 服務在 Cookie 標頭中所耗用的空間會縮小。
- 您的 URL 不再包含對 Microsoft 的參考。 例如，您的 Azure AD B2C 應用程式可能會參考 login.microsoftonline.com


 若要使用 'b2clogin.com'，您需要更新您應用程式的組態。  

1. 更新 ValidateAuthority：將 **validateAuthority** 屬性設定為 `false`。 當 **validateAuthority** 設定為 false 時，可允許重新導向到 b2clogin.com。

下列範例示範如何設定該屬性：
```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> 您 Azure AD B2C 應用程式可能有數個地方 (例如使用者流程參考和權杖端點) 都會參考 login.microsoftonline.com。 請確定您的授權端點、權杖端點及簽發者都已更新成使用 your-tenant-name.b2clogin.com。

請遵循此 [MSAL JS 範例](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c)中有關如何使用適用於 JavaScript 的 Microsoft 驗證程式庫預覽版 (msal.js) 來取得存取權杖，以及呼叫受 Azure AD B2C 保護的 API。

## <a name="next-steps"></a>後續步驟

深入了解：

- [自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [使用者介面自訂](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)