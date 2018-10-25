---
title: 取消作業 API | Microsoft Docs
description: 取消作業。
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
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 18f00391beded0744c80eab73bb1efe1c6ab8dbc
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805487"
---
<a name="cancel-operation"></a>取消作業 
=================

此 API 會取消目前正在供應項目上進行的作業。 使用[擷取作業 API](./cloud-partner-portal-api-retrieve-operations.md) 來取得要傳遞至此 API 的 `operationId`。 取消通常是一個同步作業，但在某些複雜的案例中，新的作業可能需要取消現有作業。 在此情況下，HTTP 回應本文會包含應用來查詢狀態的作業位置。

您可以透過要求提供電子郵件地址的逗號分隔清單，而此 API 將通知這些地址關於作業的進度。

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>URI 參數
--------------

|  **名稱**    |      **說明**                                  |    **資料類型**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  發行者識別碼，例如 `contoso`         |   字串          |
| offerId      |  供應項目識別碼                                     |   字串          |
| api-version  |  API 目前的版本                               |    日期           |
|  |  |  |


<a name="header"></a>頁首
------

|  **名稱**              |  **值**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Authorization         |  Bearer 您的權杖 |
|  |  |


<a name="body-example"></a>本文範例
------------

### <a name="request"></a>要求

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>要求本文屬性

|  **名稱**                |  **說明**                                               |
|  --------                |  ---------------                                               |
|  notification-emails     | 以逗號分隔的電子郵件識別碼清單，這些電子郵件識別碼將會收到關於發行作業進度的通知。 |
|  |  |


### <a name="response"></a>Response

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>回應標頭

|  **名稱**             |    **值**                       |
|  ---------            |    ----------                      |
| Operation-Location    | URL，可進行查詢以判斷作業的目前狀態。 |
|  |  |


### <a name="response-status-codes"></a>回應狀態碼

| **代碼**  |  **說明**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | 正常。 已成功處理要求，並同步取消作業。 |
|  202      | 已接受。 已成功處理要求，而且正在取消該作業。 取消作業的位置會在回應標頭中傳回。 |
|  400      | 錯誤/格式錯誤的要求。 錯誤回應本文可能會提供更多資訊。  |
|  403      | 禁止存取。 用戶端無權存取要求中指定的命名空間。 |
|  404      | 找不到。 指定的實體不存在。 |
|  |  |
