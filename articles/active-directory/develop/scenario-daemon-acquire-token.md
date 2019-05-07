---
title: 精靈應用程式呼叫 web Api （取得應用程式的權杖）-Microsoft 身分識別平台
description: 了解如何建置精靈應用程式呼叫 web Api （取得權杖）
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
ms.openlocfilehash: aa4f5dc7a5aceaf81f71eacd36d131471a57e5c0
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075366"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>呼叫 web Api-的精靈應用程式取得權杖

一旦建構機密用戶端應用程式時，您可以藉由呼叫來取得應用程式的權杖``AcquireTokenForClient``，傳遞範圍內，並強制或不重新整理語彙基元。

## <a name="scopes-to-request"></a>要求的範圍

要求用戶端認證流程是資源的名稱範圍後面`/.default`。 這個標記法會指示要使用的 Azure AD**應用程式層級權限**應用程式註冊期間以靜態方式宣告。 此外，如先前所示，這些 API 的權限必須被授與租用戶系統管理員

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

MSAL。Python，組態檔應該像下列程式碼片段：

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

使用用戶端認證的範圍應該一律是 resourceId +"/。 預設"

### <a name="case-of-v10-resources"></a>V1.0 資源的大小寫

> [!IMPORTANT]
> 接受 v1.0 存取權杖的資源要求存取權杖的 msal （v2.0 端點），Azure AD 會剖析要求的範圍從目標對象所採取的最後一個斜線之前的所有項目，並使用它做為資源識別碼。
> 因此如果，例如 Azure SQL (**https://database.windows.net**) 之資源所預期的結尾斜線的對象 (針對 Azure SQL: `https://database.windows.net/`)，您必須要求的範圍`https://database.windows.net//.default`（請注意兩個斜線）。 另請參閱 MSAL.NET 問題[#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747):省略資源 url 結尾的斜線，導致 sql 驗證失敗。

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

#### <a name="application-token-cache"></a>應用程式的權杖快取

MSAL.NET，在`AcquireTokenForClient`會使用**應用程式的權杖快取**（其他 AcquireTokenXX 方法會使用使用者的權杖快取） 不要呼叫`AcquireTokenSilent`再呼叫`AcquireTokenForClient`為`AcquireTokenSilent`使用**使用者**權杖快取。 `AcquireTokenForClient` 會檢查**應用程式**權杖快取本身，並更新它。

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

如果您不需要您選擇的語言的程式庫，您可能想要直接使用通訊協定：

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

### <a name="learn-more-about-the-protocol"></a>深入了解通訊協定

如需詳細資訊，請參閱 < 通訊協定文件：[Azure Active Directory v2.0 和 OAuth 2.0 用戶端認證流程](v2-oauth2-client-creds-grant-flow.md)。

## <a name="troubleshooting"></a>疑難排解

### <a name="did-you-use-the-resourcedefault-scope"></a>您未使用的資源 /。 預設範圍嗎？

如果您收到錯誤訊息，告知您，您使用無效的範圍，您可能沒有使用`resource/.default`範圍。

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>您是否忘記提供系統管理員同意？ 精靈應用程式需要的 ！

如果呼叫 API 時，收到錯誤**權限不足以完成作業**，租用戶系統管理員必須授與給應用程式的權限。 請參閱步驟 6 的註冊上述的用戶端應用程式。
您通常會看到，錯誤會像下列的錯誤描述：

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
> [精靈應用程式-呼叫 web API](scenario-daemon-call-api.md)