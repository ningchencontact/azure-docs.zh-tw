---
title: 快速入門：使用適用於 Node.js 的 Bing 圖像式搜尋 SDK 來取得影像見解
titleSuffix: Azure Cognitive Services
description: 使用 Bing 圖像式搜尋 SDK 上傳影像並取得其見解。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/18/2018
ms.author: v-gedod
ms.openlocfilehash: c46c33b81a1162bdb30edc7a39a04fea8c0abb7d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55866928"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-nodejs"></a>快速入門：使用適用於 Node.js 的 Bing 圖像式搜尋 SDK 來取得影像見解

使用本快速入門，開始使用 Node.js SDK 從 Bing 圖像式搜尋服務取得影像見解。 雖然 Bing 圖像式搜尋具有與大部分程式設計語言相容的 REST API，但 SDK 會提供簡單的方法，將服務整合到您的應用程式。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js) 上找到。 

## <a name="prerequisites"></a>必要條件
* [Node.js](https://www.nodejs.org/)
* 適用於 Node.js 的 Bing 圖像式搜尋 SDK
    * 若要使用 Bing 圖像式搜尋 SDK 來設定主控台應用程式，請執行下列命令：
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-search-visualSearch` 。


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在您最愛的 IDE 或編輯器中建立新的 JavaScript 檔案，然後新增下列需求。 接著為您的訂用帳戶金鑰、自訂組態識別碼，以及您要上傳影像的檔案路徑，建立變數。 

    ```javascript
    const os = require("os");
    const async = require('async');
    const fs = require('fs');
    const Search = require('azure-cognitiveservices-visualsearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    
    let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
    let credentials = new CognitiveServicesCredentials(keyVar);
    let filePath = "../Data/image.jpg";
    ```

2. 具現化用戶端。

    ```javascript
    let visualSearchClient = new Search.VisualSearchClient(credentials);
    ```

## <a name="search-for-images"></a>搜尋影像

1. 使用 `fs.createReadStream()` 來讀入您的影像檔案，以及為您的搜尋要求和結果建立變數。 然後使用用戶端來搜尋影像。

    ```javascript
    let fileStream = fs.createReadStream(filePath);
    let visualSearchRequest = JSON.stringify({});
    let visualSearchResults;
    try {
        visualSearchResults = await visualSearchClient.images.visualSearch({
            image: fileStream,
            knowledgeRequest: visualSearchRequest
        });
        console.log("Search visual search request with binary of image");
    } catch (err) {
        console.log("Encountered exception. " + err.message);
    }
    ```

2. 剖析先前查詢的結果：

    ```javascript
    // Visual Search results
    if (visualSearchResults.image.imageInsightsToken) {
        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
    }
    else {
        console.log("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.tags.length > 0) {
        let firstTagResult = visualSearchResults.tags[0];
        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);
    
        // List of actions in first tag
        if (firstTagResult.actions.length > 0) {
            let firstActionResult = firstTagResult.actions[0];
            console.log(`First tag action count: ${firstTagResult.actions.length}`);
            console.log(`First tag action type: ${firstActionResult.actionType}`);
        }
        else {
            console.log("Couldn't find tag actions!");
        }
    
    }
    else {
        console.log("Couldn't find image tags!");
    }
    
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置單頁 Web 應用程式](tutorial-bing-visual-search-single-page-app.md)
