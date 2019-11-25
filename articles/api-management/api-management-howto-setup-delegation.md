---
title: 如何委派使用者註冊和產品訂閱
description: 了解如何在 Azure API Management 中將使用者註冊和產品訂閱委派給第三方。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: a69babdf2fffb4cb9d963f1806f3c85755e50294
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454350"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>如何委派使用者註冊和產品訂閱

Delegation allows you to use your existing website for handling developer sign in/sign up and subscription to products, as opposed to using the built-in functionality in the developer portal. It enables your website to own the user data and perform the validation of these steps in a custom way.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"> </a>委派開發人員登入和註冊

To delegate developer, sign in and sign up to your existing website, you'll need to create a special delegation endpoint on your site. It needs to act as the entry-point for any such request initiated from the API Management developer portal.

最終工作流程如下所示：

1. Developer clicks on the sign in or sign up link at the API Management developer portal
2. 瀏覽器重新導向至委派端點
3. Delegation endpoint in return redirects to or presents UI asking user to sign in or sign up
4. 成功時，將使用者重新導向回到他們所來自的 API 管理開發人員入口網站頁面

首先，請設定 API 管理透過委派端點來傳遞要求。 In the Azure portal, search for **Security** in your API Management resource and then click the **Delegation** item. Click the checkbox to enable 'Delegate sign in & sign up'.

![Delegation page][api-management-delegation-signin-up]

* 決定特殊委派端點的 URL，並在 [ **Delegation endpoint URL** ] 欄位中輸入。 
* 在 [委派驗證金鑰] 欄位中輸入密碼，用來計算提供給您驗證的簽章，以確定要求確實來自 Azure API 管理。 您可以按一下 [產生] 按鈕，讓 API 管理為您隨機產生金鑰。

現在您需要建立「 **委派端點**」。 必須執行一些動作：

1. 接收下列形式的要求：
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL of source page}&salt={string}&sig={string}*
   > 
   > 
   
    Query parameters for the sign in / sign up case:
   
   * **operation**：識別委派要求的類型 - 在此案例中只能是 **SignIn**
   * **returnUrl**: the URL of the page where the user clicked on a sign in or sign up link
   * **salt**：特殊 salt 字串，用於計算安全性雜湊
   * **sig**：已經過計算的安全性雜湊，用於和您已計算的雜湊進行比較
2. 確認要求來自 Azure API 管理 (選擇性，但基於安全性理由，強烈建議這麼做)
   
   * 根據 **returnUrl** 和 **salt** 查詢參數，計算字串的 HMAC-SHA512 雜湊 ([以下提供範例程式碼])：
     
     > HMAC(**salt** + '\n' + **returnUrl**)
     > 
     > 
   * 比較以上計算的雜湊和 **sig** 查詢參數的值。 如果兩個雜湊相符，則繼續下一步，否則拒絕要求。
3. Verify that you are receiving a request for sign in/sign up: the **operation** query parameter will be set to "**SignIn**".
4. Present the user with UI to sign in or sign up
5. 如果使用者要註冊，您必須在 API 管理中為他們建立對應的帳戶。 使用 API Management REST API [建立使用者]。 When doing so, ensure that you set the user ID to the same value as in your user store or to an ID that you can keep track of.
6. 成功驗證使用者之後：
   
   * [要求單一登入 (SSO) 權杖]
   * 將 returnUrl 查詢參數附加至您從上述 API 呼叫收到的 SSO URL：
     
     > 例如， https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * 將使用者重新導向至以上產生的 URL

除了 **SignIn** 作業之外，您也可以遵循上述步驟來執行帳戶管理，並使用以下其中一項作業：

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

您必須傳遞下列查詢參數，供帳戶管理作業使用。

* **operation**：識別委派要求的類型 (ChangePassword、ChangeProfile 或 CloseAccount)
* **userId**: the user ID of the account to manage
* **salt**：特殊 salt 字串，用於計算安全性雜湊
* **sig**：已經過計算的安全性雜湊，用於和您已計算的雜湊進行比較

## <a name="delegate-product-subscription"> </a>委派產品訂閱
Delegating product subscription works similarly to delegating user sign in/-up. 最終工作流程如下所示：

1. Developer selects a product in the API Management developer portal and clicks on the Subscribe button.
2. Browser is redirected to the delegation endpoint.
3. Delegation endpoint performs required product subscription steps. It's up to you to design the steps. They may include redirecting to another page to request billing information, asking additional questions, or simply storing the information and not requiring any user action.

若要啟用此功能，請在 [委派] 頁面按一下 [委派產品訂用帳戶]。

Next, ensure the delegation endpoint does the following actions:

1. 接收下列形式的要求：
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={operation}&productId={product to subscribe to}&userId={user making request}&salt={string}&sig={string}*
   >
   
    產品訂閱案例的查詢參數：
   
   * **operation**：識別委派要求的類型。 對於產品訂閱要求，有效的選項包括：
     * "Subscribe"：為使用者訂閱具有提供之識別碼的 (請參閱下面) 指定產品的要求
     * "Unsubscribe"：將為使用者取消訂閱產品的要求
     * "Renew"：訂閱續訂要求 (例如，可能過期)
   * **productId**：使用者要求訂閱之產品的識別碼
   * **subscriptionId**: on *Unsubscribe* and *Renew* - the ID of the product subscription
   * **userId**: the ID of the user the request is made for
   * **salt**：特殊 salt 字串，用於計算安全性雜湊
   * **sig**：已經過計算的安全性雜湊，用於和您已計算的雜湊進行比較

2. 確認要求來自 Azure API 管理 (選擇性，但基於安全性理由，強烈建議這麼做)
   
   * Compute an HMAC-SHA512 of a string based on the **productId**, **userId**, and **salt** query parameters:
     
     > HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * 比較以上計算的雜湊和 **sig** 查詢參數的值。 如果兩個雜湊相符，則繼續下一步，否則拒絕要求。
3. Process product subscription based on the type of operation requested in **operation** - for example, billing, further questions, etc.
4. On successfully subscribing the user to the product on your side, subscribe the user to the API Management product by [calling the REST API for subscriptions].

## <a name="delegate-example-code"> </a> 範例程式碼

These code samples show how to:

* Take the *delegation validation key*, which is set in the Delegation screen of the publisher portal
* Create an HMAC, which is then used to validate the signature, proving the validity of the passed returnUrl.

相同的程式碼稍微修改一下後，也適用於 productId 和 userId。

**產生 returnUrl 雜湊的 C# 程式碼**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**產生 returnUrl 雜湊的 NodeJS 程式碼**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> You need to [republish the developer portal](api-management-howto-developer-portal-customize.md#publish) for the delegation changes to take effect.

## <a name="next-steps"></a>後續步驟
如需委派的詳細資訊，請觀看以下影片：

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[要求單一登入 (SSO) 權杖]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/User/GenerateSsoUrl
[建立使用者]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user/createorupdate
[calling the REST API for subscriptions]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate
[Next steps]: #next-steps
[以下提供範例程式碼]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
