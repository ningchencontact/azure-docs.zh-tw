---
title: 快速入門：將文字轉換為語音，Node.js - 語音服務
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將了解如何使用 Node.js 和文字轉語音 REST API 將文字轉換為語音。 本指南中所包含的範例文字已建構為語音合成標記語言 (SSML)。 這可讓您選擇語音回應的語音和語言。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 808466e9d2546472a4f86dd0d7eacd48e75cb94a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224474"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>快速入門：使用 Node.js 將文字轉換為語音

在本快速入門中，您將了解如何使用 Node.js 和文字轉語音 REST API 將文字轉換為語音。 本指南中的要求本文已結構化為[語音合成標記語言 (SSML)](speech-synthesis-markup.md)，這可讓您選擇回應的語音和語言。

本快速入門需要 [Azure 認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)和語音服務資源。 如果您還沒有帳戶，可以使用[免費試用](get-started.md)來取得訂用帳戶金鑰。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* [Node 8.12.x 或更新版本](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download)，或您最愛的文字編輯器
* 語音服務的 Azure 訂用帳戶金鑰。 [免費取得一個金鑰!](get-started.md)

## <a name="create-a-project-and-require-dependencies"></a>建立專案並要求相依性

使用慣用的 IDE 或編輯器中建立新的 Node.js 專案。 然後，將下列程式碼片段複製到您的專案中名為 `tts.js` 的檔案。

```javascript
// Requires request for HTTP requests
const request = require('request');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> 如果您未曾使用這些模組，則必須先加以安裝，再執行您的程式。 若要安裝這些套件，請執行：`npm install request readline-sync`。

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>設定訂用帳戶金鑰，並為 TTS 建立提示

在接下來的幾節中，您將建立函式來處理授權、呼叫文字轉語音 API，並驗證回應。 首先新增訂用帳戶金鑰以及建立文字輸入的提示。

```javascript
/*
 * These lines will attempt to read your subscription key from an environment
 * variable. If you prefer to hardcode the subscription key for ease of use,
 * replace process.env.SUBSCRIPTION_KEY with your subscription key as a string.  
 */
const subscriptionKey = process.env.SUBSCRIPTION_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};

// Prompts the user to input text.
let text = readline.question('What would you like to convert to speech? ');
```

## <a name="get-an-access-token"></a>取得存取權杖

文字轉語音 REST API 需要存取權杖來進行驗證。 若要取得存取權杖，則必須進行交換。 此範例會使用 `issueToken` 端點，以語音服務訂用帳戶金鑰交換存取權杖。

此函式會採用兩個引數，您的語音服務訂用帳戶金鑰，以及一個回呼函式。 在函式取得存取權杖之後，就會將值傳遞至回呼函式。 在下一節中，我們會建立可呼叫文字轉換語音 API 的函式，並儲存合成的語音回應。

這個範例假設您的語音服務訂用帳戶是在美國西部區域。 如果您使用不同的區域，請更新 `uri` 的值。 如需完整清單，請參閱[區域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)。

請將下列程式碼複製到您的專案中：

```javascript
function textToSpeech(subscriptionKey, saveAudio) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    };
    // This function retrieve the access token and is passed as callback
    // to request below.
    function getToken(error, response, body) {
        console.log("Getting your token...\n")
        if (!error && response.statusCode == 200) {
            //This is the callback to our saveAudio function.
            // It takes a single argument, which is the returned accessToken.
            saveAudio(body)
        }
        else {
          throw new Error(error);
        }
    }
    request(options, getToken)
}
```

> [!NOTE]
> 如需驗證的詳細資訊，請參閱[使用存取權杖進行驗證](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)。

## <a name="make-a-request-and-save-the-response"></a>提出要求並儲存回應

在此，您即將建置對文字轉語音 API 的要求，並儲存語音回應。 此範例假設您會使用美國西部端點。 如果您的資源註冊至不同區域，請務必更新 `uri`。 如需詳細資訊，請參閱[語音服務區域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)。

接下來，您必須為要求新增必要的標頭。 請務必將 `User-Agent` 更新為您的資源名稱 (位於 Azure 入口網站)，並將 `X-Microsoft-OutputFormat` 設定為您偏好的音訊輸出。 如需輸出格式的完整清單，請參閱[音訊輸出](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-outputs)。

然後，使用語音合成標記語言 (SSML) 建構要求本文。 此範例會定義結構，並使用您稍早建立的 `text` 輸入。

>[!NOTE]
> 此範例會使用 `JessaRUS` 語音音調。 如需 Microsoft 提供的完整語音/語言清單，請參閱[語言支援](language-support.md)。
> 如果您想要為品牌建立獨特可辨識的語音，請參閱[建立自訂語音音調](how-to-customize-voice-font.md)。

最後，您會對服務提出要求。 如果要求成功，且傳回狀態碼 200，則語音回應會寫入為 `sample.wav`。

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function saveAudio(accessToken) {
    // Create the SSML request.
    let xml_body = xmlbuilder.create('speak')
      .att('version', '1.0')
      .att('xml:lang', 'en-us')
      .ele('voice')
      .att('xml:lang', 'en-us')
      .att('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
      .txt(text)
      .end();
    // Convert the XML into a string to send in the TTS request.
    let body = xml_body.toString();

    let options = {
        method: 'POST',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/v1',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'cache-control': 'no-cache',
            'User-Agent': 'YOUR_RESOURCE_NAME',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'Content-Type': 'application/ssml+xml'
        },
        body: body
    };
    // This function makes the request to convert speech to text.
    // The speech is returned as the response.
    function convertText(error, response, body){
      if (!error && response.statusCode == 200) {
        console.log("Converting text-to-speech. Please hold...\n")
      }
      else {
        throw new Error(error);
      }
      console.log("Your file is ready.\n")
    }
    // Pipe the response to file.
    request(options, convertText).pipe(fs.createWriteStream('sample.wav'));
}
```

## <a name="put-it-all-together"></a>組合在一起

就快要完成了。 最後一個步驟是呼叫 `textToSpeech` 函式。

```javascript
// Start the sample app.
textToSpeech(subscriptionKey, saveAudio);
```

## <a name="run-the-sample-app"></a>執行範例應用程式

就這樣，您已準備好而可執行文字轉語音範例應用程式。 從命令列 (或終端機工作階段)，請瀏覽至您的專案目錄，然後執行：

```console
node tts.js
```

出現提示時，輸入您想要從文字轉換為語音的任何內容。 如果成功，語音檔案會位於專案資料夾中。 使用您最愛的媒體播放器來播放。

## <a name="clean-up-resources"></a>清除資源

請務必從您的範例應用程式來源程式碼中移除任何機密資訊，例如訂用帳戶金鑰。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索 GitHub 上的 Node.js 範例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS) \(英文\)

## <a name="see-also"></a>另請參閱

* [文字轉換語音 API 參考](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)
* [建立自訂語音調](how-to-customize-voice-font.md)
* [錄製語音樣本來建立自訂語音](record-custom-voice-samples.md)
