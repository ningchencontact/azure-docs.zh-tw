---
title: 要求存取權杖-Azure Active Directory B2C |Microsoft Docs
description: 了解如何從 Azure Active Directory B2C 要求存取權杖。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1a545f1e0fd1360d9147280454fb8b75bf216152
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66507381"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>要求存取權杖中 Azure Active Directory B2C

*存取權杖*包含您可以授與的權限識別到您的 Api 使用 Azure Active Directory (Azure AD) B2C 中的宣告。 在呼叫資源伺服器，必須出現在 HTTP 要求存取權杖。 表示存取權杖**access_token**從 Azure AD B2C 的回應中。 

這篇文章會示範如何要求存取權杖的 web 應用程式和 web API。 如需有關在 Azure AD B2C 的權杖的詳細資訊，請參閱[語彙基元中 Azure Active Directory B2C 概觀](active-directory-b2c-reference-tokens.md)。

> [!NOTE]
> **Azure AD B2C 不支援 Web API 鏈結 (代理者)。** -許多架構包含 web API 需要呼叫另一個下游 web API，Azure AD B2C 所保護。 此案例常見於有 web API 後端，再呼叫另一個服務的用戶端。 使用 OAuth 2.0 JWT 持有人認證授與可支援此鏈結的 Web API 案例，亦稱為「代理者流程」。 不過，Azure AD B2C 目前未實作代理者流程。

## <a name="prerequisites"></a>必要條件

- [建立使用者流程](tutorial-create-user-flows.md)，讓使用者註冊並登入您的應用程式。
- 如果您還沒有這麼做，請[新增 web API 應用程式，您的 Azure Active Directory B2C 租用戶](add-web-application.md)。

## <a name="scopes"></a>範圍

範圍會提供一個方法來管理受保護資源的權限。 用戶端應用程式要求存取權杖時，必須指定在所需的權限**範圍**要求參數。 例如，若要指定**範圍值**的`read`api 具有**應用程式識別碼 URI**的`https://contoso.onmicrosoft.com/api`，則範圍會是`https://contoso.onmicrosoft.com/api/read`。

Web API 可使用範圍來實作以範圍為基礎的存取控制。 例如，Web API 的使用者可以同時具有讀取和寫入權限，Web API 的使用者也可能只具有讀取權限。 若要取得相同的要求中的多個權限，您可以新增多個項目中的單一**範圍**要求，並以空格分隔的參數。

下列範例會顯示已解碼的 URL 中的範圍：

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

下列範例會示範編碼在 URL 中的範圍：

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

如果您要求比授與用戶端應用程式的更多範圍，如果至少一個權限會授與，也會成功呼叫。 **Scp**中產生的存取權杖的宣告會填入已成功授與的權限。 OpenID Connect 標準會指定數個特殊的範圍值。 下列範圍代表存取使用者的設定檔的權限：

- **openid** -要求的識別碼權杖。
- **offline_access** -要求重新整理權杖 using[授權碼流程](active-directory-b2c-reference-oauth-code.md)。

如果**response_type**中的參數`/authorize`要求包含`token`，則**範圍**參數必須包含至少一個資源範圍以外的其他`openid`並`offline_access`，將會被授與。 否則，`/authorize`要求會失敗。

## <a name="request-a-token"></a>要求權杖

若要要求存取權杖，您需要授權碼。 以下是範例要求`/authorize`端點授權碼。 用於存取權杖不支援自訂網域。 使用您的租用戶 name.onmicrosoft.com 網域要求 URL 中。

在下列範例中，您可以取代下列值：

- `<tenant-name>` - Azure AD B2C 租用戶的名稱。
- `<policy-name>` - 您的自訂原則或使用者流程名稱。
- `<application-ID>` 您註冊，以便支援使用者流程的 web 應用程式-應用程式識別碼。
- `<redirect-uri>` - 您註冊用戶端應用程式時所輸入的 [重新導向 URI]  。

```
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://tenant-name>.onmicrosoft.com/api/read
&response_type=code 
```

使用授權碼回應應該類似此範例：

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

已成功收到授權碼之後, 您可以使用它來要求存取權杖：

```
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: https://<tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

您應該會看到類似下列的回應：

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

當使用 https://jwt.ms 若要檢查傳回的存取權杖，您應該會看到類似下列的範例：

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>後續步驟

- 深入了解如何[設定 Azure AD B2C 中的權杖](configure-tokens.md)
