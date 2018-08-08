---
title: 使用 OAuth 2.0 向 Azure AD 進行驗證並取得 JWT 權杖
description: 程式碼範例會說明如何使用 OAuth 2.0 向 Azure Active Directory 進行驗證，以存取組織中受保護的 Web 應用程式和 Web API。
services: active-directory
author: rloutlaw
manager: angerobe
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: routlaw
ms.custom: aaddev
ms.openlocfilehash: cbc86eb6d13034fb3154ed8e9d021064f1d15ece
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2018
ms.locfileid: "39266695"
---
# <a name="request-an-access-token-using-oauth-20-to-access-web-apis-and-applications-secured-by-azure-active-directory"></a>使用 OAuth 2.0 來要求存取權杖，以存取受 Azure Active Directory 保護的 Web API 和應用程式

本文會說明如何取得 JSON Web 權杖 (JWT) 以存取受 Azure AD 保護的資源。 同時假設您已從使用者所授與的權限或是透過[服務主體](/azure/active-directory/develop/active-directory-application-objects)獲得[授權權杖](/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)。

## <a name="build-the-request"></a>建立要求

使用下列 `POST` HTTP 要求來取得 JWT，以存取受 Azure AD 保護的特定應用程式或 API。

```http
POST https://{tenant}/oauth2/v2.0/token?client_id={client-id}
&scope={scope}
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh   
```

### <a name="request-headers"></a>要求標頭

以下是必要標頭：

|要求標頭|說明|  
|--------------------|-----------------|  
| Host： | https://login.microsoftonline.com |
| *Content-Type:*| application/x-www-form-urlencoded |
 

### <a name="uri-parameters"></a>URI 參數

| 參數     |                       | 說明                                                                                                                                                                                                                                                                                                                                                                                                                                |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| tenant        | 必要              | 要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。 允許的值為 `common`、`organizations`、`consumers` 及租用戶識別碼。 如需更多詳細資訊，請參閱 [通訊協定基本概念](active-directory-v2-protocols.md#endpoints)。                                                                                                                                                   |
| client_id     | 必要              | 註冊入口網站 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) 指派給應用程式的應用程式識別碼。                                                                                                                                                                                                                             |
| grant_type    | 必要              | 必須是授權碼流程的 `authorization_code` 。                                                                                                                                                                                                                                                                                                                                                                            |
| scope         | 必要              | 範圍的空格分隔清單。 在此階段中要求的範圍必須相當於或為 `/authorize` 呼叫中的範圍子集。 如果這個要求中指定的範圍遍及多個資源伺服器，v2.0 端點就會傳回第一個範圍中所指定資源的權杖。 如需範圍的詳盡說明，請參閱 [權限、同意和範圍](active-directory-v2-scopes.md)。 |
| code          | 必要              | 您在 `/authorize` 的呼叫中取得的 authorization_code                                                                                                                                                                                                                                                                                                                                                                |
| redirect_uri  | 必要              | 用來取得 authorization_code 的相同 redirect_uri 值。                                                                                                                                                                                                                                                                                                                                                             |
| client_secret | Web Apps 所需 | 您在應用程式註冊入口網站中為應用程式建立的應用程式密碼。 請勿用於原生應用程式，原因是裝置無法穩當地儲存 client_secret。 Web Apps 和 Web API 都需要應用程式密碼，其能夠將 client_secret 安全地儲存在伺服器端。  用戶端密碼必須在傳送之前先進行 URL 編碼。                                                                                 |
| code_verifier | 選用              | 用來取得 authorization_code 的相同 code_verifier。 如果在授權碼授與要求中已使用 PKCE，則為必要參數。 如需詳細資訊，請參閱 [PKCE RFC](https://tools.ietf.org/html/rfc7636) \(英文\)                                                                                                                                                                                                                                                                                             |
## <a name="handle-the-response"></a>處理回應

成功的權杖回應會包含 JWT 權杖，而且看起來會像下面這樣：

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| 參數     | 說明                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| access_token  | 所要求的存取權杖。 應用程式可以使用這個權杖驗證受保護的資源，例如 Web API。                                                                                                                                                                                                                                                                                                                                    |
| token_type    | 表示權杖類型值。 Azure AD 唯一支援的類型是 Bearer。                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | 存取權杖的有效期 (以秒為單位)。                                                                                                                                                                                                                                                                                                                                                                                                       |
| scope         | access_token 有效的範圍。                                                                                                                                                                                                                                                                                                                                                                                                         |
| refresh_token | OAuth 2.0 重新整理權杖。 應用程式可以使用這個權杖，在目前的存取權杖過期之後，取得其他的存取權杖。 Refresh_token 的有效期很長，而且可以用來延長保留資源存取權的時間。 如需詳細資訊，請參閱 [v2.0 權杖參考](active-directory-v2-tokens.md)。 <br> **注意：** 只在要求 `offline_access` 範圍時提供。                                               |
| id_token      | 不帶正負號的 JSON Web Token (JWT)。 應用程式可以將這個權杖的區段解碼，來要求已登入使用者的相關資訊。 應用程式可以快取並顯示值，但不應依賴這些值來取得任何授權或安全性界限。 如需有關 id_token 的詳細資訊，請參閱 [v2.0 端點權杖參考](active-directory-v2-tokens.md)。 <br> **注意：** 只在要求 `openid` 範圍時提供。 |



