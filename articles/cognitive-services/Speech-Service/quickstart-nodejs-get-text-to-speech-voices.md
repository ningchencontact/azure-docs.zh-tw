---
title: 快速入門：列出文字轉換語音的語音，Node.js-語音服務
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將了解如何使用 Node.js 的區域/端點取得標準和類神經語音的完整清單。 清單會傳回為 JSON，而語音可用性會因區域而異。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: erhopf
ms.openlocfilehash: 62187ddbe587a81038f8424b079e3c0c313d1ae2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58887115"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>快速入門：取得文字轉換語音的語音使用 Node.js 的清單

在本快速入門中，您將了解如何使用 Node.js 的區域/端點取得標準和類神經語音的完整清單。 清單會傳回為 JSON，而語音可用性會因區域而異。 如需支援的區域的清單，請參閱 <<c0> [ 區域](regions.md)。

本快速入門需要[Azure 認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)與語音服務資源。 如果您還沒有帳戶，可以使用[免費試用](get-started.md)來取得訂用帳戶金鑰。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* [Node 8.12.x 或更新版本](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download)，或您最愛的文字編輯器
* 適用於語音服務的 Azure 訂用帳戶金鑰。 [免費取得一個金鑰!](get-started.md)

## <a name="create-a-project-and-require-dependencies"></a>建立專案並要求相依性

使用慣用的 IDE 或編輯器中建立新的 Node.js 專案。 然後，將下列程式碼片段複製到您的專案中名為 `get-voices.js` 的檔案。

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languagesto a file
const fs = require('fs');
```

> [!NOTE]
> 如果您未曾使用這些模組，則必須先加以安裝，再執行您的程式。 若要安裝這些套件，請執行：`npm install request request-promise`。

## <a name="get-an-access-token"></a>取得存取權杖

文字轉語音 REST API 需要存取權杖來進行驗證。 若要取得存取權杖，則必須進行交換。 此函式會交換語音服務的訂用帳戶金鑰的存取權杖使用`issueToken`端點。

這個範例假設您的語音服務訂用帳戶是在美國西部區域中。 如果您使用不同的區域，請更新 `uri` 的值。 如需完整清單，請參閱[區域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)。

請將下列程式碼複製到您的專案中：

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> 如需驗證的詳細資訊，請參閱[使用存取權杖進行驗證](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)。

在下一步 區段中，我們將建立函式，來取得語音的清單，並將 JSON 輸出儲存至檔案。

## <a name="make-a-request-and-save-the-response"></a>提出要求並儲存回應

在此您要建置要求，並儲存傳回語音的清單中。 此範例假設您會使用美國西部端點。 如果您的資源註冊至不同區域，請務必更新 `uri`。 如需詳細資訊，請參閱 <<c0> [ 語音服務區域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)。

接下來，新增必要的標頭的要求。 最後，您會對服務提出要求。 如果要求成功，而且會傳回狀態碼 200，回應會寫入至檔案。

```javascript
function textToSpeech(accessToken) {
    let options = {
        method: 'GET',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/voices/list',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'Content-Type': 'application/json'
        }
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('voices.json'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>組合在一起

就快要完成了。 最后一步是创建异步函数。 此函式會讀取環境變數中的訂用帳戶金鑰，取得權杖，等候要求完成，然後寫入至檔案的 JSON 回應。

如果不熟悉环境变量，或者首选在测试时将订阅密钥硬编码为字符串，请将 `process.env.SPEECH_SERVICE_KEY` 替换为字符串形式的订阅密钥。

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>執行範例應用程式

就這樣，您準備要執行範例應用程式。 從命令列 (或終端機工作階段)，請瀏覽至您的專案目錄，然後執行：

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>清除資源

請務必從您的範例應用程式來源程式碼中移除任何機密資訊，例如訂用帳戶金鑰。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索 GitHub 上的 Node.js 範例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS) \(英文\)

## <a name="see-also"></a>請參閱

* [文字轉換語音 API 參考](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [建立自訂語音調](how-to-customize-voice-font.md)
* [錄製語音樣本來建立自訂語音](record-custom-voice-samples.md)
