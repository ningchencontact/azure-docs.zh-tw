---
title: 快速入門：執行新聞搜尋 - 適用於 Node.js 的 Bing 新聞搜尋 SDK
titleSuffix: Azure Cognitive Services
description: 使用此快速入門以運用「適用於 Node.js 的 Bing 新聞搜尋 SDK」來搜尋新聞，並處理回應。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 0fa78474d6400f890626859c32915c8657f92d4e
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2018
ms.locfileid: "53249340"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>快速入門：使用適用於 Node.js 的 Bing 新聞搜尋 SDK 來執行新聞搜尋

Bing 新聞搜尋 SDK 包含用於新聞查詢以及剖析結果的 REST API 功能。 

[節點 Bing 新聞搜尋 SDK 範例的原始程式碼](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js)可從 Git Hub 取得。

## <a name="application-dependencies"></a>應用程式相依性
在「搜尋」下取得[認知服務存取金鑰](https://azure.microsoft.com/try/cognitive-services/)。  另請參閱[認知服務定價 - Bing 搜尋 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。 

若要使用 Bing 新聞搜尋 SDK 來設定主控台應用程式：
* 在開發環境中執行 `npm install ms-rest-azure`。
* 在開發環境中執行 `npm install azure-cognitiveservices-newssearch`。

## <a name="news-search-client"></a>新聞搜尋用戶端
在「搜尋」下取得[認知服務存取金鑰](https://azure.microsoft.com/try/cognitive-services/)。 建立 `CognitiveServicesCredentials` 的執行個體：
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
然後，將用戶端具現化：
```
const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
let client = new NewsSearchAPIClient(credentials);
```
使用用戶端以利用查詢文字搜尋，在本例中為 'Winter Olympics'：
```
client.newsOperations.search('Winter Olympics').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```
程式碼會將 `result.value` 項目列印到主控台，而不會剖析任何文字。 各個類別的結果 (若有的話) 將會包含：
- _type:'NewsArticle'
- _type:'WebPage'
- _type:'VideoObject'
- _type:'ImageObject'

<!-- Remove until we can replace with santized version
![News results](media/node-sdk-quickstart-results.png)
-->

## <a name="next-steps"></a>後續步驟

[認知服務 Node.js SDK 範例](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
