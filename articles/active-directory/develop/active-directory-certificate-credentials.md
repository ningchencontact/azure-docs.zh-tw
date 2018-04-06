---
title: Azure AD 中的憑證認證 | Microsoft Docs
description: 本文討論如何註冊和使用憑證認證來進行應用程式驗證
services: active-directory
documentationcenter: .net
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f7c58b4ebd840aca555b52a03cf44ace311b64e3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="certificate-credentials-for-application-authentication"></a>適用於應用程式驗證的憑證認證

Azure Active Directory 可讓應用程式使用本身的認證來進行驗證。 例如 OAuth 2.0 用戶端認證授與流程 ([v1](active-directory-protocols-oauth-service-to-service.md)、 [v2](active-directory-v2-protocols-oauth-client-creds.md)) 和代理者流程 ([v1](active-directory-protocols-oauth-on-behalf-of.md)、[v2](active-directory-v2-protocols-oauth-on-behalf-of.md))。
可用的認證形式之一，便是以應用程式擁有的憑證所簽署的 JSON Web 權杖 (JWT) 判斷提示。

## <a name="format-of-the-assertion"></a>判斷提示的格式
為了計算判斷提示，您可能想要使用所選語言中許多 [JSON Web 權杖](https://jwt.ms/) \(英文\) 程式庫其中之一。 權杖所攜帶的資訊是︰

#### <a name="header"></a>頁首

| 參數 |  備註 |
| --- | --- |
| `alg` | 應該是 **RS256** |
| `typ` | 應該是 **JWT** |
| `x5t` | 應該是 X.509 憑證 SHA-1 憑證指紋 |

#### <a name="claims-payload"></a>宣告 (承載)

| 參數 |  備註 |
| --- | --- |
| `aud` | 對象︰應該是 **https://login.microsoftonline.com/*tenant_Id*/oauth2/token** |
| `exp` | 到期日：權杖到期的日期。 時間會表示為從 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) UTC 到權杖有效時間到期的秒數。|
| `iss` | 簽發者︰應該是 client_id (用戶端服務的應用程式識別碼) |
| `jti` | GUID：JWT ID |
| `nbf` | 生效時間：無法在此日期之前使用權杖。 時間會表示為從 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) UTC 到權杖發出時間的秒數。 |
| `sub` | 主旨：對於 `iss`，應該是 client_id (用戶端服務的應用程式識別碼) |

#### <a name="signature"></a>簽章

簽章是使用 [JSON Web 權杖 RFC7519 規格](https://tools.ietf.org/html/rfc7519) 中所述的憑證計算的

### <a name="example-of-a-decoded-jwt-assertion"></a>已解碼的 JWT 判斷提示範例

```
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,  
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"

```

### <a name="example-of-an-encoded-jwt-assertion"></a>已編碼的 JWT 判斷提示範例

下列字串是已編碼判斷提示的範例。 如果您仔細看，會發現三個以點 (.) 分隔的區段。
第一個區段編碼標頭，第二個區段編碼承載，而最後區段則是使用前兩個區段的內容的憑證所計算的簽章。
```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

### <a name="register-your-certificate-with-azure-ad"></a>使用 Azure AD 註冊您的憑證

您可以使用以下任一種方法，透過 Azure 入口網站在 Azure AD 中建立憑證認證與用戶端應用程式之間的關聯：

**上傳憑證檔案**

在用戶端應用程式的 Azure 應用程式註冊中，按一下 [設定]，並按一下 [金鑰]，然後按一下 [上傳公開金鑰]。 選取您想要上傳的憑證檔案，並按一下 [儲存]。 儲存後會上傳憑證，並且會顯示指紋、開始日期和到期值。 

**更新應用程式資訊清單**

擁有憑證之後，您必須計算︰

- `$base64Thumbprint`，它是憑證雜湊的 base64 編碼
- `$base64Value`，它是憑證未經處理資料的 base64 編碼

您也必須提供 GUID 來識別應用程式資訊清單中的金鑰 (`$keyId`)。

在適用於用戶端應用程式的 Azure 應用程式註冊中，開啟應用程式資訊清單，然後使用下列結構描述，利用您新的憑證資訊來取代 *keyCredentials* 屬性：

```
"keyCredentials": [
    {
        "customKeyIdentifier": "$base64Thumbprint",
        "keyId": "$keyid",
        "type": "AsymmetricX509Cert",
        "usage": "Verify",
        "value":  "$base64Value"
    }
]
```

儲存對應用程式資訊清單所做的編輯，然後上傳至 Azure AD。 keyCredentials 屬性是多重值，，因此您可能上傳多個憑證以進行更豐富的金鑰管理。
