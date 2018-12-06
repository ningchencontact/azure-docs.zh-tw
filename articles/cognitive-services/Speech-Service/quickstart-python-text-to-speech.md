---
title: 快速入門：將文字轉換為語音，Python - 語音服務
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將了解如何使用 Python 和文字轉語音 REST API 將文字轉換為語音。 本指南中所包含的範例文字已建構為語音合成標記語言 (SSML)。 這可讓您選擇語音回應的語音和語言。 REST API 也支援純文字 (ASCII 或 UTF-8)，不過，如果提供純文字，則會以語音服務的預設語音和語言將回應傳回。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 11/16/2018
ms.author: erhopf
ms.openlocfilehash: 38bcd67dfb6d9b1a1955658872fee7b8b7a14a0c
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2018
ms.locfileid: "52447732"
---
# <a name="quickstart-convert-text-to-speech-using-python"></a>快速入門：使用 Python 將文字轉換為語音

在本快速入門中，您將了解如何使用 Python 和文字轉語音 REST API 將文字轉換為語音。 本指南中的要求本文已結構化為[語音合成標記語言 (SSML)](speech-synthesis-markup.md)，這可讓您選擇回應的語音和語言。

本快速入門需要 [Azure 認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)和語音服務資源。 如果您還沒有帳戶，可以使用[免費試用](get-started.md)來取得訂用帳戶金鑰。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* Python 2.7.x 或 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download)，或您最愛的文字編輯器
* 語音服務的 Azure 訂用帳戶金鑰

## <a name="create-a-project-and-import-required-modules"></a>建立專案，並匯入所需的模組

在您慣用的 IDE 或編輯器建立新的 Python 專案。 然後，將下列程式碼片段複製到您的專案中名為 `tts.py` 的檔案。

```python
import os, requests, time
```

> [!NOTE]
> 如果您未曾使用這些模組，則必須先加以安裝，再執行您的程式。 若要安裝這些套件，請執行：`pip install requests`。

這些模組會用來將語音回應寫入到具有時間戳記的檔案、建構 HTTP 要求，以及呼叫文字轉語音 API。

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>設定訂用帳戶金鑰，並為 TTS 建立提示

在接下來的幾節中，您將建立方法來處理授權、呼叫文字轉語音 API，並驗證回應。 首先讓我們新增一些程式碼，確保此範例可搭配 Python 2.7.x 及 3.x 來運作。

```python
try: input = raw_input
except NameError: pass
```

接下來，讓我們建立類別。 這是我們會放置權杖交換方法的位置，並且會呼叫文字轉語音 API。

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

`subscription_key` 是來自 Azure 入口網站的唯一金鑰。 `tts` 會提示使用者輸入將轉換成語音的文字。 此輸入是字串常值，因此其字元不需要逸出。 最後，`timestr` 會取得目前的時間，我們將用它來命名您的檔案。

## <a name="get-an-access-token"></a>取得存取權杖

文字轉語音 REST API 需要存取權杖來進行驗證。 若要取得存取權杖，則必須進行交換。 此範例會使用 `issueToken` 端點，以語音服務訂用帳戶金鑰交換存取權杖。

這個範例假設您的語音服務訂用帳戶是在美國西部區域。 如果您使用不同的區域，請更新 `fetch_token_url` 的值。 如需完整清單，請參閱[區域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)。

請將此程式碼複製到 `TextToSpeech` 類別：

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> 如需驗證的詳細資訊，請參閱[如何取得存取權杖](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#how-to-get-an-access-token)。

## <a name="make-a-request-and-save-the-response"></a>提出要求並儲存回應

在此，您要建置要求並儲存語音回應。 首先，您必須設定 `base_url` 和 `path`。 此範例假設您會使用美國西部端點。 如果您的資源註冊至不同區域，請務必更新 `base_url`。 如需詳細資訊，請參閱[語音服務區域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)。

接下來，您必須為要求新增必要的標頭。 請務必將 `User-Agent` 更新為您的資源名稱 (位於 Azure 入口網站)，並將 `X-Microsoft-OutputFormat` 設定為您偏好的音訊輸出。 如需輸出格式的完整清單，請參閱[音訊輸出](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-outputs)。

然後，使用語音合成標記語言 (SSML) 建構要求本文。 此範例會定義結構，並使用您稍早建立的 `tts` 輸入。

>[!NOTE]
> 此範例會使用 `ZiraRUS` 語音音調。 如需 Microsoft 提供的完整語音/語言清單，請參閱[語言支援](language-support.md)。
> 如果您想要為品牌建立獨特可辨識的語音，請參閱[建立自訂語音音調](how-to-customize-voice-font.md)。

最後，您會對服務提出要求。 如果要求成功，且傳回狀態碼 200，則語音回應會寫入到有時間戳記的檔案。

請將此程式碼複製到 `TextToSpeech` 類別：

```python
def save_audio(self):
    base_url = 'https://westus.tts.speech.microsoft.com/'
    path = 'cognitiveservices/v1'
    constructed_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token,
        'Content-Type': 'application/ssml+xml',
        'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
        'User-Agent': 'YOUR_RESOURCE_NAME',
        'cache-control': 'no-cache'
    }
    body = "<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>" + self.tts + "</voice></speak>"

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) + "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")

```

## <a name="put-it-all-together"></a>組合在一起

就快要完成了。 最後一個步驟是具現化類別並呼叫函式。

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>執行範例應用程式

就這樣，您已準備好而可執行文字轉語音範例應用程式。 從命令列 (或終端機工作階段)，請瀏覽至您的專案目錄，然後執行：

```console
python tts.py
```

出現提示時，輸入您想要從文字轉換為語音的任何內容。 如果成功，語音檔案會位於專案資料夾中。 使用您最愛的媒體播放器來播放。

## <a name="clean-up-resources"></a>清除資源

請務必從您的範例應用程式來源程式碼中移除任何機密資訊，例如訂用帳戶金鑰。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [文字轉換語音 API 參考](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)

## <a name="see-also"></a>另請參閱

* [建立自訂語音調](how-to-customize-voice-font.md)
* [錄製語音樣本來建立自訂語音](record-custom-voice-samples.md)
