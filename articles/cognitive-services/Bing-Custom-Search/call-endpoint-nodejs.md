---
title: 快速入門：使用 Node.js 來呼叫您的 Bing 自訂搜尋端點 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: 使用本快速入門以運用 Node.js 來開始向「Bing 自訂搜尋」執行個體要求搜尋結果
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 43b46b4dd035efa3117ce23728db42bb11a9ab6c
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790284"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>快速入門：使用 Node.js 來呼叫您的 Bing 自訂搜尋端點

您可以使用本快速入門來開始向「Bing 自訂搜尋」執行個體要求搜尋結果。 雖然此應用程式是以 JavaScript 撰寫的，但「Bing 自訂搜尋 API」是一種與大多數程式設計語言都相容的 RESTful Web 服務。 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js) 上有此範例的原始程式碼。

## <a name="prerequisites"></a>必要條件

- 「Bing 自訂搜尋」執行個體。 請參閱[快速入門：建立您的第一個 Bing 自訂搜尋執行個體](quick-start.md)，以取得詳細資訊。

- [Node.js](https://www.nodejs.org/)

- [JavaScript 要求程式庫](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在您最愛的 IDE 或編輯器中建立新的 JavaScript 檔案，然後為要求程式庫新增 `require()` 陳述式。 為您的訂用帳戶金鑰、「自訂設定識別碼」及搜尋字詞建立變數。 

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>傳送及接收搜尋要求 

1. 建立變數以儲存在您要求中傳送的資訊。 將搜尋字詞附加至 `q=` 查詢參數，並將搜尋執行個體的「自訂設定識別碼」附加至 `customconfig=`，以建構要求 URL。 使用 `&` 字元來分隔參數。 

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. 使用「JavaScript 要求程式庫」將搜尋要求傳送給「Bing 自訂搜尋」執行個體，並印出結果的相關資訊，包括其名稱、URL 及上次搜耙網頁的日期。

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置自訂搜尋 Web 應用程式](./tutorials/custom-search-web-page.md)
