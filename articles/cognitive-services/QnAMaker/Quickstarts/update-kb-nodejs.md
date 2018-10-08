---
title: 快速入門：Node.js 更新知識庫 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 如何使用 Node.js 來更新 QnA Maker 的知識庫。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 09/12/2018
ms.author: diberry
ms.openlocfilehash: a987993da5202abc9b543aa2dba0f080a622e199
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033612"
---
# <a name="update-a-knowledge-base-in-nodejs"></a>使用 Node.js 來更新知識庫

以下程式碼使用 [Update](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) 方法來更新現有的知識庫。

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

如果您還沒有知識庫，可以建立要用於本快速入門的範例知識庫：[建立新的知識庫](create-new-kb-nodejs.md)。

1. 在您偏好的 IDE 中建立新的 Node.js 專案。 使用 JavaScript 版本 ECMAScript 6+。
1. 新增下方提供的程式碼。
1. 將 `subscriptionKey` 值換成您的有效訂用帳戶金鑰。
1. 將 `kb` 值換成您的有效知識庫識別碼。 藉由移至您的其中一個 [QnA Maker 知識庫](https://www.qnamaker.ai/Home/MyServices) (英文)，來尋找此值。 選取您想要更新的知識庫。 一旦移至該頁面，請在 URL 中尋找 'kdid='，如下所示。 針對您的程式碼範例使用其值。

    ![QnA Maker 知識庫識別碼](../media/qnamaker-quickstart-kb/qna-maker-id.png)

1. 執行程式。

```nodejs
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
let subscriptionKey = 'ADD KEY HERE';

// Replace this with a valid knowledge base ID.
let kb = 'ADD ID HERE';

// Represents the various elements used to create HTTP request URIs
// for QnA Maker operations.
let host = 'westus.api.cognitive.microsoft.com';
let service = '/qnamaker/v4.0';
let method = '/knowledgebases/';

// Formats and indents JSON for display.
let pretty_print = function(s) {
    return JSON.stringify(JSON.parse(s), null, 4);
}

// Call 'callback' after we have the entire response.
let response_handler = function (callback, response) {
    let body = '';
    response.on('data', function(d) {
        body += d;
    });
    response.on('end', function() {
    // Calls 'callback' with the status code, headers, and body of the response.
    callback ({ status : response.statusCode, headers : response.headers, body : body });
    });
    response.on('error', function(e) {
        console.log ('Error: ' + e.message);
    });
};

// HTTP response handler calls 'callback' after we have the entire response.
let get_response_handler = function(callback) {
    // Return a function that takes an HTTP response and is closed over the specified callback.
    // This function signature is required by https.request, hence the need for the closure.
    return function(response) {
        response_handler(callback, response);
    }
}

// Calls 'callback' after we have the entire PATCH request response.
let patch = function(path, content, callback) {
    let request_params = {
        method : 'PATCH',
        hostname : host,
        path : path,
        headers : {
            'Content-Type' : 'application/json',
            'Content-Length' : content.length,
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

    // Pass the callback function to the response handler.
    let req = https.request(request_params, get_response_handler(callback));
    req.write(content);
    req.end ();
}

// Calls 'callback' after we have the entire GET request response.
let get = function(path, callback) {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

    // Pass the callback function to the response handler.
    let req = https.request(request_params, get_response_handler(callback));
    req.end ();
}

// Calls 'callback' after we have the response from the /knowledgebases PATCH method.
let update_kb = function(path, req, callback) {
    console.log('Calling ' + host + path + '.');
    // Send the PATCH request.
    patch(path, req, function (response) {
        // Extract the data we want from the PATCH response and pass it to the callback function.
        callback({ operation : response.headers.location, response : response.body });
    });
}

// Calls 'callback' after we have the response from the GET request to check the status.
let check_status = function(path, callback) {
    console.log('Calling ' + host + path + '.');
    // Send the GET request.
    get(path, function (response) {
        // Extract the data we want from the GET response and pass it to the callback function.
        callback({ wait : response.headers['retry-after'], response : response.body });
    });
}

// Dictionary that holds the knowledge base. Modify knowledge base here.
let req = {
  'add': {
    'qnaList': [
      {
        'id': 1,
        'answer': 'You can change the default message if you use the QnAMakerDialog. See this for details: https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle',
        'source': 'Custom Editorial',
        'questions': [
          'How can I change the default message from QnA Maker?'
        ],
        'metadata': []
      }
    ],
    'urls': []
  },
  'update' : {
    'name' : 'New KB Name'
  },
  'delete': {
    'ids': [
      0
    ]
  }
};

var path = service + method + kb;
// Convert the request to a string.
let content = JSON.stringify(req);

// Sends the request to update the knowledge base.
update_kb(path, content, function (result) {
    console.log(pretty_print(result.response));
    // Loop until the operation is complete.
    let loop = function() {
        path = service + result.operation;
        // Check the status of the operation.
        check_status(path, function(status) {
            // Write out the status.
            console.log(pretty_print(status.response));
            // Convert the status into an object and get the value of the operationState field.
            var state = (JSON.parse(status.response)).operationState;
            // If the operation isn't complete, wait and query again.
            if (state == 'Running' || state == 'NotStarted') {
                console.log('Waiting ' + status.wait + ' seconds...');
                setTimeout(loop, status.wait * 1000);
            }
        });
    }
    // Begin the loop.
    loop();
});
```

## <a name="understand-what-qna-maker-returns"></a>了解 QnA Maker 會傳回的項目

成功的回應會以 JSON 格式傳回，如下列範例所示。 您的結果可能會稍有不同。 如果最後呼叫傳回「成功」狀態，代表您的知識庫已成功更新。 若要進行疑難排解，請參閱 QnA Maker API 的[更新知識庫](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) (英文) 回應碼。

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:48Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:50Z",
  "resourceLocation": "/knowledgebases/140a46f3-b248-4f1b-9349-614bfd6e5563",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
Press any key to continue.
```

一旦更新您的知識庫，您可以在 QnA Maker 入口網站的[我的知識庫](https://www.qnamaker.ai/Home/MyServices) (英文) 頁面中檢視。 請注意，您的知識庫現在有稱為「新的知識庫名稱」的新名稱，以及其他的 QnA 配對。

若要修改知識庫的其他元素，請參閱 QnA Maker [JSON 結構描述](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) (英文)，並且修改 `req` 字串。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)