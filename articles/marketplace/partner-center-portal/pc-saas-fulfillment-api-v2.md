---
title: SaaS 履行 API v2 |Azure Marketplace
description: 這篇文章說明如何建立和管理 Azure Marketplace 與 AppSource 上的 SaaS 供應項目使用相關聯的 「 履行 」 v2 Api。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: f5be0b8886500bdce50b95846826e5fdc53b5df1
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202671"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS 履行第 2 版的 Api 

這篇文章說明可讓合作夥伴銷售其 SaaS 應用程式，在 AppSource marketplace 和 Azure Marketplace 中的 Api。 這些 Api 是可交易的 SaaS 需求提供在 Azure Marketplace 與 AppSource 上。

## <a name="managing-the-saas-subscription-life-cycle"></a>管理 SaaS 訂用帳戶生命週期

Azure 的 SaaS 管理整個生命週期的 SaaS 訂用帳戶購買。 它使用 「 履行 」 Api 做為機制來驅動實際的 「 履行 」，變更為計劃和協力廠商的訂用帳戶刪除。 客戶的帳單根據 Microsoft 維護的 SaaS 訂用帳戶的狀態。 下圖說明狀態和磁碟機狀態之間的變更作業。

![SaaS 訂用帳戶生命週期狀態](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>SaaS 的訂用帳戶的狀態

下表列出 SaaS 訂用帳戶，包括每個描述和順序圖表，（如果適用） 的佈建狀態。 

#### <a name="provisioning"></a>佈建

當客戶啟動購買時，夥伴會接收授權碼會使用 URL 參數的客戶互動式網頁上的這項資訊。 例如， `https://contoso.com/signup?token=..`，而在合作夥伴中心內的登陸頁面 URL 是`https://contoso.com/signup`。 可以進行驗證並呼叫解決 API 之詳細資料的佈建服務交換授權碼。  當佈建完成一項 SaaS 服務時，它會傳送啟用呼叫，以表示 「 履行 」 已完成，而且客戶可以支付的費用。 

下圖顯示佈建案例的 API 呼叫的順序。  

![佈建一項 SaaS 服務的 API 呼叫](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>已佈建

此狀態是已佈建服務的穩定狀態。

##### <a name="provisioning-for-update"></a>佈建的更新 

此狀態表示已暫止的更新至現有的服務。 可以由客戶從 marketplace 或 SaaS 服務 （僅適用於直接對客戶交易） 上起始這類更新。

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>（當它從 marketplace 中起始），更新佈建

從 marketplace 起始更新時下, 圖顯示動作的順序。

![從 marketplace 起始更新時的 API 呼叫](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>（當它從 SaaS 服務中起始），更新佈建

下圖顯示的動作，從 SaaS 服務起始更新時。 （webhook 呼叫 SaaS 服務所初始化的訂用帳戶更新所取代）。 

![當更新從 SaaS 服務啟動時的 API 呼叫](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>暫止

此狀態表示客戶的付款未收到。 原則中，我們將提供客戶一個取消訂用帳戶之前的寬限期。 當訂用帳戶處於此狀態： 

- 身為合作夥伴，您可以選擇降低或封鎖使用者的存取權的服務。
- 訂用帳戶必須保持在可復原的狀態，可以還原完整的功能，不會遺失任何資料或設定值。 
- 預期結尾的寬限期內取得恢復要求履行 Api 透過此訂用帳戶或解除佈建的要求。 

#### <a name="unsubscribed"></a>取消訂閱 

訂用帳戶連線到此狀態，以明確的客戶要求，或是因為未付款的 dues 的回應。 從夥伴預期是客戶的資料時，保留復原要求的特定天數，並再刪除。 


## <a name="api-reference"></a>API 參考資料

本章節記載 SaaS*訂用帳戶 API*並*作業 API*。  值`api-version`參數，針對第 2 版 Api 是`2018-08-31`。  


### <a name="parameter-and-entity-definitions"></a>參數和實體的定義

下表列出常見的參數和履行 Api 所使用的實體的定義。

|     實體/參數     |     定義                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | SaaS 資源的 GUID 識別碼。  |
| `name`                   | 由客戶提供這項資源的易記名稱。 |
| `publisherId`            | 每個發行者的唯一字串識別項 (例如:"contoso")。 |
| `offerId`                | 每個優惠的唯一字串識別項 (例如:"offer1 」)。  |
| `planId`                 | 每個計劃/SKU 的唯一字串識別項 (例如: 「 銀級 」)。 |
| `operationId`            | 針對特定作業的 GUID 識別碼。  |
|  `action`                | 可能是正在執行的資源上的動作`subscribe`， `unsubscribe`， `suspend`， `reinstate`，或`changePlan`， `changeQuantity`， `transfer`。  |
|   |   |

全域唯一識別碼 ([Guid](https://en.wikipedia.org/wiki/Universally_unique_identifier)) 是通常自動產生的 128 位元 （32-十六進位） 數字。 

#### <a name="resolve-a-subscription"></a>解決訂用帳戶 

解決端點可讓發行者 marketplace 語彙基元解析至持續性的資源識別碼。 資源識別碼是 SaaS 訂用帳戶的唯一識別碼。 當使用者重新導向至協力廠商的網站時，URL 會包含查詢參數中的語彙基元。 夥伴必須使用此權杖，並提出要求，以解決此問題。 回應包含唯一的 SaaS 訂用帳戶識別碼、 名稱、 供應項目識別碼和資源的計劃。 此權杖是有效的只有一小時。 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>貼文<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |

*要求標頭：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，其中會產生並提供回應標頭中。 |
|  x-ms-correlationid |  用於用戶端作業的特殊字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。  |
|  authorization     |  [取得 JSON web 權杖 (JWT) 持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 |
|  x-ms-marketplace-token  |  在 URL 中，當使用者重新導向至 SaaS 合作夥伴的網站從 Azure 中的語彙基元的查詢參數 (例如： `https://contoso.com/signup?token=..`)。 *附註：* URL 解碼後才能使用它的瀏覽器中的權杖值。  |

*回應碼：*

程式碼：200<br>
SaaS 的訂用帳戶將解析的不透明的語彙基元。 回應內文：
 

```json
{
    "subscriptionId": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

程式碼：400<br>
不正確的要求。 x-ms-marketplace-語彙基元是遺失、 格式不正確，或已過期。

程式碼：403<br>
未經授權。 驗證權杖未提供或無效，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。

程式碼：404<br>
找不到。

程式碼：500<br>
內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>訂用帳戶 API

訂用帳戶的 API 支援 HTTPS 執行下列作業：**取得**， **Post**， **Patch**，和**刪除**。


#### <a name="list-subscriptions"></a>列出訂用帳戶

列出所有 SaaS 訂用帳戶供 「 發行者 」。

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>取得<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*查詢參數：*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  要用於此要求的作業版本。  |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，其中會產生並提供回應標頭中。 |
| x-ms-correlationid |  用於用戶端作業的特殊字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。  |
| authorization      |  [取得 JSON web 權杖 (JWT) 持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

*回應碼：*

程式碼：200 <br/>
取得發行者和發行者的所有供應項目，驗證權杖為基礎的對應訂用帳戶。
回應裝載：<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

接續權杖會有其他 「 頁面 」 的計劃，以擷取時，才存在。 

程式碼：403 <br>
未經授權。 驗證權杖未提供或無效，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。 

程式碼：500<br>
內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>取得訂用帳戶

取得指定的 SaaS 訂用帳戶。 您可以使用這個呼叫來取得授權資訊和計劃資訊。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>取得<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   取得解決透過解決 API 語彙基元之後的 SaaS 訂用帳戶的唯一識別碼。   |
|  ApiVersion        |   要用於此要求的作業版本。   |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，其中會產生並提供回應標頭中。 |
|  x-ms-correlationid |  用於用戶端作業的特殊字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。  |
|  authorization     |  [取得 JSON web 權杖 (JWT) 持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 |

*回應碼：*

程式碼：200<br>
取得識別項的 SaaS 訂用帳戶。 回應裝載：<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

程式碼：403<br>
未經授權。 驗證權杖未提供或無效，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。

程式碼：404<br>
找不到。<br> 

程式碼：500<br>
內部伺服器錯誤。<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>清單適用的方案

若要找出是否有任何私人或公用供應項目目前的發行者使用這個呼叫。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>取得<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   要用於此要求的作業版本。  |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，其中會產生並提供回應標頭中。 |
|  x-ms-correlationid  | 用於用戶端作業的特殊字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。 |
|  authorization     |  [取得 JSON web 權杖 (JWT) 持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 |

*回應碼：*

程式碼：200<br>
取得客戶一份可用的方案。 回應內文：

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

程式碼：404<br>
找不到。<br> 

程式碼：403<br>
未經授權。 驗證權杖未提供或無效，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。 <br> 

程式碼：500<br>
內部伺服器錯誤。<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>啟用訂用帳戶

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>貼文<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |
| subscriptionId     | 取得使用解決 API 解決語彙基元之後的 SaaS 訂用帳戶的唯一識別碼。  |

*要求標頭：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | 用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，其中會產生並提供回應標頭中。  |
|  x-ms-correlationid  | 用於用戶端作業的特殊字串值。 這個字串會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。  |
|  authorization     |  [取得 JSON web 權杖 (JWT) 持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 |

*要求承載：*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*回應碼：*

程式碼：200<br>
啟用訂用帳戶。<br>

程式碼：400<br>
不正確的要求： 驗證失敗。

程式碼：403<br>
未經授權。 驗證權杖未提供或無效，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。

程式碼：404<br>
找不到。

程式碼：500<br>
內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>變更訂用帳戶計劃

更新訂用帳戶方案。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>修補程式<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |
| subscriptionId     | 取得使用解決 API 解決語彙基元之後的 SaaS 訂用帳戶的唯一識別碼。  |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，其中會產生並提供回應標頭中。  |
|  x-ms-correlationid  |  用於用戶端作業的特殊字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。    |
| authorization      |  [取得 JSON web 權杖 (JWT) 持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

*要求承載：*

```json
Request Body:
{
    "planId": "gold"
}
```

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | 若要取得作業的狀態與資源連結。   |

*回應碼：*

程式碼：202<br>
若要變更方案的要求已經被接受。 合作夥伴必須輪詢作業位置判斷成功或失敗。 <br>

程式碼：400<br>
不正確的要求： 驗證失敗。

程式碼：403<br>
未經授權。 驗證權杖未提供或無效，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。

程式碼：404<br>
找不到。

程式碼：500<br>
內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>只有一次修補，不能同時，可以是計劃或數量。 編輯訂閱加上**更新**不在`allowedCustomerOperations`。

#### <a name="change-the-quantity-on-the-subscription"></a>變更訂用帳戶數量

更新訂用帳戶數量。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>修補程式：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |
| subscriptionId     | 取得使用解決 API 解決語彙基元之後的 SaaS 訂用帳戶的唯一識別碼。  |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，其中會產生並提供回應標頭中。  |
|  x-ms-correlationid  |  用於用戶端作業的特殊字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。    |
| authorization      |  [取得 JSON web 權杖 (JWT) 持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

*要求承載：*

```json
Request Body:
{
    "quantity": 5
}
```

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | 連結至資源，以取得作業的狀態。   |

*回應碼：*

程式碼：202<br>
變更數量的要求已經被接受。 合作夥伴必須輪詢作業位置判斷成功或失敗。 <br>

程式碼：400<br>
不正確的要求： 驗證失敗。


程式碼：403<br>
未經授權。 驗證權杖未提供或無效，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。

程式碼：404<br>
找不到。

程式碼：500<br>
內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>只有一次修補，不能同時，可以是計劃或數量。 編輯訂閱加上**更新**不在`allowedCustomerOperations`。

#### <a name="delete-a-subscription"></a>刪除訂用帳戶

取消訂閱，然後刪除指定的訂用帳戶。

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionid-api-versionapiversion"></a>Delete<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |
| subscriptionId     | 取得使用解決 API 解決語彙基元之後的 SaaS 訂用帳戶的唯一識別碼。  |

*要求標頭：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，其中會產生並提供回應標頭中。   |
|  x-ms-correlationid  |  用於用戶端作業的特殊字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。   |
|  authorization     |  [取得 JSON web 權杖 (JWT) 持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

*回應碼：*

程式碼：202<br>
夥伴起始的呼叫取消訂閱 SaaS 訂用帳戶。<br>

程式碼：400<br>
刪除訂閱加上**刪除**不是在`allowedCustomerOperations`。

程式碼：403<br>
未經授權。 驗證權杖未提供或無效，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。

程式碼：404<br>
找不到。

程式碼：500<br>
內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>作業 API

作業 API 支援下列的修補程式，以及如何取得作業。

#### <a name="list-outstanding-operations"></a>列出未完成的作業 

列出目前的發行者未完成的作業。 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>取得<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*查詢參數：*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   要用於此要求的作業版本。                |
| subscriptionId     | 取得使用解決 API 解決語彙基元之後的 SaaS 訂用帳戶的唯一識別碼。  |

*要求標頭：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，其中會產生並提供回應標頭中。  |
|  x-ms-correlationid |  用於用戶端作業的特殊字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。  |
|  authorization     |  [取得 JSON web 權杖 (JWT) 持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

*回應碼：*

程式碼：200<br> 取得暫止的訂用帳戶的作業清單。 回應裝載：

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```


程式碼：400<br>
不正確的要求： 驗證失敗。

程式碼：403<br>
未經授權。 驗證權杖未提供或無效，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。

程式碼：404<br>
找不到。

程式碼：500<br>
內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>取得作業狀態

可讓發行者追蹤指定觸發的非同步作業的狀態 (例如`subscribe`， `unsubscribe`， `changePlan`，或`changeQuantity`)。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>取得<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，其中會產生並提供回應標頭中。  |
|  x-ms-correlationid |  用於用戶端作業的特殊字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。  |
|  authorization     |  [取得 JSON web 權杖 (JWT) 持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

*回應碼：*<br>

程式碼：200<br> 請取得指定暫止的 SaaS 作業。 回應裝載：

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

程式碼：400<br>
不正確的要求： 驗證失敗。

程式碼：403<br>
未經授權。 驗證權杖未提供或無效，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。
 
程式碼：404<br>
找不到。

程式碼：500<br> 內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>更新作業的狀態

更新作業，以指示成功或失敗，以提供的值的狀態。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>修補程式<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  要用於此要求的作業版本。  |
| subscriptionId     | 取得使用解決 API 解決語彙基元之後的 SaaS 訂用帳戶的唯一識別碼。  |
|  operationId       | 要完成作業。 |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，其中會產生並提供回應標頭中。 |
|  x-ms-correlationid |  用於用戶端作業的特殊字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。 |
|  authorization     |  [取得 JSON web 權杖 (JWT) 持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  |

*要求承載：*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*回應碼：*

程式碼：200<br> 通知上的夥伴端的作業完成時呼叫。 例如，此回應可能表示基座或計劃的變更。

程式碼：400<br>
不正確的要求： 驗證失敗。

程式碼：403<br>
未經授權。 驗證權杖未提供或無效，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。

程式碼：404<br>
找不到。

程式碼：409<br>
發生衝突。 例如，已達到較新的交易。

程式碼：500<br> 內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>在 SaaS 服務上實作 webhook

「 發行者 」 必須實作以主動通知使用者變更其服務中的此 SaaS 服務中的 webhook。 SaaS 服務應該會呼叫 API 來驗證和授權才能對 webhook 通知採取動作的作業。

```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher’s name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
其中的動作可以是下列其中之一： 
- `subscribe` （當資源已啟用）
- `unsubscribe` （當資源已刪除）
- `changePlan` （當變更計劃作業已完成）
- `changeQuantity` （當變更數量作業已完成）
- `suspend` （當資源已暫止）
- `reinstate` （當資源具有已恢復暫停之後）

其中的狀態可以是下列其中之一： 
- **NotStarted** <br>
 - **InProgress** <br>
- **已成功** <br>
- **已失敗** <br>
- **Conflict** <br>

在 webhook 通知中，可採取動作的狀態都是**Succeeded**並**失敗**。 作業的生命週期是從**NotStarted**終端機的狀態，像是**Succeeded**， **Failed**，或**衝突**。 如果您收到**NotStarted**或是**InProgress**，繼續要求透過取得 API 的狀態，等到作業到達終止狀態之前採取動作。 

## <a name="mock-apis"></a>模擬 （mock) 的 Api

您可以使用我們的模擬 （mock) Api，可協助您開始進行開發，特別是原型設計，以及作為測試的專案。 

裝載端點： `https://marketplaceapi.microsoft.com/api` （不需要驗證）<br/>
API 版本： `2018-09-15`<br/>
範例 URI: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

API 端點路徑都相同模擬 （mock） 和實際的 Api，但不同的 API 版本。 版本是`2018-09-15`模擬 （mock） 的版本和`2018-08-31`實際執行版本。 

這篇文章中的 API 呼叫的任何可模擬 （mock） 的主應用程式端點。 一般情況下，會希望取得模擬 （mock） 的資料，傳回做為回應。 更新訂用帳戶上的方法的模擬 （mock) API 的呼叫一律會傳回 500。 

## <a name="next-steps"></a>後續步驟

開發人員可以也以程式設計方式擷取和操作工作負載、 供應項目，以及 「 發行者 」 設定檔，使用[Cloud Partner 入口網站 REST Api](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)。
