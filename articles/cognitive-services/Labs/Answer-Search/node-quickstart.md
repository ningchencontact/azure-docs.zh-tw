---
title: Microsoft 認知服務的 Node 快速入門 - 專案答案搜尋 | Microsoft Docs
description: 在 Azure 上開始使用 Microsoft 認知服務「專案答案搜尋」。
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 36b2709d39230aae7929164ba4c9306f57043b43
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369570"
---
# <a name="project-answer-search-node-quickstart"></a>專案答案搜尋 Node 快速入門

下列 Node 範例建立 Yosemite 國家公園相關資訊的查詢。

## <a name="prerequisites"></a>先決條件

取得免費試用版[認知服務實驗室](https://aka.ms/answersearchsubscription)的存取金鑰

此範例使用 Node v8.9.4

## <a name="code-scenario"></a>程式碼案例 

下列程式碼取得答案。
實際進行步驟如下：
1. 宣告變數來依主機及路徑指定端點。
2. 指定要預覽的查詢 URL，並加入查詢參數。  
3. 建立回應的處理常式函式。
4. 定義建立要求的搜尋函式，並加入 *Ocp-Apim-Subscription-Key* 標頭。
5. 執行搜尋函式。 

此示範的完整程式碼如下：

````
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'; 

let host = 'api.labs.cognitive.microsoft.com';
let path = '/answerSearch/v7.0/search';

let mkt = 'en-us';
let q = 'Yosemite National Park';

let params = '?q=' + encodeURI(q) + '&mkt=en-us';

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

````

## <a name="next-steps"></a>後續步驟
- [C# 範例程式碼](c-sharp-quickstart.md)
- [Java 快速入門](java-quickstart.md)
- [Python 快速入門](python-quickstart.md)