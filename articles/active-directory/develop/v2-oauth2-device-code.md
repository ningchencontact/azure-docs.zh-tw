---
title: 使用 Azure AD v2.0 在無瀏覽器裝置上登入使用者 | Microsoft Docs
description: 使用裝置代碼授與，組建無瀏覽器的內嵌驗證流程。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 780eec4d-7ee1-48b7-b29f-cd0b8cb41ed3
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: e05bf723507ba12b577605d44ddb3aaef3b19efe
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079620"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-device-code-flow"></a>Azure Active Directory v2.0 和 OAuth 2.0 裝置代碼流程

Azure AD 支援[裝置代碼授與](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12)會允許使用者登入具有輸入限制的裝置，例如智慧型電視、物聯網裝置或印表機。  若要啟用此流程，裝置會讓使用者在其他裝置的瀏覽器中瀏覽網頁，以執行登入程序。  使用者登入後，裝置可取得所需的存取權杖和重新整理權杖。  

> [!Important] 
> 目前 v2.0 端點僅支援 Azure AD 租用戶的裝置流程，但不包括個人帳戶，  因此您必須使用租用戶端點或組織端點。  
>
> 獲邀使用 Azure AD 租用戶的個人帳戶將能使用裝置代碼授與，但僅限在租用戶相關背景中使用。

> [!NOTE]
> v2.0 端點並未支援所有的 Azure Active Directory 案例和功能。 若要判斷您是否應該使用 v2.0 端點，請參閱 [v2.0 限制](active-directory-v2-limitations.md)。
>

## <a name="protocol-diagram"></a>通訊協定圖表

整個用戶端認證流程與下圖相似。 我們會在本文稍後說明每個步驟。

![裝置程式碼流程](media/v2-oauth2-device-flow/v2-oauth-device-flow.png)

## <a name="device-authorization-request"></a>裝置授權要求

用戶端必須先向驗證伺服器查詢用於初始化驗證的裝置和使用者代碼。  用戶端從 `/devicecode` 端點收集此要求。 在此要求中，用戶端也應一併附上必須向使用者索取的權限。  自發出要求算起，使用者只有 15 分鐘可以登入 (`expires_in` 的一般值)，因此請在使用者已準備好登入的情況下，才發出要求。

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| 參數 | 條件 | 說明 |
| --- | --- | --- |
| tenant |必要 |您想要要求權限的目錄租用戶。 這可以採用 GUID 或易記名稱格式。  |
| client_id |必要 |[應用程式註冊入口網站](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)指派給您應用程式的「應用程式識別碼」。 |
| scope | 建議 | 您要使用者同意的 [範圍](v2-permissions-and-consent.md) 空格分隔清單。  |

### <a name="device-authorization-response"></a>裝置授權回應

成功的回應會是一個 JSON 物件，其中包含允許使用者登入的所需資訊。  

| 參數 | 格式 | 說明 |
| ---              | --- | --- |
|`device_code`     |字串| 長字串，可用於驗證用戶端與授權伺服器之間的工作階段。  用戶端可用於向授權伺服器要求存取權杖。 |
|`user_code`       |字串| 向使用者顯示的短字串，用於識別輔助裝置上的工作階段。|
|`verification_uri`|URI| 為了執行登入程序，使用者應使用 `user_code` 查看的 URI。 |
|`verification_uri_complete`|URI| 結合 `user_code` 和 `verification_uri` 的 URI，用於向使用者進行非文字傳輸 (例如透過藍牙傳輸到裝置，或透過 QR 代碼)。  |
|`expires_in`      |int| `device_code` 和 `user_code` 到期之前的秒數。 |
|`interval`        |int| 用戶端在輪詢要求之間應等待的秒數。 |
| `message`        |字串| 人類看得懂的字串，包含使用者說明。  在 `?mkt=xx-XX` 形式的要求中加入  **查詢參數**、填寫適當的語言文化代碼，即可進行當地語系化。 |

## <a name="authenticating-the-user"></a>驗證使用者

收到 `user_code` 和 `verification_uri` 後，用戶端會向使用者顯示這些內容，指示他們使用手機或電腦瀏覽器登入。  此外，用戶端可使用 QR 代碼或類似機制顯示 `verfication_uri_complete`，由這類機制代替使用者輸入 `user_code`。

使用者在 `verification_uri`進行驗證時，用戶端應使用 `device_code` 輪詢 `/token` 端點，以取得要求的權杖。

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

|參數 | 必要 | 說明|
| -------- | -------- | ---------- |
|`grant_type` | 必要| 必須是 `urn:ietf:params:oauth:grant-type:device_code`|
|`client_id`  | 必要| 必須符合初始要求中使用的 `client_id`。 |
|`device_code`| 必要| 裝置授權要求傳回的 `device_code`。  |

### <a name="expected-errors"></a>預期的錯誤

由於裝置代碼流程是輪詢通訊協定，因此用戶端應在使用者完成驗證之前收到錯誤。  

| Error | 說明 | 用戶端動作 |
|------ | ----------- | -------------|
| `authorization_pending` |  用戶尚未完成驗證，但也未取消流程。 | 經過至少 `interval` 秒後，重複要求流程。 |
| `authorization_declined`|  終端使用者拒絕了授權要求。| 停止輪詢，並還原到未驗證的狀態。  |
| `bad_verification_code`|無法識別傳送到 `/token` 端點的 `device_code`。 | 確認用戶端是否在要求中傳送正確的 `device_code`。 |
| `expired_token`|  已經過了至少 `expires_in` 秒，`device_code` 也無法再進行驗證。 | 停止輪詢，並還原到未驗證的狀態。 |


### <a name="succesful-authentication-response"></a>成功的驗證回應

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

| 參數 | 格式 | 說明 |
| --------- | ------ | ----------- |
|`token_type` | 字串| 一律是「Bearer」。 |
|`scope` | 空格分隔的字串 | 如果傳回了存取權杖，則會列出存取權杖的有效範圍。 |
|`expires_in`| int | 包含的存取權杖須經過多久 (秒數) 才會生效。 |
|`access_token`| 不透明字串 | 針對已要求的[範圍](v2-permissions-and-consent.md)發出。  |
|`id_token`   | JWT | 原始 `scope` 參數包含 `openid` 範圍時發出。  |
|`refresh_token` | 不透明字串 | 原始 `scope` 參數包含 `offline_access` 時發出。  |

執行 [OAuth 代碼流程文件](v2-oauth2-auth-code-flow.md#refresh-the-access-token)詳述的同一個流程，可將重新整理權杖用於取得新的存取權杖和重新整理權杖。  