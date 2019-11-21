---
title: Use Microsoft identity platform to sign in users on browser-less devices | Azure
description: Build embedded and browser-less authentication flows using the device authorization grant.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c948c59a90e0db17b4704188221cfc3c3d82310
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207609"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft identity platform and the OAuth 2.0 device authorization grant flow

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

The Microsoft identity platform supports the [device authorization grant](https://tools.ietf.org/html/rfc8628), which allows users to sign in to input-constrained devices such as a smart TV, IoT device, or printer.  若要啟用此流程，裝置會讓使用者在其他裝置的瀏覽器中瀏覽網頁，以執行登入程序。  使用者登入後，裝置可取得所需的存取權杖和重新整理權杖。  

This article describes how to program directly against the protocol in your application.  When possible, we recommend you use the supported Microsoft Authentication Libraries (MSAL) instead to [acquire tokens and call secured web APIs](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Also take a look at the [sample apps that use MSAL](sample-v2-code.md).

> [!NOTE]
> The Microsoft identity platform endpoint doesn't support all Azure Active Directory scenarios and features. To determine whether you should use the Microsoft identity platform endpoint, read about [Microsoft identity platform limitations](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>通訊協定圖表

整個用戶端認證流程與下圖相似。 我們會在本文稍後說明每個步驟。

![裝置程式碼流程](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>裝置授權要求

The client must first check with the authentication server for a device and user code that's used to initiate authentication. 用戶端從 `/devicecode` 端點收集此要求。 在此要求中，用戶端也應一併附上必須向使用者索取的權限。 自發出要求算起，使用者只有 15 分鐘可以登入 (`expires_in` 的一般值)，因此請在使用者已準備好登入的情況下，才發出要求。

> [!TIP]
> 嘗試在 Postman 中執行這項要求！
> [![Try running this request in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| 參數 | 條件 | 描述 |
| --- | --- | --- |
| `tenant` | 必要項 | Can be /common, /consumers, or /organizations.  It can also be the directory tenant that you want to request permission from in GUID or friendly name format.  |
| `client_id` | 必要項 | The **Application (client) ID** that the [Azure portal – App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) experience assigned to your app. |
| `scope` | 建議 | 您要使用者同意的 [範圍](v2-permissions-and-consent.md) 空格分隔清單。  |

### <a name="device-authorization-response"></a>裝置授權回應

成功的回應會是一個 JSON 物件，其中包含允許使用者登入的所需資訊。  

| 參數 | 格式 | 描述 |
| ---              | --- | --- |
|`device_code`     | String | 長字串，可用於驗證用戶端與授權伺服器之間的工作階段。 The client uses this parameter to request the access token from the authorization server. |
|`user_code`       | String | A short string shown to the user that's used to identify the session on a secondary device.|
|`verification_uri`| URI | 為了執行登入程序，使用者應使用 `user_code` 查看的 URI。 |
|`expires_in`      | int | `device_code` 和 `user_code` 到期之前的秒數。 |
|`interval`        | int | 用戶端在輪詢要求之間應等待的秒數。 |
| `message`        | String | 人類看得懂的字串，包含使用者說明。 在 `?mkt=xx-XX` 形式的要求中加入  **查詢參數**、填寫適當的語言文化代碼，即可進行當地語系化。 |

> [!NOTE]
> The `verification_uri_complete` response field is not included or supported at this time.  We mention this because if you read the [standard](https://tools.ietf.org/html/rfc8628) you see that `verification_uri_complete` is listed as an optional part of the device code flow standard.

## <a name="authenticating-the-user"></a>驗證使用者

After receiving the `user_code` and `verification_uri`, the client displays these to the user, instructing them to sign in using their mobile phone or PC browser.

If the user authenticates with a personal account (on /common or /consumers), they will be asked to sign in again in order to transfer authentication state to the device.  They will also be asked to provide consent, to ensure they are aware of the permissions being granted.  This does not apply to work or school accounts used to authenticate. 

使用者在 `verification_uri`進行驗證時，用戶端應使用 `device_code` 輪詢 `/token` 端點，以取得要求的權杖。

``` 
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| 參數 | 必要項 | 描述|
| -------- | -------- | ---------- |
| `tenant`  | 必要項 | The same tenant or tenant alias used in the initial request. | 
| `grant_type` | 必要項 | 必須是 `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | 必要項 | 必須符合初始要求中使用的 `client_id`。 |
| `device_code`| 必要項 | 裝置授權要求傳回的 `device_code`。  |

### <a name="expected-errors"></a>預期的錯誤

The device code flow is a polling protocol so your client must expect to receive errors before the user has finished authenticating.  

| Error | 描述 | 用戶端動作 |
| ------ | ----------- | -------------|
| `authorization_pending` | The user hasn't finished authenticating, but hasn't canceled the flow. | 經過至少 `interval` 秒後，重複要求流程。 |
| `authorization_declined` | 終端使用者拒絕了授權要求。| 停止輪詢，並還原到未驗證的狀態。  |
| `bad_verification_code`| The `device_code` sent to the `/token` endpoint wasn't recognized. | 確認用戶端是否在要求中傳送正確的 `device_code`。 |
| `expired_token` | 已經過了至少 `expires_in` 秒，`device_code` 也無法再進行驗證。 | Stop polling and revert to an unauthenticated state. |   

### <a name="successful-authentication-response"></a>成功驗證回應

成功的權杖回應如下：

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| 參數 | 格式 | 描述 |
| --------- | ------ | ----------- |
| `token_type` | String| 一律是「Bearer」。 |
| `scope` | 空格分隔的字串 | 如果傳回了存取權杖，則會列出存取權杖的有效範圍。 |
| `expires_in`| int | 包含的存取權杖須經過多久 (秒數) 才會生效。 |
| `access_token`| 不透明字串 | 針對已要求的[範圍](v2-permissions-and-consent.md)發出。  |
| `id_token`   | JWT | 原始 `scope` 參數包含 `openid` 範圍時發出。  |
| `refresh_token` | 不透明字串 | 原始 `scope` 參數包含 `offline_access` 時發出。  |

You can use the refresh token to acquire new access tokens and refresh tokens using the same flow documented in the [OAuth Code flow documentation](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
