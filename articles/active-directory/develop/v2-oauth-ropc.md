---
title: 使用 Microsoft 身分識別平台，讓使用者使用資源擁有者密碼認證 (ROPC) 授與登入 |Azure
description: 使用資源擁有者密碼認證授與支援無瀏覽器的驗證流程。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9cfa28cae87c8a9a97e1c64b96f75ae4c6eab08d
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004936"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credential"></a>Microsoft 身分識別平台和 OAuth 2.0 資源擁有者密碼認證

Microsoft 身分識別平台支援[資源擁有者密碼認證 (ROPC) 授與](https://tools.ietf.org/html/rfc6749#section-4.3)，可讓應用程式藉由直接處理其密碼登入使用者。 ROPC 流程需要較高程度的信任和使用者風險和其他、 更安全的流程無法使用時，您應該只使用此流程。

> [!IMPORTANT]
>
> * Microsoft 身分識別平台端點僅支援 ROPC，Azure AD 租用戶，而不是個人帳戶。 因此您必須使用租用戶特定端點 (`https://login.microsoftonline.com/{TenantId_or_Name}`) 或 `organizations` 端點。
> * 受邀加入 Azure AD 租用戶的個人帳戶無法使用 ROPC。
> * 沒有密碼的帳戶無法透過 ROPC 登入。 針對此案例，建議您改用不同的應用程式流程。
> * 如果使用者必須使用多重要素驗證 (MFA) 來登入應用程式，則會遭到封鎖。

## <a name="protocol-diagram"></a>通訊協定圖表

下圖顯示 ROPC 流程。

![ROPC 流程](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>授權要求

ROPC 流程是單一要求&mdash;它會將用戶端識別與使用者的認證傳送至 IDP，然後接收權杖。 用戶端這麼做之前，必須先要求使用者的電子郵件地址 (UPN) 和密碼。 要求成功之後，用戶端應該就會立即從記憶體安全地釋出使用者的認證。 用戶端永遠不得儲存這些認證。

> [!TIP]
> 嘗試在 Postman 中執行這項要求！
> [![在 Postman 中執行](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| 參數 | 條件 | 描述 |
| --- | --- | --- |
| `tenant` | 必要項 | 您想要將使用者登入的目標目錄租用戶。 這可以採用 GUID 或易記名稱格式。 此參數無法設為 `common` 或 `consumers`，但可設定為 `organizations`。 |
| `grant_type` | 必要項 | 必須設為 `password`。 |
| `username` | 必要項 | 使用者的電子郵件地址。 |
| `password` | 必要項 | 使用者的密碼。 |
| `scope` | 建議 | 以空格分隔的[範圍](v2-permissions-and-consent.md)清單或應用程式所需的權限。 在互動式流程中，系統管理員或使用者必須同意事先這些範圍。 |

### <a name="successful-authentication-response"></a>成功驗證回應

下列範例顯示成功的權杖回應：

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
| `token_type` | 字串 | 一律設定為 `Bearer`。 |
| `scope` | 空格分隔的字串 | 如果傳回了存取權杖，此參數會列出存取權杖的有效範圍。 |
| `expires_in`| int | 所含存取權杖的有效時間長度 (秒數)。 |
| `access_token`| 不透明字串 | 針對已要求的[範圍](v2-permissions-and-consent.md)發出。 |
| `id_token` | JWT | 原始 `scope` 參數包含 `openid` 範圍時發出。 |
| `refresh_token` | 不透明字串 | 原始 `scope` 參數包含 `offline_access` 時發出。 |

您可以使用 [OAuth 程式碼流程文件](v2-oauth2-auth-code-flow.md#refresh-the-access-token)中所述的同一個流程，透過重新整理權杖來取得新的存取權杖和重新整理權杖。

### <a name="error-response"></a>錯誤回應

如果使用者未提供正確的使用者名稱或密碼，或用戶端未收到所要求的同意，驗證將會失敗。

| Error | 描述 | 用戶端動作 |
|------ | ----------- | -------------|
| `invalid_grant` | 驗證失敗 | 認證不正確，或用戶端沒有同意所要求的範圍。 如果未授與的範圍，`consent_required`便會傳回錯誤。 如果發生這種情況，用戶端應使用 WebView 或瀏覽器將使用者傳送至互動式提示。 |
| `invalid_request` | 要求未正確建構 | 不支援的授與類型`/common`或`/consumers`驗證內容。  請改用 `/organizations`。 |
| `invalid_client` | 未正確設定應用程式 | 如果此情形`allowPublicClient`屬性未設定為在中，則為 true[應用程式資訊清單](reference-app-manifest.md)。 需要 `allowPublicClient` 屬性，因為 ROPC 授與沒有重新導向 URI。 除非設定此屬性，否則 Azure AD 無法判斷應用程式是公用用戶端應用程式還是機密用戶端應用程式。 ROPC 只有在公用用戶端應用程式上執行。 |

## <a name="learn-more"></a>深入了解

* 使用[範例主控台應用程式](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2)自行試用 ROPC。
* 若要判斷您是否應該使用 v2.0 端點，請參閱[Microsoft 身分識別平台限制](active-directory-v2-limitations.md)。
