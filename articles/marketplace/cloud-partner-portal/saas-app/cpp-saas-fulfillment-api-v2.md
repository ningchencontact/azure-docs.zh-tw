---
title: SaaS 履行 API V2 |Azure Marketplace
description: 說明如何建立使用相關聯的 「 履行 」 V2 Api 的 Azure Marketplace 與 AppSource 上的 SaaS 供應項目。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: pabutler
ms.openlocfilehash: 4efd9556e255709204654cf0acbf1b08fa2c1fc0
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872145"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS 履行 Api 第 2 版 

本文詳細說明在 Azure Marketplace 與 AppSource 讓獨立軟體廠商 (Isv) 銷售其 SaaS 應用程式的 API。 此 API 是可交易的 SaaS 需求提供在 Azure Marketplace 與 AppSource 上。

## <a name="managing-the-saas-subscription-lifecycle"></a>管理 SaaS 訂用帳戶生命週期

Microsoft SaaS 服務會管理 SaaS 訂用帳戶購買的整個生命週期，並使用 「 履行 」 API，來驅動實際的 「 履行 」，一種機制變更計劃和 ISV 的訂用帳戶刪除。 客戶的計費是依 Microsoft 維護的 SaaS 訂用帳戶的狀態。 下圖說明狀態和磁碟機狀態之間的變更作業。

![SaaS 訂用帳戶生命週期狀態](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>SaaS 的訂用帳戶的狀態

下表列出 SaaS 訂用帳戶，包括每個描述和順序圖表，（如果適用） 的佈建狀態。 

#### <a name="provisioning"></a>佈建

當客戶啟動購買時，ISV 會使用 URL 參數的客戶互動式網頁上的驗證程式碼中收到這項資訊。 例如：`https://contoso.com/signup?token=..`登陸頁面的 URL 提供者，在合作夥伴中心所在`https://contoso.com/signup`。 可以進行驗證的驗證程式碼，並交換所需佈建方法是呼叫解決 API 之詳細資料。  當佈建完成 SaaS 服務時，它會傳送啟用呼叫，以表示 「 履行 」 已完成，而且客戶可以支付的費用。  下圖顯示佈建案例的 API 呼叫的順序。  

![API 呼叫來佈建一項 SaaS 服務。](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>已佈建

此狀態是已佈建服務的穩定狀態。

#### <a name="provisioning-for-update"></a>佈建的更新
（從 marketplace) 

此狀態表示更新的現有服務已暫止。 這類更新就會起始客戶在 marketplace 中，或在 SaaS 服務 （僅適用於直接支援客戶的交易。）從 marketplace 起始更新時下, 圖顯示的動作。

![從 marketplace 起始更新時，就會呼叫 API。](./media/saas-update-api-v2-calls-from-marketplace-a.png)

#### <a name="provisioning-for-update"></a>佈建的更新  
（從 SaaS 服務） 

下圖顯示的動作，當更新由 SaaS 服務。 （取代 SaaS 服務所初始化的訂用帳戶更新 webhook 呼叫。 

![更新由 SaaS 服務啟動時，就會呼叫 API。](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>擱置

此狀態表示客戶的付款未收到。 由原則，我們將提供客戶一段寬限期之前 unfulfilling 訂用帳戶。 當訂用帳戶處於此狀態： 

- 身為 ISV，您可以選擇降低或封鎖使用者的存取權的服務。 
- 訂用帳戶必須保持在可復原的狀態，可以還原完整的功能，不會遺失任何資料或設定值。 
- 您可以預期結尾的寬限期內取得恢復要求 「 履行 」 API，透過此訂用帳戶或解除佈建的要求。 

#### <a name="unsubscribed"></a>取消訂閱 

訂用帳戶連線到此狀態，以明確的客戶要求或回應給未支付會費的回應。 從 ISV 預期是客戶的資料時，保留復原 X 天的最少的要求，並再刪除。 


## <a name="api-reference"></a>API 參考

本章節記載 SaaS*訂用帳戶 API*並*作業 API*。  值`api-version`參數，針對第 2 版 Api 是`2018-08-31`。  


### <a name="parameter-and-entity-definitions"></a>參數和實體的定義

下表列出常見的參數和履行 Api 所使用的實體的定義。

|     實體/參數     |     定義                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | SaaS 資源的 GUID 識別碼  |
| `name`                   | 由客戶提供這項資源的易記名稱 |
| `publisherId`            | 每個 「 發行者 」，例如"contoso"的唯一字串識別項 |
| `offerId`                | 每個供應項目，例如"offer1 」 的唯一字串識別項  |
| `planId`                 | 每個計劃/sku，例如 「 銀級 」 的唯一字串識別項 |
| `operationId`            | 針對特定作業的 GUID 識別碼  |
|  `action`                | 可能是正在執行的資源上的動作`subscribe`， `unsubscribe`， `suspend`， `reinstate`，或`changePlan`， `changeQuantity`， `transfer`  |
|   |   |

全域唯一識別碼 ([Guid](https://en.wikipedia.org/wiki/Universally_unique_identifier)) 是通常自動產生的 128 位元 （32 個十六進位） 數字。 

#### <a name="resolve-a-subscription"></a>解決訂用帳戶 

解決端點可讓發行者 marketplace 語彙基元解析為永續性的資源識別碼。 資源識別碼是 SAAS 訂用帳戶的唯一識別碼。  使用者重新導向至 ISV 的網站時，URL 會包含查詢參數中的權杖。 ISV 必須使用此權杖，並提出要求，以解決此問題。 回應包含唯一的 SAAS 訂用帳戶識別碼、名稱、供應項目識別碼和資源方案。 此權杖僅在一小時內有效。 

**文章：<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`**

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本  |

*要求標頭：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  內容類型      | `application/json` |
|  x-ms-requestid    |  用於追蹤要求從用戶端，最好是一個 GUID 唯一的字串值。 如果未提供此值，則回應標頭中會產生並提供一個。 |
|  x-ms-correlationid |  在用戶端上的作業的唯一字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，則回應標頭中會產生並提供一個。  |
|  授權     |  [取得 JSON web 權杖 (JWT) 持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |
|  x-ms-marketplace-token  |  當使用者重新導向至 SaaS ISV 的網站從 Azure URL 中的語彙基元的查詢參數 (如例如： `https://contoso.com/signup?token=..`)。 *附註：* URL 解碼後才能使用它的瀏覽器中的權杖值。  |

*回應碼：*

密碼:200<br>
SaaS 的訂用帳戶將解析的不透明的語彙基元。<br>

```json
Response body:
{
    "subscriptionId": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

密碼:404<br>
找不到

密碼:400<br>
不正確的要求。 x-ms-marketplace-語彙基元是遺失、 格式不正確或已過期。

密碼:403<br>
未經授權。 驗證權杖未提供，不正確，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。

密碼:500<br>
內部伺服器錯誤

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

**取得此項目：<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`**

*查詢參數：*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  要用於此要求的作業版本。  |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
| 內容類型       |  `application/json`  |
| x-ms-requestid     |  用於追蹤要求從用戶端，最好是一個 GUID 唯一的字串值。 如果未提供此值，則回應標頭中會產生並提供一個。 |
| x-ms-correlationid |  在用戶端上的作業的唯一字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。  |
| 授權      |  [取得 JSON web 權杖 (JWT) 持有人權杖。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*回應碼：*

密碼:200 <br/>
根據驗證權杖，取得 「 發行者 」 與對應的訂用帳戶的所有發行者的供應項目。<br> 回應裝載：<br>

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
          ], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. [Provisioning, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

接續權杖才會存在，如果有其他 「 頁面 」 的計劃，以擷取。 

密碼:403 <br>
未經授權。 驗證權杖未提供，不正確，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。 

密碼:500 內部伺服器錯誤

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

**取得此項目：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`**

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   取得解決透過解決 API 語彙基元之後的 SaaS 訂用帳戶的唯一識別碼   |
|  ApiVersion        |   要用於此要求的作業版本   |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|  內容類型      |  `application/json`  |
|  x-ms-requestid    |  用於追蹤要求從用戶端，最好是一個 GUID 唯一的字串值。 如果未提供此值，則回應標頭中會產生並提供一個。 |
|  x-ms-correlationid |  在用戶端上的作業的唯一字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。  |
|  授權     |  [取得 JSON web 權杖 (JWT) 持有人權杖。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*回應碼：*

密碼:200<br>
從識別碼取得 SaaS 訂用帳戶<br> 回應裝載：<br>

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
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

密碼:404<br>
找不到<br> 

密碼:403<br>
未經授權。 驗證權杖未提供，不正確，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。

密碼:500<br>
內部伺服器錯誤<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>清單適用的方案

若要找出是否有任何私密/公開金鑰供應項目目前的發行者使用這個呼叫。

**取得此項目：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`**

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   要用於此要求的作業版本  |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|   內容類型     |  `application/json` |
|   x-ms-requestid   |   用於追蹤要求從用戶端，最好是一個 GUID 唯一的字串值。 如果未提供此值，則回應標頭中會產生並提供一個。 |
|  x-ms-correlationid  | 在用戶端上的作業的唯一字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，則回應標頭中會產生並提供一個。 |
|  授權     |  [取得 JSON web 權杖 (JWT) 持有人權杖。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*回應碼：*

密碼:200<br>
取得客戶一份可用的方案。<br>

回應內文：

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

密碼:404<br>
找不到<br> 

密碼:403<br>
未經授權。 驗證權杖未提供，不正確，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。 <br> 

密碼:500<br>
內部伺服器錯誤<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>啟用訂用帳戶

**文章：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`**

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本  |
| subscriptionId     | 取得解決使用解決 API 語彙基元之後的 SaaS 訂用帳戶的唯一識別碼  |

*要求標頭：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  內容類型      | `application/json`  |
|  x-ms-requestid    | 用於追蹤要求從用戶端，最好是一個 GUID 唯一的字串值。 如果未提供此值，則回應標頭中會產生並提供一個。  |
|  x-ms-correlationid  | 在用戶端上的作業的唯一字串值。 這個字串會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。  |
|  授權     |  [取得 JSON web 權杖 (JWT) 持有人權杖。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*要求：*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*回應碼：*

密碼:200<br>
啟用訂用帳戶。<br>

密碼:404<br>
找不到

密碼:400<br>
不正確的要求驗證失敗

密碼:403<br>
未經授權。 驗證權杖未提供，不正確，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。

密碼:500<br>
內部伺服器錯誤

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

**修補程式：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |
| subscriptionId     | 取得解決使用解決 API 語彙基元之後的 SaaS 訂用帳戶的唯一識別碼。  |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|  內容類型      | `application/json` |
|  x-ms-requestid    |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則回應標頭中會產生並提供一個。  |
|  x-ms-correlationid  |  用於用戶端作業的特殊字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。    |
| 授權      |  [取得 JSON web 權杖 (JWT) 持有人權杖。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

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
| Operation-Location | 連結至資源，以取得作業的狀態。   |

*回應碼：*

密碼:202<br>
若要變更方案的要求已經被接受。 ISV 要輪詢作業位置判斷成功/失敗。 <br>

密碼:404<br>
找不到

密碼:400<br>
不正確的要求驗證失敗。

>[!Note]
>只有一次修補，不能同時，可以是計劃或數量。 編輯訂閱加上**更新**不在`allowedCustomerOperations`。

密碼:403<br>
未經授權。 驗證權杖未提供，不正確，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。

密碼:500<br>
內部伺服器錯誤

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-quantity-on-the-subscription"></a>變更訂用帳戶數量

更新訂用帳戶數量。

**修補程式：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |
| subscriptionId     | 取得解決使用解決 API 語彙基元之後的 SaaS 訂用帳戶的唯一識別碼。  |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|  內容類型      | `application/json` |
|  x-ms-requestid    |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則回應標頭中會產生並提供一個。  |
|  x-ms-correlationid  |  用於用戶端作業的特殊字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。    |
| 授權      |  [取得 JSON web 權杖 (JWT) 持有人權杖。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

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

密碼:202<br>
已接受。 變更數量的要求已經被接受。 ISV 要輪詢作業位置判斷成功/失敗。 <br>

密碼:404<br>
找不到

密碼:400<br>
不正確的要求驗證失敗。

>[!Note]
>只有一次修補，不能同時，可以是計劃或數量。 編輯訂閱加上**更新**不在`allowedCustomerOperations`。

密碼:403<br>
未經授權。 驗證權杖未提供，不正確，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。

密碼:500<br>
內部伺服器錯誤

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="delete-a-subscription"></a>刪除訂閱

取消訂閱，然後刪除指定的訂用帳戶。

**刪除：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`**

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |
| subscriptionId     | 取得解決使用解決 API 語彙基元之後的 SaaS 訂用帳戶的唯一識別碼。  |

*要求標頭：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   內容類型     |  `application/json` |
|  x-ms-requestid    |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，其中會產生並提供回應標頭中。   |
|  x-ms-correlationid  |  用於用戶端作業的特殊字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。   |
|  授權     |  [取得 JSON web 權杖 (JWT) 持有人權杖。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*回應碼：*

密碼:202<br>
表示起始的 ISV 呼叫取消訂閱 SaaS 訂用帳戶。<br>

密碼:404<br>
找不到

密碼:400<br>
刪除訂閱加上**刪除**不是在`allowedCustomerOperations`。

密碼:403<br>
未經授權。 驗證權杖未提供，不正確，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。

密碼:500<br>
內部伺服器錯誤

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

**取得此項目：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`**

*查詢參數：*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   要用於此要求的作業版本。                |
| subscriptionId     | 取得解決使用解決 API 語彙基元之後的 SaaS 訂用帳戶的唯一識別碼。  |

*要求標頭：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   內容類型     |  `application/json` |
|  x-ms-requestid    |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則回應標頭中會產生並提供一個。  |
|  x-ms-correlationid |  用於用戶端作業的特殊字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。  |
|  授權     |  [取得 JSON web 權杖 (JWT) 持有人權杖。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*回應碼：*

密碼:200<br> 取得暫止的訂用帳戶的作業清單。<br>
回應裝載：

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

密碼:404<br>
找不到

密碼:400<br>
不正確的要求驗證失敗

密碼:403<br>
未經授權。 驗證權杖未提供，不正確，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。

密碼:500<br>
內部伺服器錯誤

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>取得作業狀態

可讓發行者追蹤指定觸發的非同步作業的狀態 (訂閱 / 取消訂閱] / [變更計劃 / 變更數量)。

**取得此項目：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|  內容類型      |  `application/json`   |
|  x-ms-requestid    |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則回應標頭中會產生並提供一個。  |
|  x-ms-correlationid |  用於用戶端作業的特殊字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。  |
|  授權     |[取得 JSON web 權杖 (JWT) 持有人權杖。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*回應碼：* 密碼:200<br> 取得指定的暫止的 SaaS 作業<br>
回應裝載：

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

密碼:404<br>
找不到

密碼:400<br>
不正確的要求驗證失敗

密碼:403<br>
未經授權。 驗證權杖未提供，不正確，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。
 
密碼:500<br> 內部伺服器錯誤

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>更新作業的狀態

更新作業，以表示成功/失敗，以提供的值的狀態。

**修補程式：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  要用於此要求的作業版本。  |
| subscriptionId     | 取得解決使用解決 API 語彙基元之後的 SaaS 訂用帳戶的唯一識別碼。  |
|  operationId       | 要完成作業。 |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|   內容類型     | `application/json`   |
|   x-ms-requestid   |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則回應標頭中會產生並提供一個。 |
|  x-ms-correlationid |  用於用戶端作業的特殊字串值。 此參數會將相互關聯來自用戶端作業的所有事件與伺服器端上的事件。 如果未提供此值，其中會產生並提供回應標頭中。 |
|  授權     |  [取得 JSON web 權杖 (JWT) 持有人權杖。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*要求承載：*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*回應碼：*

密碼:200<br> 呼叫以通知 ISV 端作業的完成。 例如，此回應可能表示基座/計劃的變更。

密碼:404<br>
找不到

密碼:400<br>
不正確的要求驗證失敗

密碼:403<br>
未經授權。 驗證權杖未提供，不正確，或要求嘗試存取不屬於目前的 「 發行者 」 的併購。

密碼:409<br>
發生衝突。 比方說，是已達到較新的交易

密碼:500<br> 內部伺服器錯誤

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="webhook-on-the-saas-service"></a>在 SaaS 服務的 Webhook

「 發行者 」 必須實作以主動通知使用者變更其服務中的此 SaaS 服務中的 webhook。 API 應該是未經驗證，並將 Microsoft SaaS 服務所呼叫。 SaaS 服務應該會呼叫 API 來驗證和授權才能對 webhook 通知採取動作的作業。

```json
{
    "operationId": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",
    "planId": "silver",
    "quantity": "20"  ,
    "action": "Subscribe",
    "timeStamp": "2018-12-01T00:00:00"
}
```

其中可以是其中一個動作： 
- `Subscribe`  （當資源已啟用）
- `Unsubscribe` （當資源已刪除）
- `ChangePlan` （當變更計劃作業已完成）
- `ChangeQuantity` （當變更數量作業已完成）
- `Suspend` （當資源已暫止）
- `Reinstate` （當資源具有已恢復暫停之後）


## <a name="mock-api"></a>模擬 （mock) 的 API

您可以使用我們的模擬 （mock) Api，可協助您開始進行開發，特別是原型設計和測試專案。 

主機的端點： `https://marketplaceapi.microsoft.com/api` <br/>
API 版本： `2018-09-15` <br/>
不需要驗證 <br/>
範例 Uri: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

API 端點路徑都相同模擬及實際的 Api，但不同的 API 版本。 此版本為 2018年-09-15 的模擬及實際執行版本 2018年-08-31。 

這篇文章中的 API 呼叫的任何可模擬 （mock） 的主應用程式端點。 您可以預期取得模擬 （mock） 的資料，傳回做為回應。 一般情況下，您可以預期取得模擬 （mock） 的資料，傳回做為回應。 更新訂用帳戶上的方法的模擬 （mock) API 的呼叫一律會傳回 500。 

## <a name="next-steps"></a>後續步驟

開發人員可以也以程式設計方式擷取和操作工作負載、 供應項目，以及 「 發行者 」 設定檔使用[Cloud Partner 入口網站 REST Api](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)。
