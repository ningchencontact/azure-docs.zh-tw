---
title: 保護使用 Microsoft 身分識別平台的隱含流程的單一頁面應用程式 |Azure
description: 建置 web 應用程式的單一頁面應用程式中使用隱含流程 Microsoft 身分識別平台實作。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 160799917fb2802689f23d57d6daa9ad0a92c37a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273086"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft 身分識別平台和隱含授與流程

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

與 Microsoft 身分識別平台的端點，您可以將使用者登入您的單一頁面應用程式，使用來自 Microsoft 的同時個人和工作或學校帳戶。 主要於瀏覽器中執行的單頁及 JavaScript 應用程式在驗證時會面臨一些有趣的挑戰：

* 這些應用程式的安全性特性與傳統伺服器型 Web 應用程式大不相同。
* 許多授權伺服器及識別提供者不支援 CORS 要求。
* 重新導向離開應用程式的完整網頁瀏覽器變得對使用者經驗特別有侵入性。

這些應用程式 （AngularJS、 Ember.js、 React.js 和等等），Microsoft 身分識別平台支援的 OAuth 2.0 隱含授權流程。 隱含流程相關說明，請參閱 [OAuth 2.0 規格](https://tools.ietf.org/html/rfc6749#section-4.2)。 其主要優點是它可讓應用程式，以取得權杖 Microsoft 身分識別平台而不需執行的後端伺服器認證交換。 這可讓應用程式登入使用者、維護工作階段，並且取得用戶端 JavaScript 程式碼中所有其他 Web API 的權杖。 使用隱含流程時需注意幾個重要的安全性考量，特別是[用戶端](https://tools.ietf.org/html/rfc6749#section-10.3)及[使用者模擬](https://tools.ietf.org/html/rfc6749#section-10.3)。

如果您想要將驗證新增至您的 JavaScript 應用程式中使用隱含流程與 Microsoft 身分識別平台，建議使用的開放原始碼 JavaScript 程式庫中， [msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)。

但如果您不想在單頁應用程式中使用程式庫，也不想自行傳送通訊協定訊息，請遵循下列一般步驟。

> [!NOTE]
> 並非所有的 Azure Active Directory (Azure AD) 案例和功能都受到 Microsoft 身分識別平台的端點。 若要判斷是否應該使用 Microsoft 身分識別平台的端點，請參閱[Microsoft 身分識別平台限制](active-directory-v2-limitations.md)。

## <a name="protocol-diagram"></a>通訊協定圖表

下圖說明整個隱含登入流程，而後續幾節會更詳細地說明每個步驟。

![OpenID Connect 區隔線](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>傳送登入要求

若要一開始將使用者登入您的應用程式，您可以傳送[OpenID Connect](v2-protocols-oidc.md)驗證要求，以及如何取得`id_token`從 Microsoft 身分識別平台的端點。

> [!IMPORTANT]
> 若要成功要求識別碼權杖，應用程式註冊[Azure 入口網站-應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)分頁必須具有正確，選取啟用的隱含授與流程**存取權杖**和**識別碼權杖**底下**隱含授與**一節。 如果未啟用，`unsupported_response`便會傳回錯誤：**不允許此用戶端使用為輸入參數 "response_type" 提供的值。值應為 'code'**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> 若要使用隱含流程來測試登入，請按一下 <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize.. </a>登入之後，您的瀏覽器應重新導向至在位址列中有 `id_token` 的 `https://localhost/myapp/` 。
>

| 參數 |  | 描述 |
| --- | --- | --- |
| `tenant` | 必要 |要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。 允許的值為 `common`、`organizations`、`consumers` 及租用戶識別碼。 如需更多詳細資訊，請參閱 [通訊協定基本概念](active-directory-v2-protocols.md#endpoints)。 |
| `client_id` | 必要 | （用戶端） 應用程式識別碼[Azure 入口網站-應用程式註冊](https://go.microsoft.com/fwlink/?linkid=2083908)指派給您的應用程式的頁面。 |
| `response_type` | 必要 |必須包含 OpenID Connect 登入的 `id_token` 。 它也可能包含 response_type `token`。 這裡使用 `token` ，讓您的應用程式能夠立即從授權端點接收存取權杖，而不需要向授權端點進行第二次要求。 如果您使用 `token` response_type，`scope` 參數必須包含範圍，以指出要對哪個資源發出權杖。 |
| `redirect_uri` | 建議使用 |應用程式的 redirect_uri，您的應用程式可在此傳送及接收驗證回應。 其必須完全符合您在入口網站中註冊的其中一個 redirect_uris，不然就必須得是編碼的 url。 |
| `scope` | 必要 |[範圍](v2-permissions-and-consent.md)的空格分隔清單。 針對 OpenID Connect，即必須包含範圍 `openid`，其會在同意 UI 中轉譯成「讓您登入」權限。 (選擇性) 您也可以包含 `email` 或 `profile` 範圍，以取得額外使用者資料的存取權。 您也可以在此要求中包含其他範圍，以要求同意各種資源。 |
| `response_mode` | 選用 |指定將產生的權杖送回到應用程式所應該使用的方法。 預設為查詢存取權杖，但若要求包含 id_token，則會查詢片段。 |
| `state` | 建議使用 |同樣會隨權杖回應傳回之要求中所包含的值。 其可以是您想要之任何內容的字串。 隨機產生的唯一值通常用於 [防止跨站台要求偽造攻擊](https://tools.ietf.org/html/rfc6749#section-10.12)。 此狀態也用於在驗證要求出現之前，於應用程式中編碼使用者的狀態資訊，例如之前所在的網頁或檢視。 |
| `nonce` | 必要 |由應用程式產生且包含在要求中的值，會以宣告方式包含在產生的 id_token 中。 應用程式接著便可確認此值，以減少權杖重新執行攻擊。 此值通常是隨機的唯一字串，可用以識別要求的來源。 只有要求 id_token 時，才需要此值。 |
| `prompt` | 選用 |表示需要的使用者互動類型。 目前的有效值只有 'login'、'none'、'select_account' 和 'consent'。 `prompt=login` 會強制使用者在該要求上輸入認證，否定單一登入。 `prompt=none` 則相反-它會確保使用者不顯示任何互動式提示。 如果無法透過單一登入以無訊息方式完成要求，Microsoft 身分識別平台端點會傳回錯誤。 `prompt=select_account` 會將使用者傳送至帳戶選擇器，工作階段中記下的所有帳戶都會出現在當中。 `prompt=consent` 會在使用者登入之後觸發 OAuth 同意對話方塊，詢問使用者是否要授與權限給應用程式。 |
| `login_hint`  |選用 |如果您事先知道其使用者名稱，可用來預先填入使用者登入頁面的使用者名稱/電子郵件地址欄位。 通常應用程式會在重新驗證期間使用此參數，已經使用 `preferred_username` 宣告從上一個登入擷取使用者名稱。|
| `domain_hint` | 選用 |可以是 `consumers` 或 `organizations` 其中一個。 如果包含，它會略過電子郵件為基礎的探索程序，使用者會經歷在登入頁面上，導致稍微更流暢的使用者體驗。 通常應用程式會在重新驗證期間使用此參數，方法是從 id_token 擷取 `tid` 宣告。 如果 `tid` 宣告值是 `9188040d-6c67-4c5b-b112-36a304b66dad` (Microsoft 帳戶取用者租用戶)，您應該使用 `domain_hint=consumers`。 否則，您可以在重新驗證期間使用 `domain_hint=organizations`。 |

此時，會要求使用者輸入其認證並完成驗證。 Microsoft 身分識別平台端點也會確保使用者已經同意中指出的權限`scope`查詢參數。 如果使用者**完全未**同意這些權限的任一項，其會要求使用者同意必要權限。 如需詳細資訊，請參閱[權限、同意及多租用戶應用程式](v2-permissions-and-consent.md)。

一旦使用者驗證，並授與同意，Microsoft 身分識別平台端點會將回應傳回給您的應用程式所指定`redirect_uri`，使用中指定的方法`response_mode`參數。

#### <a name="successful-response"></a>成功回應

使用 `response_mode=fragment` 及 `response_type=id_token+token` 的成功回應如下所示 (內含分行符號以利閱讀)：

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fuser.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| 參數 | 描述 |
| --- | --- |
| `access_token` |如果 `response_type` 包含 `token` 則納入。 應用程式要求的存取權杖，在此案例中為 Microsoft Graph 的存取權杖。 不應該已解碼或檢查存取權杖，它應該被視為不透明的字串。 |
| `token_type` |如果 `response_type` 包含 `token` 則納入。 一律為 `Bearer`。 |
| `expires_in`|如果 `response_type` 包含 `token` 則納入。 表示權杖有效的秒數 (針對快取目的)。 |
| `scope` |如果 `response_type` 包含 `token` 則納入。 表示在 access_token 的有效範圍。 可能不包含的所有範圍要求，如果不是套用到使用者 （如果是 Azure AD 專用範圍個人的帳戶用來登入所要求）。 |
| `id_token` | 已簽署的 JSON Web 權杖 (JWT)。 應用程式可以將這個權杖的區段解碼，來要求已登入使用者的相關資訊。 應用程式可以快取的值，並顯示，但它不應該依賴這些取得任何授權或安全性界限。 如需有關 id_token 的詳細資訊，請參閱 [`id_token reference`](id-tokens.md)。 <br> **附註：** 只在要求 `openid` 範圍時提供。 |
| `state` |如果要求中包含狀態參數，回應中就應該出現相同的值。 應用程式應該確認要求和回應中的狀態值完全相同。 |

#### <a name="error-response"></a>錯誤回應

錯誤回應可能也會傳送至 `redirect_uri` ，讓應用程式可以適當地處理：

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 參數 | 描述 |
| --- | --- |
| `error` |用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| `error_description` |協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |

## <a name="validate-the-idtoken"></a>驗證 id_token

僅接收 id_token 不足以驗證使用者;您也必須驗證 id_token 的簽章，並確認您的應用程式需求為基礎的權杖中的宣告。 Microsoft 身分識別平台端點會使用[JSON Web 權杖 (Jwt)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)和公開金鑰加密簽署權杖，並驗證它們有效。

您可以選擇驗證用戶端程式碼中的 `id_token`，但是常見的作法是將 `id_token` 傳送至後端伺服器，並且在那裡執行驗證。 一旦驗證了 id_token 的簽章，有數項宣告您將需要確認。 如需詳細資訊，請參閱 [`id_token` 參考](id-tokens.md)，包括[驗證權杖](id-tokens.md#validating-an-id_token)及[簽署金鑰變換的重要資訊](active-directory-signing-key-rollover.md)。 我們建議利用程式庫來剖析和驗證權杖 - 對於大部分語言和平台至少有一個可用。

您可能也希望根據自己的案例驗證其他宣告。 一些常見的驗證包括：

* 確保使用者/組織已註冊應用程式。
* 確保使用者擁有適當授權/權限。
* 確保驗證具有特定強度，例如多重要素驗證。

一旦驗證 id_token 之後，您可以利用使用者開始工作階段，並使用 id_token 中的宣告，以取得您的應用程式中的使用者相關資訊。 這項資訊可以用於顯示、 記錄、 個人化，和更多功能。

## <a name="get-access-tokens"></a>取得存取權杖

既然您已經將使用者註冊到單一頁面應用程式中，您可以取得存取權杖呼叫 web Api 受到 Microsoft 身分識別平台，而這類[Microsoft Graph](https://developer.microsoft.com/graph)。 即使您已經收到使用 `token` response_type 的權杖，仍可使用此方法來取得其他資源的權杖，而不需重新導向使用者進行再次登入。

在正常的 OpenID Connect/OAuth 流程中，您可以這樣對 Microsoft 身分識別平台提出要求`/token`端點。 不過，Microsoft 身分識別平台端點並不支援 CORS 要求，因此進行 AJAX 呼叫以取得和重新整理權杖是不可能。 相反地，您可以在隱藏的 iframe 中使用隱含流程，為其他 Web API 取得新權杖： 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

如需 URL 中查詢參數的詳細資料，請參閱[傳送登入要求](#send-the-sign-in-request)。

> [!TIP]
> 請嘗試將下列要求貼至瀏覽器！ (請務必以您使用者的正確值取代 `login_hint` 值)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint=your-username`
>

由於 `prompt=none` 參數，此要求會立即成功或失敗，並且傳回給您的應用程式。 會使用 `response_mode` 參數中指定的方法，將成功的回應傳送至指定的 `redirect_uri` 給您的應用程式。

#### <a name="successful-response"></a>成功回應

使用 `response_mode=fragment` 的成功回應如下所示：

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| 參數 | 描述 |
| --- | --- |
| `access_token` |如果 `response_type` 包含 `token` 則納入。 應用程式要求的存取權杖，在此案例中為 Microsoft Graph 的存取權杖。 不應該已解碼或檢查存取權杖，它應該被視為不透明的字串。 |
| `token_type` | 一律為 `Bearer`。 |
| `expires_in` | 表示權杖有效的秒數 (針對快取目的)。 |
| `scope` | 表示在 access_token 的有效範圍。 可能不包含的所有範圍要求，如果不是套用到使用者 （如果是 Azure AD 專用範圍個人的帳戶用來登入所要求）。 |
| `id_token` | 已簽署的 JSON Web 權杖 (JWT)。 如果 `response_type` 包含 `id_token` 則納入。 應用程式可以將這個權杖的區段解碼，來要求已登入使用者的相關資訊。 應用程式可以快取的值，並顯示，但它不應該依賴這些取得任何授權或安全性界限。 如需 id_token 的詳細資訊，請參閱 [`id_token` 參考](id-tokens.md)。 <br> **附註：** 只在要求 `openid` 範圍時提供。 |
| `state` |如果要求中包含狀態參數，回應中就應該出現相同的值。 應用程式應該確認要求和回應中的狀態值完全相同。 |

#### <a name="error-response"></a>錯誤回應

錯誤回應可能也會傳送至 `redirect_uri` ，讓應用程式可以適當地處理。 如果是 `prompt=none`，預期的錯誤為：

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| 參數 | 描述 |
| --- | --- |
| `error` |用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。 |
| `error_description` |協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。 |

如果您在 iframe 要求中收到此錯誤，使用者必須再次以互動方式登入以擷取新的權杖。 您可以選擇對於您的應用程式合理的任何方式處理這種情況。

## <a name="validating-access-tokens"></a>驗證存取權杖

一旦您收到 access_token，請務必要驗證權杖的簽章以及下列宣告。 您也可以選擇根據您的案例驗證其他宣告。

* **對象**宣告，用以確定權杖預定要提供給您的應用程式
* **簽發者**宣告-用以確認權杖已簽發給您的應用程式，Microsoft 身分識別平台端點
* **生效時間**和**到期時間**宣告，用以確認權杖尚未過期

如需存取權杖中所出示宣告的詳細資訊，請參閱[存取權杖參考](access-tokens.md)

## <a name="refreshing-tokens"></a>重新整理權杖

隱含授與不提供重新整理權杖功能。 `id_token` 和 `access_token` 馬上就會到期，因此您的應用程式必須準備好定期重新整理這些權杖。 若要重新整理任一種類型的語彙基元，您可以執行上述使用的相同隱藏的 iframe 要求`prompt=none`參數來控制身分識別平台的行為。 如果您想要收到新的 `id_token`，務必使用 `response_type=id_token` 和 `scope=openid`，以及 `nonce` 參數。

## <a name="send-a-sign-out-request"></a>傳送登出要求

OpenID Connect`end_session_endpoint`可讓您的應用程式，將要求傳送至 Microsoft 身分識別平台端點，以結束使用者工作階段及清除 Microsoft 身分識別平台端點設定的 cookie。 若要將使用者從 Web 應用程式完全登出，您的應用程式應結束自己和使用者之間的工作階段 (通常是透過清除權杖快取或卸除 Cookie)，然後將瀏覽器重新導向至：

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| 參數 |  | 描述 |
| --- | --- | --- |
| `tenant` |必要 |要求路徑中的 `{tenant}` 值可用來控制可登入應用程式的人員。 允許的值為 `common`、`organizations`、`consumers` 及租用戶識別碼。 如需更多詳細資訊，請參閱 [通訊協定基本概念](active-directory-v2-protocols.md#endpoints)。 |
| `post_logout_redirect_uri` | 建議使用 | 使用者在完成登出之後應該要返回的 URL。 這個值必須符合為應用程式註冊的其中一個重新導向 URI。 如果未包含，使用者將會顯示一般訊息的 Microsoft 身分識別平台端點。 |

## <a name="next-steps"></a>後續步驟

* 瀏覽所有 [MSAL JS 範例](sample-v2-code.md)以開始撰寫程式碼。
