---
title: 教學課程：使用 Node.js 啟動沈浸式閱讀程式
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，您將建立會啟動沈浸式閱讀程式的 Node.js 應用程式。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: f8697042ed46e0ff333f736454346908d76cf039
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718368"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>教學課程：啟動沈浸式閱讀程式 (Node.js)

在[概觀](./overview.md)中，您會了解什麼是沈浸式閱讀程式，及其如何實作經實證的技術，針對語言學習者、沈浸式讀者及存在學習差異的學生改善其閱讀理解程度。 本教學課程涵蓋如何建立會啟動沈浸式閱讀程式的 Node.js Web 應用程式。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 使用 Express 建立 Node.js Web 應用程式
> * 取得存取權杖
> * 使用範例內容啟動沈浸式閱讀程式
> * 指定內容的語言
> * 指定沈浸式閱讀程式介面的語言
> * 使用數學內容啟動沈浸式閱讀程式

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

* 沈浸式閱讀程式的訂用帳戶金鑰。 依照[下列指示](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)取得金鑰。
* [Node.js](https://nodejs.org/) 和 [Yarn](https://yarnpkg.com)
* 整合式開發環境，例如 [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>使用 Express 建立 Node.js Web 應用程式

使用 `express-generator` 工具建立 Node.js Web 應用程式。

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

安裝 yarn 的相依項目，並新增相依性 `request` 和 `dotenv`，其在本教學課程稍後將會使用。

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-access-token"></a>取得存取權杖

接下來，撰寫後端 API 以使用訂用帳戶金鑰來取出存取權杖。 這個步驟您需要訂用帳戶金鑰和端點。 您可以在 Azure 入口網站沈浸式閱讀程式資源的 [金鑰] 頁面中找到您的訂用帳戶金鑰。 您可以在 [概觀] 頁面中找到您的端點。

擁有訂用帳戶金鑰和端點之後，請建立名為 _.env_ 的新檔案，然後將下列程式碼貼入，分別使用您的訂用帳戶金鑰和端點取代 `{YOUR_SUBSCRIPTION_KEY}` 和 `{YOUR_ENDPOINT}`。

```text
SUBSCRIPTION_KEY={YOUR_SUBSCRIPTION_KEY}
ENDPOINT={YOUR_ENDPOINT}
```

請勿將此檔案認可到原始檔控制，因為它包含不應公開的機密資料。

接著，開啟 _app.js_ 並將下列新增至檔案開頭處。 這樣可將訂用帳戶金鑰和端點以環境變數載入 Node。

```javascript
require('dotenv').config();
```

開啟 _routes\index.js_ 檔案和檔案頂端的下列匯入：

```javascript
var request = require('request');
```

接著，將下列程式碼直接加在該行下方。 此程式碼會建立可使用訂用帳戶金鑰取得存取權杖的 API 端點，然後傳回該權杖。

```javascript
router.get('/token', function(req, res, next) {
  request.post({
    headers: {
        'Ocp-Apim-Subscription-Key': process.env.SUBSCRIPTION_KEY,
        'content-type': 'application/x-www-form-urlencoded'
    },
    url: process.env.ENDPOINT
  },
  function(err, resp, token) {
    return res.send(token);
  });
});
```

此 API 端點應以某種形式的驗證方法受到保護 (例如 [OAuth](https://oauth.net/2/))，該作業已超出本教學課程的範圍。

## <a name="launch-the-immersive-reader-with-sample-content"></a>使用範例內容啟動沈浸式閱讀程式

1. 開啟 _views\layout.pug_，並在 `head` 標籤底下、`body` 標籤之前新增下列程式碼。 這些 `script` 標籤會載入[沈浸式閱讀程式 SDK](https://github.com/Microsoft/immersive-reader-sdk) 和 jQuery。

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. 開啟 _views\index.pug_，並以下列程式碼取代其內容。 此程式碼會將某些範例內容填入頁面，並新增啟動沈浸式閱讀程式的按鈕。

    ```pug
    extends layout

    block content
      h2(id='title') Geography
      p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
      div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
      script.
        function launchImmersiveReader() {
          // First, get a token using our /token endpoint
          $.ajax('/token', { success: token => {
            // Second, grab the content from the page
            const content = {
              title: document.getElementById('title').innerText,
              chunks: [ {
                content: document.getElementById('content').innerText + '\n\n',
                lang: 'en'
              } ]
            };

            // Third, launch the Immersive Reader
            ImmersiveReader.launchAsync(token, content);
          }});
        }
    ```

3. Web 應用程式現在已經準備好了。 執行下列動作以啟動應用程式︰

    ```bash
    npm start
    ```

4. 開啟瀏覽器並巡覽至 _http://localhost:3000_ 。 在頁面上您應看到上述內容。 按一下 [沈浸式閱讀程式]  按鈕，將您的內容以沈浸式閱讀程式啟動。

## <a name="specify-the-language-of-your-content"></a>指定內容的語言

沈浸式閱讀程式支援許多不同的語言。 遵循下列步驟，即可指定內容的語言。

1. 開啟 _views\index.pug_，並在上一個步驟所新增的 `p(id=content)` 標籤下方新增下列程式碼。 此程式碼會將某些西班牙文的內容新增至您的頁面。

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. 在 JavaScript 程式碼中，在 `ImmersiveReader.launchAsync` 呼叫上方新增下列內容。 此程式碼會將西班牙文的內容傳遞至沈浸式閱讀程式。

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. 再次瀏覽至 _http://localhost:3000_ 。 您應該會在頁面上看到西班牙文文字，當您按一下 [沈浸式閱讀程式]  ，它也會顯示在沈浸式閱讀程式中。

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>指定沈浸式閱讀程式介面的語言

根據預設，沈浸式閱讀程式介面的語言會符合瀏覽器的語言設定。 您也可以使用下列程式碼指定沈浸式閱讀程式介面的語言。

1. 在 _views\index.pug_ 中，以下列程式碼取代 `ImmersiveReader.launchAsync(token, content)` 呼叫。

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, content, options);
    ```

2. 瀏覽至 _http://localhost:3000_ 。 當您啟動沈浸式閱讀程式，介面會以法文顯示。

## <a name="launch-the-immersive-reader-with-math-content"></a>使用數學內容啟動沈浸式閱讀程式

您可以使用 [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML) 在沈浸式閱讀程式中包含數學內容。

1. 修改 _views\index.pug_ 以在 `ImmersiveReader.launchAsync` 呼叫上方包含下列程式碼：

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. 瀏覽至 _http://localhost:3000_ 。 當您啟動沈浸式閱讀程式並捲動至底部時，您會看到數學公式。

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK](https://github.com/Microsoft/immersive-reader-sdk) 和[沈浸式閱讀程式 SDK 參考](./reference.md)
* 檢視 [GitHub](https://github.com/microsoft/immersive-reader-sdk/samples/advanced-csharp) 上的程式碼範例