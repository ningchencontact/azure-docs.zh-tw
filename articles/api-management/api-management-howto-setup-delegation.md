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
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: 796bea3c64ef7fc03367707461d13e0ea2514b8b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59051746"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>如何委派使用者註冊和產品訂閱

委派可讓您使用您現有的網站來處理開發人員登入 / 註冊和產品，而不是在開發人員入口網站中使用的內建功能的訂用帳戶。 這樣可讓您的網站擁有使用者資料，並以自訂方式來執行這些步驟的驗證。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"> </a>委派開發人員登入並註冊

若要委托开发人员登录并注册现有网站，需要在该站点上创建一个特殊的委托终结点。 该终结点需要充当从 API 管理开发人员门户发起的任何此类请求的入口点。

最終工作流程如下所示：

1. 開發人員按下的滑鼠，在登入或註冊在 API 管理開發人員入口網站的連結
2. 瀏覽器重新導向至委派端點
3. 委派端點再轉將重新導向至或呈現 UI，要求使用者登入或註冊
4. 成功時，將使用者重新導向回到他們所來自的 API 管理開發人員入口網站頁面

首先，請設定 API 管理透過委派端點來傳遞要求。 在 API 管理發佈者入口網站中，按一下 [安全性]，然後按一下 [委派] 索引標籤。按一下核取方塊以啟用 [委派的登入與註冊]。

![Delegation page][api-management-delegation-signin-up]

* 決定特殊委派端點的 URL，並在 [ **Delegation endpoint URL** ] 欄位中輸入。 
* 在 [委派驗證金鑰] 欄位中輸入密碼，用來計算提供給您驗證的簽章，以確定要求確實來自 Azure API 管理。 您可以按一下 [產生] 按鈕，讓 API 管理為您隨機產生金鑰。

現在您需要建立「 **委派端點**」。 必須執行一些動作：

1. 接收下列形式的要求：
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn & returnUrl = {URL 來源網頁} & s = {字串} & s i g = {string}*
   > 
   > 
   
    查詢參數的登入/註冊案例：
   
   * **operation**：識別委派要求的類型 - 在此案例中只能是 **SignIn**
   * **returnUrl**： 頁面，其中使用者按下的登入或註冊連結的 URL
   * **salt**：特殊 salt 字串，用於計算安全性雜湊
   * **sig**：已經過計算的安全性雜湊，用於和您已計算的雜湊進行比較
2. 確認要求來自 Azure API 管理 (選擇性，但基於安全性理由，強烈建議這麼做)
   
   * 根據 **returnUrl** 和 **salt** 查詢參數，計算字串的 HMAC-SHA512 雜湊 ([以下提供範例程式碼])：
     
     > HMAC(**salt** + '\n' + **returnUrl**)
     > 
     > 
   * 将上面计算的哈希与 **sig** 查询参数的值进行比较。 如果兩個雜湊相符，則繼續下一步，否則拒絕要求。
3. 請確認您會接收要求，以便進行登入 / 註冊：**作業**查詢參數會設為 「**登入**"。
4. 顯示給使用者的登入或註冊 UI
5. 如果使用者要註冊，您必須在 API 管理中為他們建立對應的帳戶。 使用 API Management REST API [建立使用者]。 當這麼做，請確定您設定的使用者識別碼，與您的使用者存放區相同的值或您可以追蹤的識別碼。
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
* **userId**： 帳戶的使用者識別碼，管理
* **salt**：特殊 salt 字串，用於計算安全性雜湊
* **sig**：已經過計算的安全性雜湊，用於和您已計算的雜湊進行比較

## <a name="delegate-product-subscription"> </a>委派產品訂閱
委派產品訂閱的運作方式類似於委派使用者登入/註冊。 最終工作流程如下所示：

1. 开发人员在 API 管理开发人员门户中选择一个产品，并单击“订阅”按钮。
2. 浏览器将重定向到委托终结点。
3. 委托终结点执行所需的产品订阅步骤。 具体的步骤由你设计。 步骤可以包括重定向到另一个用于请求计费信息的页面、提出更多提问，或者只是存储信息而不要求执行任何用户操作

若要啟用此功能，請在 [委派] 頁面按一下 [委派產品訂用帳戶]。

接下来，确保委托终结点执行以下操作：

1. 接收下列形式的要求：
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation= {operation} & productId = {來訂閱產品} & userId = {提出要求的使用者} & s = {string} & s i g = {string}*
   >
   
    產品訂閱案例的查詢參數：
   
   * **operation**：識別委派要求的類型。 對於產品訂閱要求，有效的選項包括：
     * "Subscribe"：為使用者訂閱具有提供之識別碼的 (請參閱下面) 指定產品的要求
     * "Unsubscribe"：將為使用者取消訂閱產品的要求
     * "Renew"：訂閱續訂要求 (例如，可能過期)
   * **productId**：使用者要求訂閱之產品的識別碼
   * **subscriptionId**（*Unsubscribe* 和 *Renew*）中 - 产品订阅的 ID
   * **userId**：提出请求时所针对的用户的 ID
   * **salt**：特殊 salt 字串，用於計算安全性雜湊
   * **sig**：已經過計算的安全性雜湊，用於和您已計算的雜湊進行比較

2. 確認要求來自 Azure API 管理 (選擇性，但基於安全性理由，強烈建議這麼做)
   
   * 根据 **productId**、**userId** 和 **salt** 查询参数计算字符串的 HMAC-SHA512：
     
     > HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * 比較以上計算的雜湊和 **sig** 查詢參數的值。 如果兩個雜湊相符，則繼續下一步，否則拒絕要求。
3. 根据在 **operation** 中请求的操作类型（例如请求计费信息、提问更多问题，等等）处理产品订阅。
4. 當使用者在您這邊成功訂閱產品時， [呼叫 REST API 來訂閱產品]，讓使用者訂閱 API 管理產品。

## <a name="delegate-example-code"> </a> 範例程式碼

这些代码示例演示如何：

* 提取发布者门户的“委托”屏幕中设置的委托验证密钥
* 创建 HMAC，随后它将用于验证签名，以证实所传递的 returnUrl 的有效性。

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

## <a name="next-steps"></a>後續步驟
如需委派的詳細資訊，請觀看以下影片：

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[要求單一登入 (SSO) 權杖]: https://docs.microsoft.com/rest/api/apimanagement/User/GenerateSsoUrl
[建立使用者]: https://docs.microsoft.com/rest/api/apimanagement/user/createorupdate
[呼叫 REST API 來訂閱產品]: https://docs.microsoft.com/rest/api/apimanagement/productsubscriptions
[Next steps]: #next-steps
[以下提供範例程式碼]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
