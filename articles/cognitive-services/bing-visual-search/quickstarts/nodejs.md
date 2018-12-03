---
title: 快速入門：建立圖像式搜尋查詢 (Node.js) - Bing 圖像式搜尋
titleSuffix: Azure Cognitive Services
description: 如何將影像上傳到 Bing 圖像式搜尋 API，並取得影像的深入解析。
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 553d068d70f7e722f3c8e4de3978f3583b941963
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442519"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-javascript"></a>快速入門：使用 JavaScript 的第一個 Bing 圖像式搜尋查詢

Bing 圖像式搜尋 API 會傳回您所提供影像的相關資訊。 您可以使用影像的 URL、見解權杖，或上傳影像來提供影像。 如需這些選項的資訊，請參閱[什麼是 Bing 圖像式搜尋 API？](../overview.md) 本文將示範如何上傳影像。 在拍攝知名地標的照片並取回其相關資訊的行動裝置案例中，上傳影像可能很有用。 例如，見解可能包含關於地標的雜項。 

若您上傳本機影像，以下會顯示您必須包含在 POST 本文中的表單資料。 表單資料必須包含 Content-Disposition 標頭。 其 `name` 參數必須設定為 "image"，而 `filename` 參數可以設定為任何字串。 表單的內容是影像的二進位檔案。 您可以上傳的影像大小上限為 1 MB。 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

本文包含簡單的主控台應用程式，它會傳送 Bing 圖像式搜尋 API 要求，並顯示 JSON 搜尋結果。 雖然此應用程式是以 JavaScript 撰寫，但 API 是一種與任何程式語言相容的 RESTful Web 服務，可提出 HTTP 要求並剖析 JSON。 

## <a name="prerequisites"></a>必要條件
為使用本快速入門，您必須在 S9 定價層上啟用訂用帳戶，如[認知服務定價 - Bing 搜尋 API](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/) 中所示。 

若要在 Azure 入口網站中啟用訂用帳戶：
1. 在 Azure 入口網站頂端顯示 `Search resources, services, and docs` 的文字方塊中輸入 'BingSearchV7'。  
2. 在下拉式清單中的 Marketplace 下選取 `Bing Search v7`。
3. 輸入新資源的 `Name`。
4. 選取 `Pay-As-You-Go` 訂用帳戶。
5. 選取 `S9` 定價層。
6. 按一下 `Enable` 來啟用訂用帳戶。

您需要有 [Node.js 6](https://nodejs.org/en/download/)，才能執行此程式碼。

## <a name="running-the-application"></a>執行應用程式

以下顯示如何使用 Node.js 中的 FormData 來傳送訊息。

若要執行此應用程式，請遵循下列步驟：

1. 建立專案的資料夾 (或使用您最愛的 IDE 或編輯器)。
2. 從命令提示字元或終端機，巡覽至您剛剛建立的資料夾。
3. 安裝 request 模組：  
  ```  
  npm install request  
  ```  
3. 安裝 form-data 模組：  
  ```  
  npm install form-data  
  ```  
4. 建立名為 GetVisualInsights.js 的檔案，並在其中新增下列程式碼。
5. 以您的訂用帳戶金鑰取代 `subscriptionKey` 值。
6. 以要上傳之影像的路徑取代 `imagePath` 值。
7. 執行程式。  
  ```
  node GetVisualInsights.js
  ```

```javascript
var request = require('request');
var FormData = require('form-data');
var fs = require('fs');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';
var imagePath = "<pathtoyourimagegoeshere>";

var form = new FormData();
form.append("image", fs.createReadStream(imagePath));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```


## <a name="next-steps"></a>後續步驟

[使用見解權杖取得影像的相關見解](../use-insights-token.md)  
[Bing 圖像式搜尋影像上傳教學課程](../tutorial-visual-search-image-upload.md)
[Bing 圖像式搜尋單頁應用程式教學課程](../tutorial-bing-visual-search-single-page-app.md)  
[Bing 圖像式搜尋概觀](../overview.md)  
[試試看](https://aka.ms/bingvisualsearchtryforfree)  
[取得免費試用的存取金鑰](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing 圖像式搜尋 API 參考](https://aka.ms/bingvisualsearchreferencedoc)
