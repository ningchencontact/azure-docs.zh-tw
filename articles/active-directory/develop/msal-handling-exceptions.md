---
title: 錯誤和例外狀況 (MSAL) | Azure
description: 了解如何處理 MSAL 應用程式中的錯誤和例外狀況、條件式存取和宣告挑戰。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 30ab8a3fec459bef1a85c44e9a7cdb91b541fa2d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111384"
---
# <a name="handling-exceptions-and-errors-using-msal"></a>使用 MSAL 處理例外狀況和錯誤
Microsoft 驗證程式庫 (MSAL) 中的例外狀況僅供應用程式開發人員進行疑難排解之用，而不會向使用者顯示。 例外狀況訊息不會當地語系化。

在處理例外狀況和錯誤時，您可以使用例外狀況類型本身和錯誤碼來區別例外狀況。  如需錯誤碼清單，請參閱[驗證與授權錯誤碼](reference-aadsts-error-codes.md)。

## <a name="net-exceptions"></a>.NET 例外狀況
在處理例外狀況時，您可以使用例外狀況類型本身和 `ErrorCode` 成員來區別例外狀況。 `ErrorCode` 的值是 [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet) 類型的常數。

您也可以查看 [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet)、[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)、[MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) 的欄位。

如果擲回 [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)，錯誤碼可能會包含可在[驗證和授權錯誤碼](reference-aadsts-error-codes.md)中找到的代碼。

### <a name="common-exceptions"></a>常見例外狀況
以下是可能會擲回的常見例外狀況和一些可能的補救措施。

| 例外狀況 | 錯誤碼 | 緩和|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001：使用者或系統管理員未同意使用識別碼為 '{appId}'、名稱為 '{appName}' 的應用程式。 請傳送此使用者和資源的互動式授權要求。| 您必須先取得使用者同意。 如果您未使用 .NET Core (因此就沒有任何 Web UI)，請呼叫 `AcquireTokeninteractive` (一次即可)。 如果您使用 .NET core 或不想要執行 `AcquireTokenInteractive`，則使用者可以瀏覽至下列 URL 表示同意： https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read 。 若要呼叫 `AcquireTokenInteractive`：`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079：使用者必須使用多重要素驗證。| 沒有風險降低措施 - 如果您的租用戶已設定 MFA，且 AAD 決定加以強制執行，您就必須退回到 `AcquireTokenInteractive` 或 `AcquireTokenByDeviceCode` 之類的互動式流程。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010：不支援透過 */common* 或 */consumers* 端點的授與類型。 請使用 */organizations* 或租用戶專屬端點。 您使用的是 */common*。| 如 Azure AD 的訊息所說明，授權單位必須有一個租用戶或 */organizations*。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002：要求本文必須包含下列參數：client_secret 或 client_assertion。| 如果您的應用程式未在 Azure AD 中註冊為公用用戶端應用程式，就會發生此狀況。 在 Azure 入口網站中，為您的應用程式編輯資訊清單，並將 `allowPublicClient` 設為 `true`。 |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| unknown_user 訊息：無法識別登入的使用者| 程式庫無法查詢目前登入 Windows 的使用者，或這名使用者未加入 AD 或 AAD (不支援已加入工作場所的使用者)。 風險降低措施 1：在 UWP 上，確認應用程式具有下列功能：企業驗證、私人網路 (用戶端和伺服器)、使用者帳戶資訊。 降低風險 2：實作您自己的邏輯以擷取使用者名稱 (例如john@contoso.com)，並使用採用該使用者名稱的 `AcquireTokenByIntegratedWindowsAuth` 表單。|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| 此方法需依賴由 Active Directory (AD) 公開的通訊協定。 如果使用者建立於不具 AD 支援 (「受控」使用者) 的 Azure Active Directory 中，此方法將會失敗。 使用者若建立於 AD 中，並且受 AAD 支援(「同盟」使用者)，則可受益於這種非互動式的驗證方法。 風險降低措施：使用互動式驗證。|

## <a name="javascript-errors"></a>JavaScript 錯誤

MSAL.js 提供的錯誤物件可擷取及分類不同類型的常見錯誤，並且有介面可用來存取錯誤的特定詳細資料 (例如錯誤訊息)，以適當處理這些錯誤。

**Error 物件**

```javascript                                
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```                
藉由擴充錯誤類別，您將可存取下列屬性：
* **AuthError.message：** 此訊息與 errorMessage 相同。
* **AuthError.stack：** 擲回錯誤的堆疊追蹤。 可用來追蹤錯誤的原點。

**錯誤類型**

可用的錯誤類型如下：

* *AuthError：* MSAL.js 程式庫的基本錯誤類別，也可用於非預期的錯誤。

* *ClientAuthError：* 表示用戶端驗證有問題的錯誤類別。 來自程式庫的錯誤大多為 ClientAuthError。 這些可能是諸如在登入進行期間呼叫登入方法、使用者取消登入等錯誤。

* *ClientConfigurationError：* 在指定的使用者設定參數格式不正確或遺失時，於提出要求之前擲回的錯誤類別擴充 ClientAuthError。

* *ServerError：* 代表驗證伺服器傳送之錯誤字串的錯誤類別。 這些可能是要求格式或參數無效之類的錯誤，或導致伺服器無法對使用者進行驗證或授權的任何其他錯誤。

* *InteractionRequiredAuthError：* 代表需要互動式呼叫之伺服器錯誤的錯誤類別擴充 ServerError。 如果使用者必須與伺服器互動以提供驗證/授權所需的認證或同意，`acquireTokenSilent` 就會擲回此錯誤。 錯誤碼包括 "interaction_required"、"login_required"、"consent_required"。

若要對使用重新導向方法 (`loginRedirect`、`acquireTokenRedirect`) 的驗證流程進行錯誤處理，您必須註冊在使用 `handleRedirectCallback()` 方法的重新導向之後依據作業成功與否呼叫的回呼，如下所示：

```javascript
function authCallback(error, response) {
    //handle redirect response
}


var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);

myMSALObj.acquireTokenRedirect(request);
```

快顯體驗的方法 (`loginPopup`、`acquireTokenPopup`) 會傳回承諾，因此您可以使用承諾模式 (.then 和 .catch) 處理錯誤，如下所示：

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>需要互動錯誤

在需要 UI 互動時會傳回錯誤。 這表示您嘗試使用非互動式方法來取得權杖 (例如 `acquireTokenSilent`)，但 MSAL 無法以無訊息模式加以執行。 可能的原因包括：

* 您必須登入
* 您必須同意
* 您需要通過多重要素驗證。

補救措施是呼叫互動式方法，例如 `acquireTokenPopup` 或 `acquireTokenRedirect`：

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

## <a name="conditional-access-and-claims-challenges"></a>條件式存取和宣告挑戰
以無訊息模式取得權杖時，若您嘗試存取的 API 需要[條件式存取宣告挑戰](conditional-access-dev-guide.md) (例如 MFA 原則)，則您的應用程式可能會收到錯誤。

處理此錯誤的模式是使用 MSAL 以互動方式取得權杖。 以互動方式取得權杖時，系統會對使用者發出提示，讓他們有機會符合所需的條件式存取原則。

在某些情況下，當您呼叫需要條件式存取的 API 時，您可能會在 API 發出的錯誤中收到宣告挑戰。 例如，如果條件式存取原則是必須具有受控裝置 (Intune)，則錯誤可能會是 [AADSTS53000：您的裝置必須受到管理才能存取此資源](reference-aadsts-error-codes.md)，或類似的錯誤。 在此情況下，您可以在取得權杖的呼叫中傳入宣告，讓系統提示使用者符合適當的原則。

### <a name="net"></a>.NET
從 MSAL.NET 呼叫需要條件式存取的 API 時，您的應用程式將必須處理宣告挑戰例外狀況。 此例外狀況會呈現為[宣告](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet)屬性非空白的 [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)。

若要處理宣告挑戰，您必須使用 `PublicClientApplicationBuilder` 類別的 `.WithClaim()` 方法。

### <a name="javascript"></a>JavaScript
使用 MSAL.js 以無訊息模式取得權杖時 (使用 `acquireTokenSilent`)，若您嘗試存取的 API 需要[條件式存取宣告挑戰](conditional-access-dev-guide.md) (例如 MFA 原則)，則您的應用程式可能會收到錯誤。

處理此錯誤的模式，是發出在 MSAL.js 中取得權杖的互動式呼叫 (例如 `acquireTokenPopup` 或 `acquireTokenRedirect`)，如下列範例所示：

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    myMSALObj.acquireTokenPopup(accessTokenRequest).then(
        function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
});
```

以互動方式取得權杖時，系統會對使用者發出提示，讓他們有機會符合所需的條件式存取原則。

當您呼叫需要條件式存取的 API 時，您可能會在 API 發出的錯誤中收到宣告挑戰。 在此情況下，您可以在呼叫中傳入錯誤所傳回的宣告 (例如 `extraQueryParameters`)，讓系統提示使用者符合適當的原則：

```javascript
var request = {
    scopes: ["user.read"],
    extraQueryParameters: {claims: claims}
}

myMSALObj.acquireTokenPopup(request);
```

## <a name="retrying-after-errors-and-exceptions"></a>在發生錯誤和例外狀況後重試

### <a name="http-error-codes-500-600"></a>HTTP 錯誤碼 500-600
針對 HTTP 錯誤碼為 500-600 的錯誤，MSAL.NET 會實作簡單的單次重試機制。

### <a name="http-429"></a>HTTP 429
服務權杖伺服器 (STS) 因「要求過多」而太忙碌時，會傳回 HTTP 錯誤 429，並以延遲秒數或日期提示您可在何時重試 (Retry-After 回應欄位)。

#### <a name="net"></a>.NET
[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) 例外狀況會將 `System.Net.Http.Headers.HttpResponseHeaders` 顯示為屬性 `namedHeaders`。 因此，您可以利用錯誤碼中的其他資訊改善應用程式的可靠性。 如同我們在先前的案例中提到的，您可以使用 `RetryAfterproperty` (類型 `RetryConditionHeaderValue`) 並計算何時可重試。

以下是精靈應用程式的範例 (使用用戶端認證流程)，但您可加以調整並用於任何取得權杖的方法。

```csharp
do
{
 retry = false;
 TimeSpan? delay;
 try
 {
  result = await publicClientApplication.AcquireTokenForClient(scopes, account)
                                        .ExecuteAsync();
 }
 catch (MsalServiceException serviceException)
 {
  if (ex.ErrorCode == "temporarily_unavailable")
  {
   RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
   if (retryAfter.Delta.HasValue)
   {
    delay = retryAfter.Delta;
   }
   else if (retryAfter.Date.HasValue)
   {
    delay = retryAfter.Date.Value.Offset;
   }
  }
 }

    …
 if (delay.HasValue)
 {
  Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
  retry = true;
 }
} while (retry);
```
