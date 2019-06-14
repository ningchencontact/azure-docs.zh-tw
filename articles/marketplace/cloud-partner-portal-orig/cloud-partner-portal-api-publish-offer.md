---
title: 發佈供應項目 |Azure Marketplace
description: 用來發佈指定供應項目的 API。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 117a4e5e238e754524ff813ce25ebc1105e2153c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64934985"
---
<a name="publish-an-offer"></a>發佈供應項目
================

啟動指定供應項目的發佈程序。 此呼叫是會長時間執行的作業。

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>URI 參數
--------------

|  **名稱**      |    **說明**                               |  **資料類型** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | 發行者識別碼，例如 `contoso`      |   字串       |
|  offerId       | 供應項目識別碼                                 |   字串       |
|  api-version   | API 的最新版本                        |   Date         |
|  |  |


<a name="header"></a>頁首
------

|  **名稱**        |    **值**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  授權   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>本文範例
------------

### <a name="request"></a>要求

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>要求本文屬性

|  **名稱**               |   **說明**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  notification-emails    | 以逗號分隔的電子郵件地址清單；系統會向這些地址傳送發佈作業進度通知。 |
|  |  |


### <a name="response"></a>Response

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>回應標頭

|  **名稱**             |    **值**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operation-Location    | 可查詢以判斷作業目前狀態的 URL。    |
|  |  |


### <a name="response-status-codes"></a>回應狀態碼

| **代碼** |  **說明**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`：已順利接受要求。 回應包含可用來追蹤已啟動之作業的位置。 |
| 400   | `Bad/Malformed request`：錯誤回應本文可能會提供更多資訊。                                                               |
| 422   | `Un-processable entity`：表示要發佈的實體無法通過驗證。                                                        |
| 404   | `Not found`：用戶端所指定的實體不存在。                                                                              |
|  |  |
