---
title: 使用 REST API 檢閱 Azure Enterprise 註冊計費資料 | Microsoft Docs
description: 了解如何使用 Azure REST API 來檢閱 Enterprise 註冊計費資訊。
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: alleonar
ms.openlocfilehash: 046b2e31aaefa5916a42b3652f9e6a8fdceff367
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064398"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>使用 REST API 檢閱 Enterprise 註冊計費

Azure 報告 API 可協助您檢閱及管理 Azure 費用。

在本文中，您將了解如何擷取與企業帳戶註冊建立關聯的目前帳單。

若要擷取目前的帳單：
``` http
GET https://consumption.azure.com/v2/enrollments/{enrollmentID}/usagedetails
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>建立要求  

`{enrollmentID}` 是必要參數，應該包含企業帳戶 (EA) 的註冊識別碼。

以下是必要標頭： 

|要求標頭|說明|  
|--------------------|-----------------|  
|*Content-Type:*|必要。 設定為 `application/json`。|  
|*Authorization:*|必要。 設定為有效的 `Bearer` [API 金鑰](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)。 |  

此範例示範傳回目前計費週期詳細資料的同步呼叫。 基於效能的考量，同步呼叫會傳回過去一個月的資訊。  您也可以[非同步呼叫 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) 傳回 36 個月的資料。


## <a name="response"></a>Response  

傳回狀態碼 200 (確定) 表示成功回應，其中包含您帳戶的詳細費用清單。

``` json
{
    "id": "${id}",
    "data": [
        {
            "cost": ${cost}, 
            "departmentId": ${departmentID},
            "subscriptionGuid" : ${subscriptionGuid} 
            "date": "${date}",
            "tags": "${tags}",
            "resourceGroup": "${resourceGroup}"
        } // ...
    ],
    "nextLink": "${nextLinkURL}"
}
```  

**data** 中的每個項目代表一筆費用：

|回應屬性|說明|
|----------------|----------|
|**cost** | 收取金額，以適用於資料中心位置的貨幣為單位。 |
|**subscriptionGuid** | 訂用帳戶的全域唯一識別碼。 | 
|**departmentId** | 部門的識別碼 (若有)。 |
|**date** | 帳單日期。 |
|**「標記」** | JSON 字串，其中包含與訂用帳戶建立關聯的標記。 |
|**resourceGroup**|資源群組名稱，其中包含產生費用的物件。 |
|**nextLink**| 設定時，指定「下一頁」詳細資料的 URL。 如果該頁面是最後一頁，則為空白。 |  
||
  
部門識別碼、資源群組、標記及相關欄位是由 EA 管理員定義。  

此範例已經過縮減；如需每個回應欄位的完整描述，請參閱 [Get usage detail](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) (取得使用情況詳細資料)。 

其他狀態碼表示錯誤狀況。 在這些情況下，回應物件會說明要求為何失敗。

``` json
{  
  "error": [  
    { "code": "Error type." 
      "message": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="next-steps"></a>後續步驟 
- 檢閱[企業報告概觀](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- 調查[企業計費 REST API](https://docs.microsoft.com/rest/api/billing/)   
- [Get started with Azure REST API](https://docs.microsoft.com/rest/api/azure/) (開始使用 Azure REST API)   
