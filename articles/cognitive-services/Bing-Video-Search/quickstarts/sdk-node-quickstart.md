---
title: 快速入門：使用適用於 Node.js 的 Bing 影片搜尋 SDK 來搜尋影片
titleSuffix: Azure Cognitive Services
description: 透過本快速入門，使用適用於 Node.js 的 Bing 影片搜尋 SDK 來傳送影片搜尋要求
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: ffb04634b41d14e151c5a14d052d0d9f70b34d70
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2019
ms.locfileid: "55568000"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-nodejs"></a>快速入門：使用適用於 Node.js 的 Bing 影片搜尋 SDK 來執行影片搜尋

透過本快速入門開始使用適用於 Node.js 的 Bing 影片搜尋 SDK 來搜尋新聞。 雖然 Bing 影片搜尋具有與大部分程式設計語言相容的 REST API，但 SDK 會提供簡單的方法，將服務整合到您的應用程式。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) 上找到。 其中包含更多的註解和功能。

## <a name="prerequisites"></a>必要條件

- [Node.js](https://www.nodejs.org/)

若要使用 Bing 影片搜尋 SDK 來設定主控台應用程式：
* 在開發環境中執行 `npm install ms-rest-azure`。
* 在開發環境中執行 `npm install azure-cognitiveservices-videosearch`。

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在您最愛的 IDE 或編輯器中建立新的 JavaScript 檔案，然後為 Bing 影片搜尋 SDK 新增 `require()` 陳述式，以及新增 `CognitiveServicesCredentials` 模組。 建立訂用帳戶金鑰的變數。 
    
    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
    ```

2. 以您的金鑰建立 `CognitiveServicesCredentials` 的執行個體。 然後用其建立影片搜尋用戶端的執行個體。

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>傳送搜尋要求

1. 使用 `client.videosOperations.search()` 將搜尋要求傳送到 Bing 影片搜尋 API。 當搜尋結果傳回時，使用 `.then()` 來記錄結果。
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Remove until the response can be replace with a sanitized version.
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'VideoObjectElementType'

![Video results](media/video-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立單頁 Web 應用程式](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>另請參閱 

* [什麼是 Bing 影片搜尋 API？](../overview.md)
* [認知服務 .NET SDK 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)