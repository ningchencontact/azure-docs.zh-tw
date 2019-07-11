---
title: 快速入門：使用適用於 Node.js 的 Bing 新聞搜尋 SDK 來執行新聞搜尋
titleSuffix: Azure Cognitive Services
description: 使用此快速入門以運用「適用於 Node.js 的 Bing 新聞搜尋 SDK」來搜尋新聞，並處理回應。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: c71d76e93eb1a121163d40fe61dcd5a8546d63f8
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203349"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>快速入門：使用適用於 Node.js 的 Bing 新聞搜尋 SDK 來執行新聞搜尋

透過本快速入門開始使用適用於 Node.js 的 Bing 新聞搜尋 SDK 來搜尋新聞。 雖然 Bing 新聞搜尋具有與大部分程式設計語言相容的 REST API，但 SDK 會提供簡單的方法，將服務整合到您的應用程式。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) 上找到。

## <a name="prerequisites"></a>必要條件

* [Node.js](https://nodejs.org/en/)

若要使用 Bing 新聞搜尋 SDK 來設定主控台應用程式：
1. 在開發環境中執行 `npm install ms-rest-azure`。
2. 在開發環境中執行 `npm install azure-cognitiveservices-newssearch`。


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 建立 `CognitiveServicesCredentials` 執行個體。 針對您的訂用帳戶金鑰和搜尋字詞建立變數。

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. 具現化用戶端：
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>傳送搜尋查詢

1. 使用用戶端以使用查詢字詞搜尋，本例中使用 "Winter Olympics"：
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

程式碼會將 `result.value` 項目列印到主控台，而不會剖析任何文字。 各個類別的結果 (若有的話) 將會包含：

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立單頁 Web 應用程式](tutorial-bing-news-search-single-page-app.md)
