---
title: 使用影片索引器 API 自訂語言模型 - Azure
titlesuffix: Azure Media Services
description: 本文說明如何使用影片索引器 API 自訂語言模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: ca1e66d20b19c1a5b85a4f4ff1c433331116bee7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553737"
---
# <a name="customize-a-language-model-with-the-video-indexer-apis"></a>使用影片索引器 API 自訂語言模型

影片索引器可讓您上傳適應文字 (也就是從您希望引擎適應其詞彙的網域中取得的文字) 來建立自訂語言模型，以自訂語音辨識。 一旦您將模型定型之後，也會辨識出現在調整文字中的新字。 

如需自訂語言模型的詳細概觀和最佳作法，請參閱[使用影片索引器自訂語言模型](customize-language-model-overview.md)。

您可以使用影片索引器 API，在您的帳戶中建立和編輯自訂語言模型，如本主題中所述。 您也可以使用網站，如[使用影片索引器網站自訂語言模型](customize-language-model-with-api.md)中所述。

## <a name="create-a-language-model"></a>建立語言模型

下列命令會在指定的帳戶中建立新的自訂語言模型。 您可以在此呼叫中上傳語言模型的檔案。 或者，您可以在這裡建立語言模型，並稍後透過更新語言模型，上傳模型的檔案。

> [!NOTE]
> 您仍然必須使用模型已啟用的檔案為模型定型，以了解其檔案的內容。 下一節說明為語言定型的指示。

### <a name="request-url"></a>要求 URL

這是 POST 要求。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

以下是以 Curl 撰寫的要求。

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}&modelName={modelName}&language={language}"

--data-ascii "{body}" 
```

[查看所需的參數，並使用影片索引器開發人員入口網站進行測試](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?) \(英文\)。

### <a name="request-parameters"></a>要求參數

|**名稱**|**類型**|**必要**|**說明**|
|---|---|---|---|
|location|string|是|應該路由傳送呼叫的目標 Azure 區域。 如需詳細資訊，請參閱 [Azure 區域和影片索引器](regions.md)。|
|accountId|string|是|帳戶的全域唯一識別碼|
|accessToken|string|是|用來針對呼叫進行驗證的存取權杖 (必須是[帳戶存取權杖](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) \(英文\) 範圍)。 存取權杖會在 1 小時後過期。|
|modelName|string|是|語言模型的名稱|
|語言|string|是|語言模型的語言。 <br/>必須使用「語言標記-區域」的 BCP-47 格式 (例如 'en-US')，為語言提供 **language** 參數。 支援的語言有英文 (en-US)、德文 (de-DE)、西班牙文 (es-SP)、阿拉伯文 (ar-EG)、法文 (fr-FR)、印度文 (hi-HI)、義大利文 (it-IT)、日文 (ja-JP)、葡萄牙文 (pt-BR)、俄文 (ru-RU) 和中文 (zh-CN)。  |

### <a name="request-body"></a>Request body

若要將上傳要新增至語言模型的檔案，除了提供上述必要參數的值之外，您還必須在本文中使用 form-data 上傳檔案。 作法有二： 

1. 索引鍵將是檔案名稱，而值則是 txt 檔案
2. 索引鍵將是檔案名稱，而值則是 txt 檔案的 URL

### <a name="response"></a>Response

回應會依照範例 JSON 輸出的格式，提供新建立之語言模型的中繼資料，以及每個模型檔案的中繼資料。

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>將語言模型定型

下列命令會在指定的帳戶中，使用檔案中已上傳至語言模型並加以啟用的內容，將自訂語言模型定型。 

> [!NOTE]
> 您必須先建立語言模型，再上傳其檔案。 您可以在建立語言模型時，或藉由更新語言模型來上傳檔案。 

### <a name="request-url"></a>要求 URL

這是 PUT 要求。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}
```

以下是以 Curl 撰寫的要求。

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}"
```
 
[查看所需的參數，並使用影片索引器開發人員入口網站進行測試](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) \(英文\)。

### <a name="request-parameters"></a>要求參數

|**名稱**|**類型**|**必要**|**說明**|
|---|---|---|---|
|location|string|是|應該路由傳送呼叫的目標 Azure 區域。 如需詳細資訊，請參閱 [Azure 區域和影片索引器](regions.md)。|
|accountID|string|是|帳戶的全域唯一識別碼|
|modelId|string|是|語言模型識別碼 (建立語言模型時產生)|
|accessToken|string|是|用來針對呼叫進行驗證的存取權杖 (必須是[帳戶存取權杖](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) \(英文\) 範圍)。 存取權杖會在 1 小時後過期。|

### <a name="request-body"></a>Request body

此呼叫不需要其他要求本文。

### <a name="response"></a>Response

回應會依照範例 JSON 輸出的格式，提供新定型之語言模型的中繼資料，以及每個模型檔案的中繼資料。

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

接著，您應該針對 **linguisticModelId** 參數 ([將視訊上傳至索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)時) 和 **languageModelId** 參數 ([重新編製視訊索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)時)，使用語言模型的 **id** 值。

## <a name="delete-a-language-model"></a>刪除語言模型

下列命令會從指定的帳戶中刪除自訂語言模型。 使用已刪除之語言模型的任何視訊都會保留相同的索引，直到您重新編製視訊索引為止。 如果您重新編製視訊索引，可以將新的語言模型指派給該視訊。 否則，影片索引器將會使用其預設模型重新編製視訊索引。

### <a name="request-url"></a>要求 URL

這是 DELETE 要求。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

以下是以 Curl 撰寫的要求。

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[查看所需的參數，並使用影片索引器開發人員入口網站進行測試](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) \(英文\)。

### <a name="request-parameters"></a>要求參數 

|**名稱**|**類型**|**必要**|**說明**|
|---|---|---|---|
|location|string|是|應該路由傳送呼叫的目標 Azure 區域。 如需詳細資訊，請參閱 [Azure 區域和影片索引器](regions.md)。|
|accountID|string|是|帳戶的全域唯一識別碼|
|modelId|string|是|語言模型識別碼 (建立語言模型時產生)|
|accessToken|string|是|用來針對呼叫進行驗證的存取權杖 (必須是[帳戶存取權杖](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) \(英文\) 範圍)。 存取權杖會在 1 小時後過期。|

### <a name="request-body"></a>Request body

此呼叫不需要其他要求本文。

### <a name="response"></a>Response

成功刪除語言模型時，沒有任何傳回的內容。

## <a name="update-a-language-model"></a>更新語言模型

下列命令會在指定的帳戶中更新自訂語言模型。

> [!NOTE]
> 您必須已經建立語言模型。 您可以使用這個呼叫啟用或停用模式下的所有檔案、更新語言模型的名稱，以及上傳要新增至語言模型的檔案。

### <a name="request-url"></a>要求 URL

這是 PUT 要求。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}[&modelName][&enable]
```

以下是以 Curl 撰寫的要求。

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}?modelName={string}&enable={string}"

--data-ascii "{body}" 
```
 
[查看所需的參數，並使用影片索引器開發人員入口網站進行測試](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) \(英文\)。

### <a name="request-parameters"></a>要求參數 

|**名稱**|**類型**|**必要**|**說明**|
|---|---|---|---|
|location|string|是|應該路由傳送呼叫的目標 Azure 區域。 如需詳細資訊，請參閱 [Azure 區域和影片索引器](regions.md)。|
|accountID|string|是|帳戶的全域唯一識別碼|
|modelId|string|是|語言模型識別碼 (建立語言模型時產生)|
|accessToken|string|是|用來針對呼叫進行驗證的存取權杖 (必須是[帳戶存取權杖](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) \(英文\) 範圍)。 存取權杖會在 1 小時後過期。|
|modelName|string|否|您可以提供給模型的新名稱|
|enable|boolean|否|選擇要啟用 (true) 還是停用 (false) 此模型下的所有檔案|

### <a name="request-body"></a>Request body

若要將上傳要新增至語言模型的檔案，除了提供上述必要參數的值之外，您還必須在本文中使用 form-data 上傳檔案。 作法有二： 

1. 索引鍵將是檔案名稱，而值則是 txt 檔案
2. 索引鍵將是檔案名稱，而值則是 txt 檔案的 URL

### <a name="response"></a>Response

回應會依照範例 JSON 輸出的格式，提供新定型之語言模型的中繼資料，以及每個模型檔案的中繼資料。

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```
您可以使用這裡傳回的檔案**識別碼**來下載檔案的內容。

## <a name="update-a-file-from-a-language-model"></a>從語言模型更新檔案

下列命令可讓您在指定的帳戶中，更新自訂語言模型內檔案的名稱以及**啟用**狀態。

### <a name="request-url"></a>要求 URL

這是 PUT 要求。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}[&fileName][&enable]
```

以下是以 Curl 撰寫的要求。

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}?fileName={string}&enable={string}"
```
 
[查看所需的參數，並使用影片索引器開發人員入口網站進行測試](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) \(英文\)。

### <a name="request-parameters"></a>要求參數 

|**名稱**|**類型**|**必要**|**說明**|
|---|---|---|---|
|location|string|是|應該路由傳送呼叫的目標 Azure 區域。 如需詳細資訊，請參閱 [Azure 區域和影片索引器](regions.md)。|
|accountId|string|是|帳戶的全域唯一識別碼|
|modelId|string|是|保留檔案之語言模型的識別碼 (建立語言模型時產生)|
|fileId|string|是|要更新之檔案的識別碼 (在建立或更新語言模型期間上傳檔案時產生)|
|accessToken|string|是|用來針對呼叫進行驗證的存取權杖 (必須是[帳戶存取權杖](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) \(英文\) 範圍)。 存取權杖會在 1 小時後過期。|
|fileName|string|否|更新檔案名稱所使用的名稱|
|enable|boolean|否|更新要在語言模型中啟用 (true) 還是停用 (false) 此檔案|

### <a name="request-body"></a>Request body

此呼叫不需要其他要求本文。

### <a name="response"></a>Response

回應會針對您依照下列範例 JSON 輸出格式更新的檔案，提供中繼資料。

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```
您可以使用這裡傳回的檔案**識別碼**來下載檔案的內容。

## <a name="get-a-specific-language-model"></a>取得特定的語言模型

下列命令會傳回指定的帳戶中指定之語言模型的相關資訊，例如語言模型中的語言和檔案。 

### <a name="request-url"></a>要求 URL

這是 GET 要求。
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

以下是以 Curl 撰寫的要求。

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[查看所需的參數，並使用影片索引器開發人員入口網站測試](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get)。

### <a name="request-parameters-and-request-body"></a>要求參數和要求本文

|**名稱**|**類型**|**必要**|**說明**|
|---|---|---|---|
|location|string|是|應該路由傳送呼叫的目標 Azure 區域。 如需詳細資訊，請參閱 [Azure 區域和影片索引器](regions.md)。|
|accountID|string|是|帳戶的全域唯一識別碼|
|modelId|string|是|語言模型識別碼 (建立語言模型時產生)|
|accessToken|string|是|用來針對呼叫進行驗證的存取權杖 (必須是[帳戶存取權杖](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) \(英文\) 範圍)。 存取權杖會在 1 小時後過期。|

### <a name="request-body"></a>Request body

此呼叫不需要其他要求本文。

### <a name="response"></a>Response

回應會依照下列範例 JSON 輸出的格式，提供指定之語言模型的中繼資料，以及每個模型檔案的中繼資料。

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

您可以使用這裡傳回的檔案**識別碼**來下載檔案的內容。

## <a name="get-all-the-language-models"></a>取得所有語言模型

下列命令會傳回清單中指定之帳戶的所有自訂語言模型。

### <a name="request-url"></a>要求 URL

這是 GET 要求。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}
```

以下是以 Curl 撰寫的要求。

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}"
```
 
[查看所需的參數，並使用影片索引器開發人員入口網站進行測試](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) \(英文\)。

### <a name="request-parameters"></a>要求參數

|**名稱**|**類型**|**必要**|**說明**|
|---|---|---|---|
|location|string|是|應該路由傳送呼叫的目標 Azure 區域。 如需詳細資訊，請參閱 [Azure 區域和影片索引器](regions.md)。|
|accountID|string|是|帳戶的全域唯一識別碼|
|accessToken|string|是|用來針對呼叫進行驗證的存取權杖 (必須是[帳戶存取權杖](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) \(英文\) 範圍)。 存取權杖會在 1 小時後過期。|

### <a name="request-body"></a>Request body

此呼叫不需要其他要求本文。

### <a name="response"></a>Response

回應會針對您帳戶中的所有語言模型，依照下列範例 JSON 輸出的格式，提供一份清單及各自的中繼資料和檔案。

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>從語言模型刪除檔案

下列命令會從指定之帳戶中指定的語言模型，刪除指定的檔案。 

### <a name="request-url"></a>要求 URL

這是 DELETE 要求。
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}
```

以下是以 Curl 撰寫的要求。

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[查看所需的參數，並使用影片索引器開發人員入口網站進行測試](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) \(英文\)。

### <a name="request-parameters"></a>要求參數 

|**名稱**|**類型**|**必要**|**說明**|
|---|---|---|---|
|location|string|是|應該路由傳送呼叫的目標 Azure 區域。 如需詳細資訊，請參閱 [Azure 區域和影片索引器](regions.md)。|
|accountID|string|是|帳戶的全域唯一識別碼|
|modelId|string|是|保留檔案之語言模型的識別碼 (建立語言模型時產生)|
|fileId|string|是|要更新之檔案的識別碼 (在建立或更新語言模型期間上傳檔案時產生)|
|accessToken|string|是|用來針對呼叫進行驗證的存取權杖 (必須是[帳戶存取權杖](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) \(英文\) 範圍)。 存取權杖會在 1 小時後過期。|

### <a name="request-body"></a>Request body

此呼叫不需要其他要求本文。

### <a name="response"></a>Response

從語言模型成功刪除檔案時，沒有任何傳回的內容。

## <a name="get-metadata-on-a-file-from-a-language-model"></a>從語言模型取得檔案的中繼資料

這會從您帳戶中選擇的語言模型，傳回指定之檔案的內容和中繼資料。

### <a name="request-url"></a>要求 URL

這是 GET 要求。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

以下是以 Curl 撰寫的要求。
```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[查看所需的參數，並使用影片索引器開發人員入口網站進行測試](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) \(英文\)。

### <a name="request-parameters"></a>要求參數 

|**名稱**|**類型**|**必要**|**說明**|
|---|---|---|---|
|location|string|是|應該路由傳送呼叫的目標 Azure 區域。 如需詳細資訊，請參閱 [Azure 區域和影片索引器](regions.md)。|
|accountID|string|是|帳戶的全域唯一識別碼|
|modelId|string|是|保留檔案之語言模型的識別碼 (建立語言模型時產生)|
|fileId|string|是|要更新之檔案的識別碼 (在建立或更新語言模型期間上傳檔案時產生)|
|accessToken|string|是|用來針對呼叫進行驗證的存取權杖 (必須是[帳戶存取權杖](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) \(英文\) 範圍)。 存取權杖會在 1 小時後過期。|

### <a name="request-body"></a>Request body

此呼叫不需要其他要求本文。

### <a name="response"></a>Response

回應會以 JSON 格式，提供檔案的內容和中繼資料，類似如下：

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> 此範例檔案的內容是 "hello" 和 "world" 在兩個不同的行。

## <a name="download-a-file-from-a-language-model"></a>從語言模型下載檔案

下列命令會從指定之帳戶中指定的語言模型，下載包含指定之檔案內容的文字檔。 此文字檔應符合原本上傳之文字檔的內容。

### <a name="request-url"></a>要求 URL
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}
```

以下是以 Curl 撰寫的要求。

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}"
```
 
[查看所需的參數，並使用影片索引器開發人員入口網站進行測試](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) \(英文\)。

### <a name="request-parameters"></a>要求參數 

|**名稱**|**類型**|**必要**|**說明**|
|---|---|---|---|
|location|string|是|應該路由傳送呼叫的目標 Azure 區域。 如需詳細資訊，請參閱 [Azure 區域和影片索引器](regions.md)。|
|accountID|string|是|帳戶的全域唯一識別碼|
|modelId|string|是|保留檔案之語言模型的識別碼 (建立語言模型時產生)|
|fileId|string|是|要更新之檔案的識別碼 (在建立或更新語言模型期間上傳檔案時產生)|
|accessToken|string|是|用來針對呼叫進行驗證的存取權杖 (必須是[帳戶存取權杖](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) \(英文\) 範圍)。 存取權杖會在 1 小時後過期。|

### <a name="request-body"></a>Request body 

此呼叫不需要其他要求本文。

### <a name="response"></a>Response

回應將會是以 JSON 格式，下載具有檔案內容的文字檔。 

## <a name="next-steps"></a>後續步驟

[使用網站自訂語言模型](customize-language-model-with-website.md)
