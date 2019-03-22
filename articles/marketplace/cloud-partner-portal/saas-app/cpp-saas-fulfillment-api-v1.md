---
title: SaaS 履行 API V1 Azure Marketplace |Microsoft Docs
description: 說明如何建立使用相關聯的 「 履行 」 V1 Api 在 Azure Marketplace 上的 SaaS 供應項目。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 02/27/2019
ms.author: pbutlerm
ms.openlocfilehash: d9443349ea7ce91a3b8ab01510917bc82ae9b8ad
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316128"
---
# <a name="saas-fulfillment-apis-version-1"></a>SaaS 履行 Api 第 1 版

本文說明如何使用 API 建立 SaaS 供應項目。 如果您已選取 [透過 Azure 銷售]，則必須使用 API 才能允許您 SaaS 供應項目的訂用帳戶。  

> [!WARNING]
> 這個初始版本 SaaS 履行 API 已被取代;請改用[SaaS 履行 API V2](./cpp-saas-fulfillment-api-v2.md)。


本文分成兩部分：

-   SaaS 服務和 Azure Marketplace 之間的服務對服務驗證
-   API 方法和端點

下列 API 可協助您將 SaaS 服務和 Azure 整合：

-   解決
-   訂閱
-   轉換
-   取消訂閱

下圖顯示新客戶的訂閱流程和使用這些 API 的時間：

![SaaS 供應項目的 API 流程](./media/saas-offer-publish-api-flow-v1.png)


## <a name="service-to-service-authentication-between-saas-service-and-azure-marketplace"></a>SaaS 服務和 Azure Marketplace 之間的服務對服務驗證

Azure 不會對 SaaS 服務向終端使用者顯示的驗證施加任何限制式。 不過，若為與 Azure Marketplace API 進行通訊的 SaaS 服務 ，則驗證會在 Azure Active Directory (Azure AD) 應用程式的內容中完成。

下一節會說明如何建立 Azure AD 應用程式。


### <a name="register-an-azure-ad-application"></a>註冊 Azure AD 應用程式

任何想要使用 Azure AD 功能的應用程式都必須先在 Azure AD 租用戶中註冊。 此註冊程序包含提供應用程式的 Azure AD 相關詳細資料，例如其所在的 URL、要在使用者經驗證後傳送回應的 URL，以及可識別應用程式的 URI 等。

若要使用 Azure 入口網站註冊新的應用程式，請執行下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 如果您的帳戶可讓您存取多個帳戶，請在右上角按一下您的帳戶，並將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
3. 在左側導覽窗格中，依序按一下 [Azure Active Directory] 服務、[應用程式註冊] 和 [新增應用程式註冊]。

   ![SaaS AD 應用程式註冊](./media/saas-offer-app-registration-v1.png)

4. 在 [建立] 頁面中，輸入您的應用程式\'註冊資訊：
   - **名稱**：請輸入有意義的應用程式名稱
   - **應用程式類型**： 
     - 針對在裝置本機上安裝的[用戶端應用程式](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application)選取 [原生]。 此設定適用於 OAuth 公用[原生用戶端](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client)。
     - 針對安裝在安全伺服器上的[用戶端應用程式](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application)和[資源/API 應用程式](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server)選取 [Web 應用程式/API]。 此設定適用於 OAuth 機密 [Web 用戶端](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client)和公用[使用者代理程式型用戶端](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client)。
     相同的應用程式也可以公開用戶端和資源/API。
   - **登入 URL**：針對 Web 應用程式/API 應用程式，請提供應用程式的基底 URL。 例如， **http:\//localhost:31544**可能是您本機電腦上執行的 web 應用程式的 URL。 接著，使用者會使用此 URL 來登入 Web 用戶端應用程式。
   - **重新導向 URI**：針對原生應用程式，請提供 Azure AD 用來傳回權杖回應的 URI。 您的應用程式中，輸入特定的值，例如**http:\//MyFirstAADApp**。

     ![SaaS AD 應用程式註冊](./media/saas-offer-app-registration-v1-2.png)

     如需 web 應用程式或原生應用程式的特定範例，請參閱快速入門引導式的 [開始] 區段中可用的設定[Azure AD 開發人員指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)。

5. 完成後，按一下 [建立]。 Azure AD 會將唯一的應用程式識別碼指派給您的應用程式，然後系統會帶您\'進入應用程式\'的主要註冊頁面。 根據您的應用程式是 Web 還是原生應用程式，系統會提供您不同選項以供您在應用程式中新增其他功能。

>[!Note]
>根據預設，新註冊的應用程式被設定為只允許使用者從相同的租用戶登入您的應用程式。

<a name="api-methods-and-endpoints"></a>API 方法和端點
-------------------------

下列區段說明適用於啟用 SaaS 供應項目之訂用帳戶的 API 方法和端點。

### <a name="get-a-token-based-on-the-azure-ad-app"></a>取得以 Azure AD 應用程式為基礎的權杖

HTTP 方法

`GET`

*Request URL (要求 URL)*

**https://login.microsoftonline.com/*{tenantId}*/oauth2/token**

*URI 參數*

|  **參數名稱**  | **必要**  | **說明**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | 已註冊的 AAD 應用程式租用戶識別碼   |
|  |  |  |


*要求標頭*

|  **標頭名稱**  | **必要** |  **說明**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True         | 與要求相關聯的內容類型。 預設值為 `application/x-www-form-urlencoded`。  |
|  |  |  |


*要求本文*

| **屬性名稱**   | **必要** |  **說明**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | 授與類型。 預設值為 `client_credentials`。                    |
|  Client_id          | True         |  與 Azure AD 應用程式相關聯的用戶端/應用程式識別碼。                  |
|  client_secret      | True         |  與 Azure AD 應用程式相關聯的密碼。                               |
|  資源           | True         |  要求權杖的目標資源。 預設值為 `62d94f6c-d599-489b-a797-3e10e42fbe22`。 |
|  |  |  |


*回應*

|  **名稱**  | **類型**       |  **說明**    |
| ---------- | -------------  | ------------------- |
| 200 確定    | TokenResponse  | 要求成功   |
|  |  |  |

*TokenResponse*

權杖回應範例：

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```

### <a name="marketplace-api-endpoint-and-api-version"></a>Marketplace API 端點和 API 版本

Azure Marketplace API 的端點為 `https://marketplaceapi.microsoft.com`。

目前的 API 版本為 `api-version=2017-04-15`。


### <a name="resolve-subscription"></a>解析訂用帳戶

解析端點上的 POST 動作可讓使用者解析持續性資源識別碼的 Marketplace 權杖。  資源識別碼是 SAAS 訂用帳戶的唯一識別碼。 

使用者重新導向至 ISV 的網站時，URL 會包含查詢參數中的權杖。 ISV 必須使用此權杖，並提出要求，以解決此問題。 回應包含唯一的 SAAS 訂用帳戶識別碼、名稱、供應項目識別碼和資源方案。 此權杖僅在一小時內有效。

*要求*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **參數名稱** |     **說明**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  api-version        |  要用於此要求的作業版本。   |
|  |  |


*標頭*

| **標頭索引鍵**     | **必要** | **說明**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | 否           | 用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則回應標頭中會產生並提供一個。  |
| x-ms-correlationid | 否           | 用於用戶端作業的特殊字串值。 這會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。 如果未提供此值，則回應標頭中會產生並提供一個。 |
| Content-Type       | 是          | `application/json`                                        |
| 授權      | 是          | JSON Web 權杖 (JWT) 持有人權杖。                    |
| x-ms-marketplace-token| 是| 使用者從 Azure 重新導向至 SaaS ISV 的網站時，在 URL 中的權杖查詢參數。 **附註：** 此權杖的效力只有 1 小時。 此外，URL 在使用瀏覽器的權杖值之前，會先將其解碼。|
|  |  |  |
  

*回應主體*

``` json
{
    "id": "",
    "subscriptionName": "",
    "offerId": "",
    "planId": "",
}
```

| **參數名稱** | **資料類型** | **說明**                       |
|--------------------|---------------|---------------------------------------|
| id                 | 字串        | SaaS 訂用帳戶的識別碼。          |
| subscriptionName| 字串| 使用者訂閱 SaaS 服務時在 Azure 中設定的 SaaS 訂用帳戶名稱。|
| OfferId            | 字串        | 使用者訂閱的供應項目識別碼。 |
| planId             | 字串        | 使用者訂閱的方案識別碼。  |
|  |  |  |


*回應碼*

| **HTTP 狀態碼** | **錯誤碼**     | **說明**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | 成功解析權杖。                                                            |
| 400                  | `BadRequest`         | 要求的標頭遺失或指定的 API 版本不正確。 無法解析權杖，因為其格式不正確或已過期 (此權杖在產生後只有 1 小時的效力)。 |
| 403                  | `Forbidden`          | 呼叫者沒有權限執行此作業。                                 |
| 429                  | `RequestThrottleId`  | 服務正忙於處理多個要求，請稍後重試。                                |
| 503                  | `ServiceUnavailable` | 服務已關閉，請稍後重試。                                        |
|  |  |  |


*回應標頭*

| **標頭索引鍵**     | **必要** | **說明**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 是          | 從用戶端收到的要求識別碼。                                                                   |
| x-ms-correlationid | 是          | 由用戶端傳遞時為相互關聯識別碼，否則此值為伺服器相互關聯識別碼。                   |
| x-ms-activityid    | 是          | 用於追蹤服務要求的特殊字串值。 此值可用於任何核對作業。 |
| Retry-after        | 否           | 此值只針對 429 回應設定。                                                                   |
|  |  |  |


### <a name="subscribe"></a>訂閱

訂閱端點可讓使用者針對指定方案開始訂閱 SaaS 服務，並在商務系統中啟用計費功能。

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **參數名稱**  | **說明**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | 透過 Resolve API 解析權杖後所取得的 SaaS 訂用帳戶唯一識別碼。                              |
| api-version         | 要用於此要求的作業版本。 |
|  |  |

*標頭*

|  **標頭索引鍵**        | **必要** |  **說明**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   否         | 用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則系統會產生一個並提供於回應標頭中。 |
| x-ms-correlationid     |   否         | 用於用戶端作業的特殊字串值。 這會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。 如果未提供此值，則系統會產生一個並提供於回應標頭中。 |
| If-Match/If-None-Match |   否         |   驗證程式 Etag 值強度。                                                          |
| Content-Type           |   是        |    `application/json`                                                                   |
|  授權         |   是        |    JSON Web 權杖 (JWT) 持有人權杖。                                               |
| x-ms-marketplace-session-mode| 否 | 可在訂閱 SaaS 供應項目時啟用測試模式的標記。 若有設定，該訂閱將無須支付費用。 這對於 ISV 測試方式非常實用。 請將它設定為 **「dryrun」**|
|  |  |  |

*內文*

``` json
{
    "lanId": "",
}
```

| **元素名稱** | **資料類型** | **說明**                      |
|------------------|---------------|--------------------------------------|
| planId           | (必要) 字串        | 使用者訂閱的 SaaS 服務方案識別碼。  |
|  |  |  |

*回應碼*

| **HTTP 狀態碼** | **錯誤碼**     | **說明**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | 針對指定方案收到的 SaaS 訂用帳戶啟用。                   |
| 400                  | `BadRequest`         | 必要標頭遺失或 JSON 主體的格式不正確。 |
| 403                  | `Forbidden`          | 呼叫者沒有權限執行此作業。                   |
| 404                  | `NotFound`           | 找不到具有該指定識別碼的訂用帳戶                                  |
| 409                  | `Conflict`           | 訂用帳戶上有其他作業正在進行中。                     |
| 429                  | `RequestThrottleId`  | 服務正忙於處理多個要求，請稍後重試。                  |
| 503                  | `ServiceUnavailable` | 服務已關閉，請稍後重試。                          |
|  |  |  |

如果回應碼為 202，請追蹤「Operation-location 」 標頭的要求作業狀態。 驗證作業與其他 Marketplace API 相同。

*回應標頭*

| **標頭索引鍵**     | **必要** | **說明**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 是          | 從用戶端收到的要求識別碼。                                                                   |
| x-ms-correlationid | 是          | 由用戶端傳遞時為相互關聯識別碼，否則此值為伺服器相互關聯識別碼。                   |
| x-ms-activityid    | 是          | 用於追蹤服務要求的特殊字串值。 此值可用於任何核對作業。 |
| Retry-after        | 是          | 用戶端可用來檢查狀態的間隔。                                                       |
| Operation-Location | 是          | 用於取得作業狀態的資源連結。                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>變更方案端點

變更端點可讓使用者將其目前訂閱的方案轉換成新的方案。

**PATCH**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **參數名稱**  | **說明**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | SaaS 訂用帳戶識別碼。                              |
| api-version         | 要用於此要求的作業版本。 |
|  |  |

*標頭*

| **標頭索引鍵**          | **必要** | **說明**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | 否           | 用於追蹤用戶端要求的特殊字串值。 建議的 GUID。 如果未提供此值，則系統會產生一個並提供於回應標頭中。   |
| x-ms-correlationid      | 否           | 用於用戶端作業的特殊字串值。 這會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。 如果未提供此值，則系統會產生一個並提供於回應標頭中。 |
| If-Match/If-None-Match | 否           | 驗證程式 Etag 值強度。                              |
| Content-Type            | 是          | `application/json`                                        |
| 授權           | 是          | JSON Web 權杖 (JWT) 持有人權杖。                    |
|  |  |  |

*內文*

```json
{
    "planId": ""
}
```

|  **元素名稱** |  **資料類型**  | **說明**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  (必要) 字串         | 使用者訂閱的 SaaS 服務方案識別碼。          |
|  |  |  |

*回應碼*

| **HTTP 狀態碼** | **錯誤碼**     | **說明**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | 針對指定方案收到的 SaaS 訂用帳戶啟用。                   |
| 400                  | `BadRequest`         | 必要標頭遺失或 JSON 主體的格式不正確。 |
| 403                  | `Forbidden`          | 呼叫者沒有權限執行此作業。                   |
| 404                  | `NotFound`           | 找不到具有該指定識別碼的訂用帳戶                                  |
| 409                  | `Conflict`           | 訂用帳戶上有其他作業正在進行中。                     |
| 429                  | `RequestThrottleId`  | 服務正忙於處理多個要求，請稍後重試。                  |
| 503                  | `ServiceUnavailable` | 服務已關閉，請稍後重試。                          |
|  |  |  |

*回應標頭*

| **標頭索引鍵**     | **必要** | **說明**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 是          | 從用戶端收到的要求識別碼。                                                                   |
| x-ms-correlationid | 是          | 由用戶端傳遞時為相互關聯識別碼，否則此值為伺服器相互關聯識別碼。                   |
| x-ms-activityid    | 是          | 用於追蹤服務要求的特殊字串值。 此值可用於任何核對作業。 |
| Retry-after        | 是          | 用戶端可用來檢查狀態的間隔。                                                       |
| Operation-Location | 是          | 用於取得作業狀態的資源連結。                                                        |
|  |  |  |

### <a name="delete-subscription"></a>刪除訂用帳戶

訂閱端點上的刪除動作，可讓使用者刪除具有指定識別碼的訂用帳戶。

*要求*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **參數名稱**  | **說明**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | SaaS 訂用帳戶識別碼。                              |
| api-version         | 要用於此要求的作業版本。 |
|  |  |

*標頭*

| **標頭索引鍵**     | **必要** | **說明**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | 否           | 用於追蹤用戶端要求的特殊字串值。 建議的 GUID。 如果未提供此值，則回應標頭中會產生並提供一個。                                                           |
| x-ms-correlationid | 否           | 用於用戶端作業的特殊字串值。 這會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。 如果未提供此值，則系統會產生一個並提供於回應標頭中。 |
| 授權      | 是          | JSON Web 權杖 (JWT) 持有人權杖。                    |
|  |  |  |

*回應碼*

| **HTTP 狀態碼** | **錯誤碼**     | **說明**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | 針對指定方案收到的 SaaS 訂用帳戶啟用。                   |
| 400                  | `BadRequest`         | 必要標頭遺失或 JSON 主體的格式不正確。 |
| 403                  | `Forbidden`          | 呼叫者沒有權限執行此作業。                   |
| 404                  | `NotFound`           | 找不到具有該指定識別碼的訂用帳戶                                  |
| 429                  | `RequestThrottleId`  | 服務正忙於處理多個要求，請稍後重試。                  |
| 503                  | `ServiceUnavailable` | 服務已暫時關閉。 請稍後重試。                          |
|  |  |  |

如果回應碼為 202，請追蹤「Operation-location 」 標頭的要求作業狀態。 驗證作業與其他 Marketplace API 相同。

*回應標頭*

| **標頭索引鍵**     | **必要** | **說明**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 是          | 從用戶端收到的要求識別碼。                                                                   |
| x-ms-correlationid | 是          | 由用戶端傳遞時為相互關聯識別碼，否則此值為伺服器相互關聯識別碼。                   |
| x-ms-activityid    | 是          | 用於追蹤服務要求的特殊字串值。 此值可用於任何核對作業。 |
| Retry-after        | 是          | 用戶端可用來檢查狀態的間隔。                                                       |
| Operation-Location | 是          | 用於取得作業狀態的資源連結。                                                        |
|   |  |  |

### <a name="get-operation-status"></a>取得作業狀態

此端點可讓使用者追蹤已觸發的非同步作業狀態 (訂閱/取消訂閱/變更方案)。

*要求*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{operationId}*?api-version=2017-04-15**

| **參數名稱**  | **說明**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | 已觸發作業的唯一識別碼。                |
| api-version         | 要用於此要求的作業版本。 |
|  |  |

*標頭*

| **標頭索引鍵**     | **必要** | **說明**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 否           | 用於追蹤用戶端要求的特殊字串值。 建議的 GUID。 如果未提供此值，則回應標頭中會產生並提供一個。   |
| x-ms-correlationid | 否           | 用於用戶端作業的特殊字串值。 這會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。 如果未提供此值，則回應標頭中會產生並提供一個。  |
| 授權      | 是          | JSON Web 權杖 (JWT) 持有人權杖。                    |
|  |  |  | 

*回應主體*

```json
{
    "id": "",
    "status": "",
    "resourceLocation": "",
    "created": "",
    "lastModified": ""
}
```

| **參數名稱** | **資料類型** | **說明**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | 字串        | 作業的識別碼。                                                                      |
| status             | 例舉          | 下列其中之一的作業狀態：`In Progress`、 `Succeeded` 或 `Failed`。          |
| resourceLocation   | 字串        | 已建立或修改之訂用帳戶的連結。 此連結可協助用戶端取得更新的狀態後續作業。 此值並未針對 `Unsubscribe` 作業進行設定。 |
| created            | DateTime      | 作業建立時間 (UTC)。                                                           |
| lastModified       | DateTime      | 作業上次更新時間 (UTC)。                                                      |
|  |  |  |

*回應碼*

| **HTTP 狀態碼** | **錯誤碼**     | **說明**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | 已成功解析 get 要求，且主體包含回應。    |
| 400                  | `BadRequest`         | 要求的標頭遺失或指定的 API 版本不正確。 |
| 403                  | `Forbidden`          | 呼叫者沒有權限執行此作業。                      |
| 404                  | `NotFound`           | 找不到具有該指定識別碼的訂用帳戶                                     |
| 429                  | `RequestThrottleId`  | 服務正忙於處理多個要求，請稍後重試。                     |
| 503                  | `ServiceUnavailable` | 服務已關閉，請稍後重試。                             |
|  |  |  |

*回應標頭*

| **標頭索引鍵**     | **必要** | **說明**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 是          | 從用戶端收到的要求識別碼。                                                                   |
| x-ms-correlationid | 是          | 由用戶端傳遞時為相互關聯識別碼，否則此值為伺服器相互關聯識別碼。                   |
| x-ms-activityid    | 是          | 用於追蹤服務要求的特殊字串值。 此值可用於任何核對作業。 |
| Retry-after        | 是          | 用戶端可用來檢查狀態的間隔。                                                       |
|  |  |  |

### <a name="get-subscription"></a>取得訂用帳戶

訂閱端點上的「get」動作可讓使用者取出具有指定資源識別碼的訂用帳戶。

*要求*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **參數名稱**  | **說明**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | SaaS 訂用帳戶識別碼。                              |
| api-version         | 要用於此要求的作業版本。 |
|  |  |

*標頭*

| **標頭索引鍵**     | **必要** | **說明**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 否           | 用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則回應標頭中會產生並提供一個。                                                           |
| x-ms-correlationid | 否           | 用於用戶端作業的特殊字串值。 這會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。 如果未提供此值，則回應標頭中會產生並提供一個。 |
| 授權      | 是          | JSON Web 權杖 (JWT) 持有人權杖。                                                                    |
|  |  |  |

*回應主體*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **參數名稱**     | **資料類型** | **說明**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | 字串        | Azure 中的 SaaS 訂用帳戶資源識別碼。    |
| offerId                | 字串        | 使用者訂閱的供應項目識別碼。         |
| planId                 | 字串        | 使用者訂閱的方案識別碼。          |
| saasSubscriptionName   | 字串        | SaaS 訂用帳戶名稱。                |
| saasSubscriptionStatus | 例舉          | 作業狀態。  下列其中之一：  <br/> - `Subscribed`：訂用帳戶作用中。  <br/> - `Pending`：使用者建立了該資源，但它不是由 ISV 所啟動。   <br/> - `Unsubscribed`：使用者已取消訂閱。   <br/> - `Suspended`：使用者已暫止訂用帳戶。   <br/> - `Deactivated`：Azure 訂用帳戶已暫止。  |
| created                | DateTime      | 建立訂用帳戶的時間戳記值 (UTC)。 |
| lastModified           | DateTime      | 已修改之訂用帳戶的時間戳記值 (UTC)。 |
|  |  |  |

*回應碼*

| **HTTP 狀態碼** | **錯誤碼**     | **說明**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | 已成功解析 get 要求，且主體包含回應。    |
| 400                  | `BadRequest`         | 要求的標頭遺失或指定的 API 版本不正確。 |
| 403                  | `Forbidden`          | 呼叫者沒有權限執行此作業。                      |
| 404                  | `NotFound`           | 找不到具有該指定識別碼的訂用帳戶                                     |
| 429                  | `RequestThrottleId`  | 服務正忙於處理多個要求，請稍後重試。                     |
| 503                  | `ServiceUnavailable` | 服務已關閉，請稍後重試。                             |
|  |  |  |

*回應標頭*

| **標頭索引鍵**     | **必要** | **說明**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 是          | 從用戶端收到的要求識別碼。                                                                   |
| x-ms-correlationid | 是          | 由用戶端傳遞時為相互關聯識別碼，否則此值為伺服器相互關聯識別碼。                   |
| x-ms-activityid    | 是          | 用於追蹤服務要求的特殊字串值。 此值可用於任何核對作業。 |
| Retry-after        | 否           | 用戶端可用來檢查狀態的間隔。                                                       |
| etag               | 是          | 用於取得作業狀態的資源連結。                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>取得訂用帳戶

訂用帳戶端點上的「get」動作可讓使用者從 ISV 取出所有供應項目的訂用帳戶。

*要求*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **參數名稱**  | **說明**                                       |
|---------------------|-------------------------------------------------------|
| api-version         | 要用於此要求的作業版本。 |
|  |  |

*標頭*

| **標頭索引鍵**     | **必要** | **說明**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | 否           | 用於追蹤用戶端要求的特殊字串值。 建議的 GUID。 如果未提供此值，則回應標頭中會產生並提供一個。             |
| x-ms-correlationid | 否           | 用於用戶端作業的特殊字串值。 這會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。 如果未提供此值，則回應標頭中會產生並提供一個。 |
| 授權      | 是          | JSON Web 權杖 (JWT) 持有人權杖。                    |
|  |  |  |

*回應主體*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **參數名稱**     | **資料類型** | **說明**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | 字串        | Azure 中的 SaaS 訂用帳戶資源識別碼。    |
| offerId                | 字串        | 使用者訂閱的供應項目識別碼。         |
| planId                 | 字串        | 使用者訂閱的方案識別碼。          |
| saasSubscriptionName   | 字串        | SaaS 訂用帳戶名稱。                |
| saasSubscriptionStatus | 例舉          | 作業狀態。  下列其中之一：  <br/> - `Subscribed`：訂用帳戶作用中。  <br/> - `Pending`：使用者建立了該資源，但它不是由 ISV 所啟動。   <br/> - `Unsubscribed`：使用者已取消訂閱。   <br/> - `Suspended`：使用者已暫止訂用帳戶。   <br/> - `Deactivated`：Azure 訂用帳戶已暫止。  |
| created                | DateTime      | 建立訂用帳戶的時間戳記值 (UTC)。 |
| lastModified           | DateTime      | 已修改之訂用帳戶的時間戳記值 (UTC)。 |
|  |  |  |

*回應碼*

| **HTTP 狀態碼** | **錯誤碼**     | **說明**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | 已成功解析 get 要求，且主體包含回應。    |
| 400                  | `BadRequest`         | 要求的標頭遺失或指定的 API 版本不正確。 |
| 403                  | `Forbidden`          | 呼叫者沒有權限執行此作業。                      |
| 404                  | `NotFound`           | 找不到具有該指定識別碼的訂用帳戶                                     |
| 429                  | `RequestThrottleId`  | 服務正忙於處理多個要求，請稍後重試。                     |
| 503                  | `ServiceUnavailable` | 服務已暫時關閉。 請稍後重試。                             |
|  |  |  |

*回應標頭*

| **標頭索引鍵**     | **必要** | **說明**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 是          | 從用戶端收到的要求識別碼。                                                                   |
| x-ms-correlationid | 是          | 由用戶端傳遞時為相互關聯識別碼，否則此值為伺服器相互關聯識別碼。                   |
| x-ms-activityid    | 是          | 用於追蹤服務要求的特殊字串值。 此值可用於任何核對作業。 |
| Retry-after        | 否           | 用戶端可用來檢查狀態的間隔。                                                       |
|  |  |  |

### <a name="saas-webhook"></a>SaaS Webhook

SaaS Webhook 可用來主動通知 SaaS 服務關於變更的訊息。 此 POST API 應未經驗證，且將由 Microsoft 服務所呼叫。 SaaS 服務應該會呼叫作業 API，以在對 Webhook 通知執行動作之前進行驗證和授權。 

*內文*

``` json
  { 
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "offerId": "sampleSaaSOffer", // Provided with "Update" action
    "publisherId": "contoso", 
    "planId": "silver",     // Provided with "Update" action
    "action": "Activate", // Activate/Delete/Suspend/Reinstate/Update
    "timeStamp": "2018-12-01T00:00:00"
  }
```

| **參數名稱**     | **資料類型** | **說明**                               |
|------------------------|---------------|-----------------------------------------------|
| id  | 字串       | 已觸發作業的唯一識別碼。                |
| activityId   | 字串        | 用於追蹤服務要求的特殊字串值。 此值可用於任何核對作業。               |
| subscriptionId                     | 字串        | Azure 中的 SaaS 訂用帳戶資源識別碼。    |
| offerId                | 字串        | 使用者訂閱的供應項目識別碼。 僅為「更新」動作提供。        |
| publisherId                | 字串        | SaaS 供應項目的發行者識別碼         |
| planId                 | 字串        | 使用者訂閱的方案識別碼。 僅為「更新」動作提供。          |
| 動作                 | 字串        | 觸發此通知的動作。 可能的值 - 啟動、刪除、暫止、恢復、更新          |
| timeStamp                 | 字串        | 觸發此通知時的時間戳記值 (採用 UTC)。          |
|  |  |  |


## <a name="next-steps"></a>後續步驟

開發人員可以也以程式設計方式擷取和操作工作負載、 供應項目，以及 「 發行者 」 設定檔使用[Cloud Partner 入口網站 REST Api](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)。
