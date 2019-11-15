---
title: 具有通知的 Azure 受控應用程式
description: 使用 webhook 端點設定受控應用程式，以接收有關受控應用程式實例上的建立、更新、刪除和錯誤的通知。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: a00e5be4493b8c8116e2925e88a3ce4bf8cfb722
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085313"
---
# <a name="azure-managed-applications-with-notifications"></a>具有通知的 Azure 受控應用程式

Azure 受控代理程式更新可讓發行者根據受控應用程式實例的生命週期事件來自動化動作。 發行者可以指定自訂通知 webhook 端點，以接收關於新的和現有受控應用程式實例的事件通知。 它可讓發行者在應用程式布建、更新和刪除時設定自訂工作流程。

## <a name="getting-started"></a>快速入門
若要開始接收受管理的應用程式，請啟動公用 HTTPS 端點，並在發佈服務類別目錄應用程式定義或 Marketplace 供應專案時加以指定。

以下是可快速啟動並執行的建議步驟系列：
1. 啟動會記錄傳入 POST 要求並傳回 `200 OK`的公用 HTTPS 端點。
2. 將端點新增至服務目錄應用程式定義或 marketplace 供應專案，如下所述。
3. 建立參考應用程式定義或 marketplace 供應專案的受控應用程式實例。
4. 驗證是否已成功接收通知。
5. 如以下的「**端點驗證**」一節所述，啟用授權。
6. 遵循下列**通知架構**檔來剖析通知要求，並根據通知來執行您的商務邏輯。

## <a name="adding-service-catalog-application-definition-notifications"></a>新增服務類別目錄應用程式定義通知
#### <a name="azure-portal"></a>Azure 入口網站
請閱讀[透過 Azure 入口網站發佈服務目錄應用程式](./publish-portal.md)以開始使用。

![入口網站上的服務類別目錄應用程式定義通知](./media/publish-notifications/service-catalog-notifications.png)
#### <a name="rest-api"></a>REST API

> [!NOTE]
> 目前只支援一個端點作為應用程式定義屬性中**notificationEndpoints**的一部分

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="adding-marketplace-managed-application-notifications"></a>新增 marketplace 受控代理程式更新
如需詳細資訊，請參閱[建立 Azure 應用程式供應](../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md)專案。

![入口網站上的服務類別目錄應用程式定義通知](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>事件觸發程序
下表描述所有可能的條件型 + ProvisioningState 和其觸發程式組合：

EventType | ProvisioningState | 通知的觸發程式
---|---|---
PUT | 已接受 | 在應用程式 PUT 之後，已建立受控資源群組並成功投射。 （開始進行受控 RG 內的部署之前）。
PUT | Succeeded | 在 PUT 之後，完整布建受控應用程式成功。
PUT | Failed | 在任何時間點布建應用程式實例的失敗。
PATCH | Succeeded | 在受控應用程式實例上成功修補之後，以更新標記、jit 存取原則或受控識別。
刪除 | 刪除中 | 一旦使用者起始受控應用程式實例的刪除即可。
刪除 | Deleted | 在完整且成功的受控應用程式刪除之後。
刪除 | Failed | 在解除布建程式期間封鎖刪除的任何錯誤之後。
## <a name="notification-schema"></a>通知架構
當您啟動 webhook 端點來處理通知時，您必須剖析承載以取得重要的屬性，然後對通知採取動作。 服務類別目錄和 Marketplace 受控代理程式更新都提供許多相同的屬性，且具有以下所述的小差異。

#### <a name="service-catalog-application-notification-schema"></a>服務類別目錄代理程式更新架構
以下是成功布建受控應用程式實例後的服務類別目錄通知範例。
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

如果布建失敗，則會將包含錯誤詳細資料的通知傳送至指定的端點。

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="marketplace-application-notification-schema"></a>Marketplace 代理程式更新架構

以下是成功布建受控應用程式實例後的服務類別目錄通知範例。
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

如果布建失敗，則會將包含錯誤詳細資料的通知傳送至指定的端點。

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

參數 | 描述
---|---
eventType | 觸發通知的事件種類。 （例如 "PUT"、"PATCH"、"DELETE"）
applicationId | 觸發通知之受控應用程式的完整資源識別碼。 
eventTime | 觸發通知的事件時間戳記。 （UTC ISO 8601 格式的日期和時間）。
ProvisioningState | 受控應用程式實例的布建狀態。 （例如「成功」、「失敗」、「刪除」、「已刪除」）
錯誤 | *只有在 ProvisioningState 失敗時才指定*。 包含導致失敗之問題的錯誤碼、訊息和詳細資料。
applicationDefinitionId | *僅針對服務類別目錄受控應用程式指定*。 表示已布建受控應用程式實例之應用程式定義的完整資源識別碼。
計劃 | *僅針對 Marketplace 受控應用程式指定*。 代表受控應用程式實例的發行者、供應專案、sku 和版本。

## <a name="endpoint-authentication"></a>端點驗證
若要保護 webhook 端點，並確保通知的真實性：
- 在 webhook URI 之上提供查詢參數，例如 https://your-endpoint.com?sig=Guid。 有了每個通知，請快速檢查查詢參數 `sig` 是否有預期的值 `Guid`。
- 使用 applicationId 在受控應用程式實例上發出 GET。 驗證 provisioningState 符合通知的 provisioningState，以確保一致性。

## <a name="notification-retries"></a>通知重試

受控代理程式更新服務預期會有從 webhook 端點到通知的 `200 OK` 回應。 如果 webhook 端點傳回 HTTP 錯誤碼 > = 500、429或端點暫時無法連線，通知服務將會重試。 如果 webhook 端點在10小時內無法使用，將會捨棄通知訊息，而且重試將會停止。

