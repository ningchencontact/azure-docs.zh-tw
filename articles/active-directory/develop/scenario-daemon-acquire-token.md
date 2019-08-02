---
title: 呼叫 web Api 的 Daemon 應用程式 (取得應用程式的權杖)-Microsoft 身分識別平臺
description: 瞭解如何建立可呼叫 web Api (取得權杖) 的 daemon 應用程式
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a5f15aa5264c0abf87cb15f0468e8a3a924e0b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562354"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>呼叫 web Api 的 Daemon 應用程式-取得權杖

一旦建立機密用戶端應用程式, 您就可以藉由呼叫``AcquireTokenForClient``、傳遞範圍, 以及強制或不重新整理權杖來取得應用程式的權杖。

## <a name="scopes-to-request"></a>要求的範圍

要求用戶端認證流程的範圍是資源的名稱, 後面接著`/.default`。 此標記法會告訴 Azure AD 在應用程式註冊期間, 使用靜態宣告的**應用層級許可權**。 此外, 如先前所見, 這些 API 許可權必須由租使用者系統管理員授與

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

在 MSAL 中。Python, 設定檔看起來會像下列程式碼片段:

```Python
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "secret": "This is a sample only. You better NOT persist your password."
    "scope": ["https://graph.microsoft.com/.default"]
}
```

### <a name="java"></a>Java

```Java
public final static String KEYVAULT_DEFAULT_SCOPE = "https://vault.azure.net/.default";
```

### <a name="all"></a>全部

用於用戶端認證的範圍應該一律是 resourceId + "/.default"

### <a name="case-of-azure-ad-v10-resources"></a>Azure AD (v1.0) 資源的案例

> [!IMPORTANT]
> 針對 MSAL (Microsoft 身分識別平臺端點), 針對接受 v1.0 存取權杖的資源要求存取權杖, Azure AD 藉由在最後一個斜線之前取得所有專案, 並使用它作為資源識別碼, 從要求的範圍剖析所需的物件。
> 因此, 如果 azure sql ( **https://database.windows.net** ) 之類的資源預期以斜線結尾的物件 (適用于 azure sql: `https://database.windows.net/` ), 您將需要要求的範圍`https://database.windows.net//.default` (請注意雙斜線)。 另請參閱 MSAL.NET 問題[#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747):會省略資源 url 的尾端斜線, 這會導致 sql 驗證失敗。

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

### <a name="net"></a>.NET

```CSharp
using Microsoft.Identity.Client;

// With client credentials flows the scopes is ALWAYS of the shape "resource/.default", as the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application does not have sufficient permissions
    // - did you declare enough app permissions in during the app creation?
    // - did the tenant admin needs to grant permissions to the application.
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be of the form "https://resourceurl/.default"
    // Mitigation: change the scope to be as expected !
}
```

#### <a name="application-token-cache"></a>應用程式權杖快取

在 MSAL.NET 中`AcquireTokenForClient` , 使用**應用程式權杖**快取 (所有其他的 AcquireTokenXX 方法使用使用者 token 快取) `AcquireTokenSilent`在呼叫`AcquireTokenForClient`之前`AcquireTokenSilent`不會呼叫, 而是使用**使用者**權杖快取。 `AcquireTokenForClient`檢查**應用程式**權杖快取本身並加以更新。

### <a name="python"></a>Python

```Python
# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

### <a name="java"></a>Java

```Java
ClientCredentialParameters parameters = ClientCredentialParameters
        .builder(Collections.singleton(KEYVAULT_DEFAULT_SCOPE))
        .build();

CompletableFuture<AuthenticationResult> future = cca.acquireToken(parameters);

// You can complete the future in many different ways. Here we use .get() for simplicity
AuthenticationResult result = future.get();
```

### <a name="protocol"></a>Protocol

如果您還沒有適用于您所選語言的程式庫, 您可能會想要直接使用此通訊協定:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>第一種情況︰使用共用密碼的存取權杖要求

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>第二種情況︰使用憑證的存取權杖要求

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

### <a name="learn-more-about-the-protocol"></a>深入瞭解通訊協定

如需詳細資訊, 請參閱通訊協定檔:[Microsoft 身分識別平臺和 OAuth 2.0 用戶端認證流程](v2-oauth2-client-creds-grant-flow.md)。

## <a name="troubleshooting"></a>疑難排解

### <a name="did-you-use-the-resourcedefault-scope"></a>您使用的是 resource/. 預設範圍嗎？

如果您收到錯誤訊息, 告知您使用了不正確範圍, 表示您可能未使用`resource/.default`範圍。

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>您忘了提供系統管理員同意嗎？ Daemon 應用程式需要!

如果您在呼叫 API 的**許可權不足而無法完成**作業時收到錯誤, 則租使用者系統管理員必須授與許可權給應用程式。 請參閱上面的註冊用戶端應用程式的步驟6。
您通常會看到和錯誤, 如下列錯誤描述所示:

```JSon
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Daemon 應用程式-呼叫 Web API](scenario-daemon-call-api.md)