---
title: 快速入門：取得支援的語言清單 (Node.js) - 翻譯工具文字 API
titleSuffix: Azure Cognitive Services
description: 在此快速入門中，您可以取得搭配 Node.js 使用翻譯工具文字 API 來翻譯、音譯及查閱字典時，所支援的語言清單與範例。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cb01269d21d3b036dfff84faef1903dc082dc6f1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445048"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-with-nodejs"></a>快速入門：搭配使用翻譯工具文字 API 和 Node.js 取得支援的語言清單

在此快速入門中，您將了解如何搭配使用 Node.js 與翻譯工具文字 REST API 發出 GET 要求，以傳回支援的語言清單。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* [Node 8.12.x 或更新版本](https://nodejs.org/en/)

## <a name="create-a-project-and-import-required-modules"></a>建立專案，並匯入所需的模組

使用您慣用的 IDE 或編輯器建立新的專案。 然後，將下列程式碼片段複製到您的專案中名為 `get-languages.js` 的檔案。

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> 如果您未曾使用這些模組，則必須先加以安裝，再執行您的程式。 若要安裝這些套件，請執行：`npm install request uuidv4`。

需有這些模組，才能建構 HTTP 要求，以及建立 `'X-ClientTraceId'` 標頭的唯一識別碼。

## <a name="configure-the-request"></a>設定要求

`request()` 方法 (可透過要求模組取得) 可讓我們傳遞 HTTP 方法、URL、要求參數、標頭和 JSON 內文作為 `options` 物件。 在此程式碼片段中，我們將設定要求：

>[!NOTE]
> 如需關於端點、路由和要求參數的詳細資訊，請參閱[翻譯工具文字 API 3.0：語言](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages)。

```javascript
let options = {
    method: 'GET',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'languages',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    json: true,
};
```

如果您使用認知服務的多服務訂用帳戶，您也必須在要求參數中包含 `Ocp-Apim-Subscription-Region`。 [深入了解使用多服務訂用帳戶進行驗證](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。 

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
node get-languages.js
```

如果想要將您的程式碼與我們的做比較，請使用 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS) 上提供的完整範例。

## <a name="sample-response"></a>範例回應

請在此[語言清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)中尋找國家/地區縮寫。

本範例已截斷而僅顯示結果的片段：

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
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
* [從輸入識別語言](quickstart-nodejs-detect.md)
* [取得替代的翻譯](quickstart-nodejs-dictionary.md)
* [從輸入判斷句子長度](quickstart-nodejs-sentences.md)
