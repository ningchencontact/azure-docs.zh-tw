---
title: 快速入門：在 Node.js 中使用 SDK 來要求和篩選影像
description: 在本快速入門中，您可以使用 Node.js 來要求和篩選 Bing 影像搜尋所傳回的影像。
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: e88c045b220192a617e6b8caf5d8d53f70a25b5e
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2018
ms.locfileid: "42093831"
---
# <a name="quickstart-request-and-filter-images-using-the-sdk-and-nodejs"></a>快速入門：使用 SDK 和 Node.js 來要求和篩選影像

Bing 影像搜尋 SDK 包含用於影像查詢以及剖析結果的 REST API 功能。 

[Node Bing 影像搜尋 SDK 範例的原始程式碼](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js)可從 Git Hub 取得。

## <a name="application-dependencies"></a>應用程式相依性

若要使用 Bing 影像搜尋 SDK 來設定主控台應用程式，請在您的開發環境中執行 `npm install azure-cognitiveservices-imagesearch`。

## <a name="image-search-client"></a>影像搜尋用戶端
在 [搜尋] 下取得[認知服務存取金鑰](https://azure.microsoft.com/try/cognitive-services/)。 建立 `CognitiveServicesCredentials` 的執行個體：
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
然後，具現化用戶端：
```
const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
let client = new ImageSearchAPIClient(credentials);
```
使用用戶端以利用查詢文字搜尋，在此情況下為 'El Capitan'：
```
client.imagesOperations.search('El Capitan', function (err, result, request, response) {
    if (err) throw err;
    console.log(result.value);
});

```
<!-- Need to sanitize result
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'ImageObjectElementType'

![Imageresults](media/node-sdk-quickstart-image-results.png)
-->

## <a name="next-steps"></a>後續步驟

[認知服務 Node.js SDK 範例](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)