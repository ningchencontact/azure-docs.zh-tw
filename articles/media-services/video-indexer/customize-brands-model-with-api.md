---
title: 使用 Azure 影片索引器自訂品牌模型
titlesuffix: Azure Media Services
description: 本文說明如何使用 Azure 影片索引器自訂品牌模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/03/2018
ms.author: anzaman
ms.openlocfilehash: 5591c36a13291432f2d790b4c83e98270b4bc035
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283014"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>使用影片索引器 API 自訂品牌模型

影片索引器支援在視訊和音訊內容的編製索引及重新編製索引期間，從語音和視覺文字偵測品牌。 品牌偵測功能可識別 Bing 的品牌資料庫建議所提及的產品、服務以及公司。 例如，如果在視訊或音訊內容中提及 Microsoft，或者視訊或音訊內容出現在視訊的視覺文字中，影片索引器會將其偵測為內容中的品牌。 自訂品牌模型可讓您從偵測到的品牌中排除特定品牌，並包含應該是您模型的一部分，但可能不在 Bing 品牌資料庫中的品牌。

如需詳細概觀，請參閱[概觀](customize-brands-model-overview.md)。

您可以使用影片索引器 API 建立、使用和編輯在視訊中偵測到的自訂品牌模型，如本主題中所述。 您也可以使用影片索引器網站，如[使用影片索引器網站自訂品牌模型](customize-brands-model-with-api.md)中所述。

## <a name="create-a-brand"></a>建立品牌

這會建立新的自訂品牌，並將其新增至指定之帳戶的自訂品牌模型。 

### <a name="request-url"></a>要求 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```
 
[查看所需的參數，並使用影片索引器開發人員入口網站測試](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand)。

### <a name="request-parameters"></a>要求參數 

|**名稱**|**類型**|**必要**|**說明**|
|---|---|---|---|
|location|字串|是|應該路由傳送呼叫的目標 Azure 區域。 如需詳細資訊，請參閱 [Azure 區域和影片索引器](regions.md)。|
|accountId|字串|是|帳戶的全域唯一識別碼|
|accessToken|字串|是|存取權杖 (必須是[帳戶的存取權杖](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)範圍)，可對呼叫進行驗證。 存取權杖會在 1 小時後過期。|

### <a name="request-body"></a>Request body

除了這些參數之外，您還必須提供要求主體 JSON 物件，此物件可依照下列範例的格式，提供新品牌的相關資訊。

```json
{
  "name": "Example",
  "enabled": true,
  "tags": ["Tag1", "Tag2"],
  "description": "This is an example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example"
}
```

將 **enabled** 設為 true，可將品牌置於要偵測之影片索引器的 [包含] 清單。 將 **enabled** 設為 false，則可將品牌置於 [排除] 清單，如此影片索引器將不會偵測該品牌。

**referenceUrl** 值可以是品牌的任何參考網站，例如其維基百科頁面的連結。

**tags** 值是品牌標籤的清單。 這在影片索引器網站中，會出現在品牌的 [分類] 欄位內。 例如，您可以將品牌 "Azure" 標記或分類為「雲端」。

### <a name="response"></a>Response

回應會針對您剛才依照下列範例格式建立的品牌，提供相關資訊。

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>刪除品牌

從指定之帳戶的自訂品牌模型中移除品牌。 帳戶是使用 **accountId** 參數指定的。 一旦呼叫成功之後，該品牌將不再位於 [包含] 或 [排除] 品牌清單中。

### <a name="request-url"></a>要求 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```
 
[查看所需的參數，並使用影片索引器開發人員入口網站測試](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?)。

### <a name="request-parameters"></a>要求參數

|**名稱**|**類型**|**必要**|**說明**|
|---|---|---|---|
|location|字串|是|應該路由傳送呼叫的目標 Azure 區域。 如需詳細資訊，請參閱 [Azure 區域和影片索引器](regions.md)。|
|accountId|字串|是|帳戶的全域唯一識別碼|
|id|integer|是|品牌識別碼 (建立品牌時產生)|
|accessToken|字串|是|存取權杖 (必須是[帳戶的存取權杖](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)範圍)，可對呼叫進行驗證。 存取權杖會在 1 小時後過期。|

### <a name="request-body"></a>Request body

此呼叫不需要其他要求本文。

### <a name="response"></a>Response

成功刪除品牌時，沒有任何傳回的內容。

## <a name="get-a-specific-brand"></a>取得特定品牌

這可讓您使用品牌識別碼，在指定之帳戶的自訂品牌模型中搜尋品牌的詳細資料。 

### <a name="request-url"></a>要求 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```
 
[查看所需的參數，並使用影片索引器開發人員入口網站測試](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?)。

### <a name="request-parameters"></a>要求參數 

|**名稱**|**類型**|**必要**|**說明**|
|---|---|---|---|
|location|字串|是|應該路由傳送呼叫的目標 Azure 區域。 如需詳細資訊，請參閱 [Azure 區域和影片索引器](regions.md)。|
|accountId|字串|是|帳戶的全域唯一識別碼|
|id|integer|是|品牌識別碼 (建立品牌時產生)|
|accessToken|字串|是|存取權杖 (必須是[帳戶的存取權杖](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)範圍)，可對呼叫進行驗證。 存取權杖會在 1 小時後過期。|

### <a name="request-body"></a>Request body

此呼叫不需要其他要求本文。

### <a name="response"></a>Response

回應會針對您剛才依照下列範例格式搜尋 (使用品牌識別碼) 的品牌，提供相關資訊。

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> **enabled** 設為 **true** 時，表示品牌位於要偵測之影片索引器的 [包含] 清單，**enabled** 為 false 時，則表示品牌位於 [排除] 清單，因此影片索引器將不會偵測該品牌。

## <a name="update-a-specific-brand"></a>更新特定品牌

這可讓您使用品牌識別碼，在指定之帳戶的自訂品牌模型中搜尋品牌的詳細資料。 

### <a name="request-url"></a>要求 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```
 
[查看所需的參數，並使用影片索引器開發人員入口網站測試](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?)。

### <a name="request-parameters"></a>要求參數

|**名稱**|**類型**|**必要**|**說明**|
|---|---|---|---|
|location|字串|是|應該路由傳送呼叫的目標 Azure 區域。 如需詳細資訊，請參閱 [Azure 區域和影片索引器](regions.md)。|
|accountId|字串|是|帳戶的全域唯一識別碼|
|id|integer|是|品牌識別碼 (建立品牌時產生)|
|accessToken|字串|是|存取權杖 (必須是[帳戶的存取權杖](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)範圍)，可對呼叫進行驗證。 存取權杖會在 1 小時後過期。|

### <a name="request-body"></a>Request body

除了這些參數之外，您還必須提供要求主體 JSON 物件，此物件可依照下列範例的格式，提供您要更新之品牌的相關更新資訊。

```json
{
  "name": "Example",
  "enabled": false,
  "tags": ["Tag1", "NewTag2"],
  "description": "This is an update example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-28T15:52:22.3413983",
}
```

> [!NOTE]
> 在此範例中，於 [建立品牌] 區段中範例本文內所建立的品牌，在這裡會更新為新的標籤和新的描述。 **enabled** 值也會變更為 false，以便將其置於 [排除] 清單。

### <a name="response"></a>Response

回應會針對您剛才依照下列範例格式更新的品牌，提供更新的資訊。

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampeAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>取得所有品牌

這會在指定之帳戶的自訂品牌模型中，傳回 [包含] 和 [排除] 品牌清單中的所有品牌。

### <a name="request-url"></a>要求 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```
 
[查看所需的參數，並使用影片索引器開發人員入口網站測試](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?)。

### <a name="request-parameters"></a>要求參數

|**名稱**|**類型**|**必要**|**說明**|
|---|---|---|---|
|location|字串|是|應該路由傳送呼叫的目標 Azure 區域。 如需詳細資訊，請參閱 [Azure 區域和影片索引器](regions.md)。|
|accountId|字串|是|帳戶的全域唯一識別碼|
|accessToken|字串|是|存取權杖 (必須是[帳戶的存取權杖](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)範圍)，可對呼叫進行驗證。 存取權杖會在 1 小時後過期。|

### <a name="request-body"></a>Request body

此呼叫不需要其他要求本文。

### <a name="response"></a>Response

回應會針對您帳戶中的所有品牌，依照下列範例的格式，提供一份清單及各自的詳細資料。

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
        {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> 名稱為 *Example* 的品牌位於要偵測之影片索引器的 [包含] 清單中，而名稱為 *Example2* 的品牌則位於 [排除] 清單中，因此影片索引器將不會偵測該品牌。

## <a name="get-brands-model-settings"></a>取得品牌模型設定

這會傳回指定之帳戶中的品牌模型設定。 品牌模型設定表示是否已從 Bing 品牌資料庫中啟用偵測。 如果未啟用 Bing 品牌，影片索引器將只會從指定之帳戶的自訂品牌模型中偵測品牌。

### <a name="request-url"></a>要求 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```
 
[查看所需的參數，並使用影片索引器開發人員入口網站測試](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands)。

### <a name="request-parameters"></a>要求參數

|**名稱**|**類型**|**必要**|**說明**|
|---|---|---|---|
|location|字串|是|應該路由傳送呼叫的目標 Azure 區域。 如需詳細資訊，請參閱 [Azure 區域和影片索引器](regions.md)。|
|accountId|字串|是|帳戶的全域唯一識別碼|
|accessToken|字串|是|存取權杖 (必須是[帳戶的存取權杖](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)範圍)，可對呼叫進行驗證。 存取權杖會在 1 小時後過期。|

### <a name="request-body"></a>Request body

此呼叫不需要其他要求本文。

### <a name="response"></a>Response

回應會顯示是否依照下列範例的格式啟用 Bing 品牌。

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> **useBuiltIn** 設為 true 時，表示已啟用 Bing 品牌。 如果 *useBuiltin* 為 false，則會停用 Bing 品牌。 **state** 值可以忽略，因為它已被取代。

## <a name="update-brands-model-settings"></a>更新品牌模型設定

這會更新指定之帳戶中的品牌模型設定。 品牌模型設定表示是否已從 Bing 品牌資料庫中啟用偵測。 如果未啟用 Bing 品牌，影片索引器將只會從指定之帳戶的自訂品牌模型中偵測品牌。

### <a name="request-url"></a>要求 URL： 
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/BrandsModelSettings?accessToken={accessToken}
```
 
[查看所需的參數，並使用影片索引器開發人員入口網站測試](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?)。

### <a name="request-parameters"></a>要求參數 

|**名稱**|**類型**|**必要**|**說明**|
|---|---|---|---|
|location|字串|是|應該路由傳送呼叫的目標 Azure 區域。 如需詳細資訊，請參閱 [Azure 區域和影片索引器](regions.md)。|
|accountId|字串|是|帳戶的全域唯一識別碼|
|accessToken|字串|是|存取權杖 (必須是[帳戶的存取權杖](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)範圍)，可對呼叫進行驗證。 存取權杖會在 1 小時後過期。|

### <a name="request-body"></a>Request body

除了這些參數之外，您還必須提供要求主體 JSON 物件，此物件可依照下列範例的格式，提供新品牌的相關資訊。

```json
{
    "useBuiltIn":true
}
```

> [!NOTE]
> **useBuiltIn** 設為 true 時，表示已啟用 Bing 品牌。 如果 *useBuiltin* 為 false，則會停用 Bing 品牌。

### <a name="response"></a>Response

成功更新品牌模型設定時，沒有任何傳回的內容。

## <a name="next-steps"></a>後續步驟

[使用網站自訂品牌模型](customize-brands-model-with-website.md)
