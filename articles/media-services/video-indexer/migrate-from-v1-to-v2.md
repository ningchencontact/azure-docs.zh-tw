---
title: 從 Azure 影片索引子 API v1 移轉至 v2
titlesuffix: Azure Media Services
description: 本主題說明如何從 Azure 影片索引子 API v1 移轉至 v2。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: ad2e2dace7d94c196d46beed957b39f3953246b5
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291843"
---
# <a name="migrate-from-the-video-indexer-api-v1-to-v2"></a>從影片索引子 API v1 移轉至 v2

> [!Note]
> 影片索引子 V1 API 已在 2018 年 8 月 1 日淘汰。 您現在應該使用影片索引子 v2 API。 <br/>若要使用影片索引子 v2 API 進行開發，請參閱[此處](https://api-portal.videoindexer.ai/)的指示。 

本文說明在 v2 中導入的變更。  

## <a name="api-changes"></a>API 變更

### <a name="authorization-and-operations"></a>授權和作業

在 v2 版中，影片索引子已變更 API 的驗證和授權模型。 共有兩組 API： 

* Authorization 
* 作業

**授權** API 用來取得呼叫**作業** API 的存取權杖。 **作業** API 包含所有的影片索引子 API，例如上傳影片、深入了解，以及其他作業。

在您[訂閱](video-indexer-get-started.md)**授權** API 後，您將能夠取得存取權杖，只要傳遞您的訂用帳戶金鑰即可 (就像在 v1 中一樣)。

## <a name="getting-and-using-the-access-token-for-operations-apis"></a>取得和使用作業 API 的存取權杖

在呼叫**作業** API 時，將不需再使用訂用帳戶金鑰。 此時您應傳遞**授權** API 所取得的存取權杖。 

每個要求都應有有效的權杖，以比對您要呼叫之 API 的存取層級。 例如，使用者的作業 (例如取得您的帳戶) 需要使用者存取權杖。 帳戶層級的作業 (例如列出所有影片) 則需要帳戶存取權杖。 影片的作業 (例如重新編製影片的索引) 需要帳戶存取權杖或影片存取權杖。

為了方便起見，您可以使用**授權** API > **GetAccounts** 來取得您的帳戶，而不需先取得使用者權杖。 您也可以透過有效權杖來要求取得帳戶，以略過用於取得帳戶權杖的額外呼叫。

如需關於不同存取權杖的詳細資訊，請參閱[使用 Azure 影片索引子 API](video-indexer-use-apis.md)。

### <a name="locations"></a>位置

每個對 API 的呼叫均應包含影片索引子帳戶的位置。 不含位置或位置錯誤的 API 呼叫將會失敗。

下表說明適用的值。 **參數值**是您在使用 API 時傳遞的值。

|**名稱**|**參數值**|**說明**|
|---|---|---|
|試用版|試用版|用於試用帳戶。 例如： https://api.videoindexer.ai/trial/Accounts/{accountId}/Videos/{videoId}/Index?language=English 。|
|美國西部|westus2|用於 Azure 美國西部 2 區域。  例如： https://api.videoindexer.ai/westus2/Accounts/{accountId}/Videos/{videoId}/Index?language=English 。|
|北歐 |northeurope|用於 Azure 北歐區域。 例如： https://api.videoindexer.ai/northeurope/Accounts/{accountId}/Videos/{videoId}/Index?language=English。 |
|東亞|eastasia|用於 Azure 東亞區域。 例如： https://api.videoindexer.ai/eastasia/Accounts/{accountId}/Videos/{videoId}/Index?language=English。|

### <a name="data-model"></a>資料模型

影片索引子現在已有簡化的資料模型，可提供更清楚的深入解析。 若要進一步了解 v2 API 所產生的輸出，請參閱[檢查 v2 API 所產生的影片索引子輸出](video-indexer-output-json-v2.md)。

### <a name="swagger"></a>Swagger

影片索引子 API 定義已進行相應的更新，並且可透過[影片索引子開發人員入口網站](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token)下載取得。


### <a name="v1-vs-v2-examples"></a>V1 與 V2 的範例

新的 V2 API 包含 3 個主要參數：

1. [LOCATION] - 如前所述。 試用版、westus2、northeurope 或 eastasia。
2. [YOUR_ACCOUNT_ID] - 帳戶的 GUID 識別碼。 在取得所有帳戶時擷取 (說明如下)。
3. [YOUR_VIDEO_ID] - 影片的識別碼 (例如 "d4fa369abc")。 在上傳影片時或搜尋影片時傳回。

#### <a name="uploading-a-video-in-v1"></a>在 V1 中上傳影片：

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns?name=some_name&description=some_description&privacy=private&videoUrl=http://URL_TO_YOUR_VIDEO
```

#### <a name="uploading-a-video-in-v2"></a>在 V2 中上傳影片：

1. 取得上傳要求的存取權杖：

  您可以取得所有帳戶及其存取權杖：

    ```
    https://api.videoindexer.ai/auth/[LOCATION]/Accounts?generateAccessTokens=true&allowEdit=true
    ```

  或者，取得特定帳戶的存取權杖：
  
  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/AccessToken?allowEdit=true
  ```
2. 上傳影片：

  ```
  POST https://api.videoindexer.ai/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos?name=MySample&description=MySampleDescription&videoUrl=[URL_ENCODED_VIDEO_URL]&accessToken=eyJ0eXAiOiJ...
  ```

#### <a name="getting-insights-in-v1"></a>在 V1 中取得深入解析：

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]
```
  
#### <a name="getting-insights-in-v2"></a>在 V2 中取得深入解析：

1. 使用帳戶存取權杖，或取得影片層級存取權杖：

  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/AccessToken
  ```
  
2. 取得深入解析：

  ```
  https://api.videoindexer.ai/[LOCATION]/Accounts[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

#### <a name="getting-video-processing-state-in-v1"></a>在 V1 中取得影片處理狀態：

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]/State
```
  
#### <a name="getting-video-processing-state-in-v2"></a>在 V2 中取得影片處理狀態：

在 API v2 中，處理狀態會隨著「取得影片索引 API」一起傳回。

1. 使用帳戶存取權杖，或取得影片層級存取權杖：

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```
  
2. 取得深入解析：

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

## <a name="next-steps"></a>後續步驟

[使用 Azure 影片索引子 API](video-indexer-use-apis.md)

