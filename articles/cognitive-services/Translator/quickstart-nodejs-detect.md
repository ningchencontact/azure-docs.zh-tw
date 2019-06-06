---
title: 快速入門：偵測文字語言 (Node.js) - 翻譯工具文字 API
titleSuffix: Azure Cognitive Services
description: 在此快速入門中，您將了解如何搭配使用 Node.js 與翻譯工具文字 REST API 來識別所提供文字的語言。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: 2a22584cdace196287f5475536ac019d8dcfc15d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515182"
---
# <a name="quickstart-use-the-translator-text-api-to-detect-text-language-with-nodejs"></a>快速入門：使用翻譯工具文字 API 搭配 Node.js 來偵測文字語言

在此快速入門中，您將了解如何搭配使用 Node.js 與翻譯工具文字 REST API 來偵測所提供文字的語言。

本快速入門需要 [Azure 認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)和翻譯工具文字資源。 如果您還沒有帳戶，可以使用[免費試用](https://azure.microsoft.com/try/cognitive-services/)來取得訂用帳戶金鑰。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* [Node 8.12.x 或更新版本](https://nodejs.org/en/)
* 翻譯工具文字的 Azure 訂用帳戶金鑰

## <a name="create-a-project-and-import-required-modules"></a>建立專案，並匯入所需的模組

使用您慣用的 IDE 或編輯器建立新的專案。 然後，將下列程式碼片段複製到您的專案中名為 `detect.js` 的檔案。

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> 如果您未曾使用這些模組，則必須先加以安裝，再執行您的程式。 若要安裝這些套件，請執行：`npm install request uuidv4`。

需有這些模組，才能建構 HTTP 要求，以及建立 `'X-ClientTraceId'` 標頭的唯一識別碼。

## <a name="set-the-subscription-key"></a>設定訂用帳戶金鑰

此程式碼會嘗試從環境變數 `TRANSLATOR_TEXT_KEY` 中讀取您的翻譯工具文字訂用帳戶金鑰。 如果您不熟悉環境變數，您可以將 `subscriptionKey` 設為字串，並註解化條件陳述式。

請將下列程式碼複製到您的專案中：

```javascript
/* Checks to see if the subscription key is available
as an environment variable. If you are setting your subscription key as a
string, then comment these lines out.

If you want to set your subscription key as a string, replace the value for
the Ocp-Apim-Subscription-Key header as a string. */
const subscriptionKey = process.env.TRANSLATOR_TEXT_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};
```

## <a name="configure-the-request"></a>設定要求

`request()` 方法 (可透過要求模組取得) 可讓我們傳遞 HTTP 方法、URL、要求參數、標頭和 JSON 內文作為 `options` 物件。 在此程式碼片段中，我們將設定要求：

>[!NOTE]
> 如需關於端點、路由和要求參數的詳細資訊，請參閱[翻譯工具文字 API 3.0：偵測](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect)。

```javascript
let options = {
    method: 'POST',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'detect',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'Salve, mondo!'
    }],
    json: true,
};
```

### <a name="authentication"></a>Authentication

要驗證要求，最簡單的方式是將您的訂用帳戶金鑰以 `Ocp-Apim-Subscription-Key` 標頭的形式傳入，我們在此範例中即採用此方式。 或者，您可以將訂用帳戶金鑰替換為存取權杖，並將存取權杖連同 `Authorization` 標頭傳入，以驗證您的要求。 如需詳細資訊，請參閱[驗證](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。

## <a name="make-the-request-and-print-the-response"></a>提出要求並列印回應

接著，我們將使用 `request()` 方法建立要求。 它會採用我們在上一節中建立的 `options` 物件作為第一個引數，然後列印美化的 JSON 回應。

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> 在此範例中，我們會在 `options` 物件中定義 HTTP 要求。 不過，要求模組也支援便利方法，例如 `.post` 和 `.get`。 如需詳細資訊，請參閱[便利方法](https://github.com/request/request#convenience-methods)。

## <a name="put-it-all-together"></a>組合在一起

如此，您就建立了一個簡單的程式，會呼叫翻譯工具文字 API 並傳回 JSON 回應。 現在，請執行您的程式：

```console
node detect.js
```

如果想要將您的程式碼與我們的做比較，請使用 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS) 上提供的完整範例。

## <a name="sample-response"></a>範例回應

請在此[語言清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)中尋找國家/地區縮寫。

```json
[
    {
        "alternatives": [
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "pt",
                "score": 1.0
            },
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "en",
                "score": 1.0
            }
        ],
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "it",
        "score": 1.0
    }
]
```

## <a name="clean-up-resources"></a>清除資源

如果您已將訂用帳戶金鑰硬式編碼到您的程式中，請務必在完成本快速入門後移除訂用帳戶金鑰。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瀏覽 GitHub 上的 Node.js 範例](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS) (英文)

## <a name="see-also"></a>另請參閱

除了語言偵測外，請了解如何使用翻譯工具文字 API 執行下列作業：

* [翻譯文字](quickstart-nodejs-translate.md)
* [進行文字音譯](quickstart-nodejs-transliterate.md)
* [取得替代的翻譯](quickstart-nodejs-dictionary.md)
* [取得支援的語言清單](quickstart-nodejs-languages.md)
* [從輸入判斷句子長度](quickstart-nodejs-sentences.md)
