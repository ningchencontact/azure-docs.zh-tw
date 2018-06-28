---
title: 使用 Node.js 呼叫端點 - Bing 自訂搜尋 - Microsoft 認知服務
description: 本快速入門說明如何使用 Node.js 呼叫自訂的 Bing 搜尋端點，以從您的自訂搜尋執行個體要求搜尋結果。
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 48fc234e15ce3b9172d766f6fae11b51a017ce70
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "35370742"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>呼叫 Bing 自訂搜尋端點 (Node.js)

本快速入門說明如何使用 Node.js 呼叫自訂的 Bing 搜尋端點，以從您的自訂搜尋執行個體要求搜尋結果。 

## <a name="prerequisites"></a>先決條件
若要完成本快速入門，您需要：

- 自訂搜尋執行個體。 請參閱[建立您的第一個 Bing 自訂搜尋執行個體](quick-start.md)。

- 已安裝 [Node.js](https://www.nodejs.org/)。

-  具有 **Bing 搜尋 API** 的[認知服務 API 帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 [免費試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)即足以供本快速入門使用。 您必須要有啟動免費試用版時所提供的存取金鑰，或者您可以從 Azure 儀表板使用付費訂用帳戶金鑰。

## <a name="run-the-code"></a>執行程式碼

若要呼叫 Bing 自訂搜尋端點，請遵循下列步驟：

1. 建立程式碼的資料夾。
2. 從命令提示字元或終端機，瀏覽至您剛剛建立的資料夾。
3. 安裝**要求**節點模組：
    <pre>
    npm install request
    </pre>
4. 會建立 BingCustomSearch.js 檔案，並將下列程式碼複製到該檔案。
5. 將 **YOUR-SUBSCRIPTION-KEY** 和 **YOUR-CUSTOM-CONFIG-ID** 取代為您的金鑰和組態識別碼 (請參閱步驟 1)。

    ``` javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    
    var options = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
          'q=' + searchTerm + 
          '&customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    
    request(options, function(error, response, body){
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
    })
    ```
6. 使用下列命令來執行程式碼。
    ```    
    node BingCustomSearch.js
   ``` 

## <a name="next-steps"></a>後續步驟
- [設定託管的 UI 體驗](./hosted-ui.md)
- [使用裝飾標記醒目提示文字](./hit-highlighting.md)
- [頁面網頁](./page-webpages.md)