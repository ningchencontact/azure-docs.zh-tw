---
title: Web 搜尋 SDK Node 快速入門 | Microsoft Docs
description: 設定 Web 搜尋 SDK 主控台應用程式。
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 44f7f97f6c442df3fbb1e5e08189b8db7d4b9db0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370454"
---
# <a name="web-search-sdk-node-quickstart"></a>Web 搜尋 SDK Node 快速入門

Bing Web 搜尋 SDK 包含用於 Web 查詢以及剖析結果的 REST API 功能。

[Node Bing Web 搜尋 SDK 範例的原始程式碼](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/webSearch.js)可從 Git Hub 取得。

## <a name="application-dependencies"></a>應用程式相依性

若要使用 Bing Web 搜尋 SDK 來設定主控台應用程式，請在您的開發環境中執行 `npm install azure-cognitiveservices-websearch`。

## <a name="web-search-client"></a>Web 搜尋用戶端
在 [搜尋] 下取得[認知服務存取金鑰](https://azure.microsoft.com/try/cognitive-services/)。 建立 `CognitiveServicesCredentials` 的執行個體：
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
然後，具現化用戶端：
```
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```
搜尋結果：
```
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})

```
程式碼會將 `result.value` 項目列印到主控台，而不會剖析任何文字。  各個類別的結果 (若有的話) 將會包含：
- _type：'ImageObject'
- _type：'NewsArticle'
- _type：'WebPage'
- _type：'VideoObjectElementType'

<!-- Remove until this can be replaced with a sanitized version.
![Video results](media/web-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>後續步驟

[認知服務 Node.js SDK 範例](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
