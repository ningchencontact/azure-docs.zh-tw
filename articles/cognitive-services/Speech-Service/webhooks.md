---
title: Webhook-語音服務
titleSuffix: Azure Cognitive Services
description: Webhook 是 HTTP 呼叫在處理長時間執行的進程 (例如匯入、調整、精確度測試或轉譯長時間執行的檔案) 時, 適合用來優化您的解決方案。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 3d07e540bf88c956f61b5d3b2a98702cad616985
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558809"
---
# <a name="webhooks-for-speech-services"></a>適用于語音服務的 webhook

Webhook 就像 HTTP 回呼, 可讓您的應用程式在語音服務可供使用時接受其資料。 使用 webhook, 您可以藉由排除持續輪詢回應的需求, 將 REST Api 的使用優化。 在接下來的幾節中, 您將瞭解如何搭配使用 webhook 與語音服務。

## <a name="supported-operations"></a>支援的作業

語音服務支援 webhook 所有長時間執行的作業。 下面列出的每項作業都可以在完成時觸發 HTTP 回呼。

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

接下來, 讓我們建立 webhook。

## <a name="create-a-webhook"></a>建立 Webhook

讓我們建立一個用於離線轉譯的 webhook。 案例: 使用者有一個長時間執行的音訊檔案, 他們想要使用批次轉譯 API 以非同步方式轉譯。

藉由對 HTTPs://\<區域\>cris.ai/api/speechtotext/v2.1/transcriptions/hooks 提出 POST 要求, 即可建立 webhook。

要求的設定參數會以 JSON 格式提供:

```json
{
  "configuration": {
    "url": "https://your.callback.url/goes/here",
    "secret": "<my_secret>"
  },
  "events": [
    "TranscriptionCompletion"
  ],
  "active": true,
  "name": "TranscriptionCompletionWebHook",
  "description": "This is a Webhook created to trigger an HTTP POST request when my audio file transcription is completed.",
  "properties": {
      "Active" : "True"
  }

}
```
批次轉譯 API 的所有 POST 要求都需要`name`。 `description` 和`properties`參數是選擇性的。

`Active`屬性可用來將回呼切換回您的 URL, 而不需要刪除和重新建立 webhook 註冊。 如果您只需要在程式完成後回呼一次, 則請刪除 webhook, 並將`Active`屬性切換為 false。

事件種類`TranscriptionCompletion`是在事件陣列中提供。 當轉譯進入終端機狀態 (`Succeeded`或`Failed`) 時, 它會回呼至您的端點。 回呼至註冊的 URL 時, 要求會包含`X-MicrosoftSpeechServices-Event`一個標頭, 其中包含其中一個已註冊的事件種類。 每個已註冊的事件種類都有一個要求。

您無法訂閱一種事件種類。 這是`Ping`事件種類。 使用 [ping URL] 時, 當您完成建立 webhook 時, 會將具有此類型的要求傳送至 URL (請參閱下文)。  

在設定中, `url`需要屬性。 POST 要求會傳送至此 URL。 `secret`是用來建立裝載的 SHA256 雜湊, 並以秘密作為 HMAC 金鑰。 當回呼至已註冊的`X-MicrosoftSpeechServices-Signature` URL 時, 會將雜湊設定為標頭。 此標頭是以 Base64 編碼。

這個範例說明如何使用C#來驗證承載:

```csharp

private const string EventTypeHeaderName = "X-MicrosoftSpeechServices-Event";
private const string SignatureHeaderName = "X-MicrosoftSpeechServices-Signature";

[HttpPost]
public async Task<IActionResult> PostAsync([FromHeader(Name = EventTypeHeaderName)]WebHookEventType eventTypeHeader, [FromHeader(Name = SignatureHeaderName)]string signature)
{
    string body = string.Empty;
    using (var streamReader = new StreamReader(this.Request.Body))
    {
        body = await streamReader.ReadToEndAsync().ConfigureAwait(false);
        var secretBytes = Encoding.UTF8.GetBytes("my_secret");
        using (var hmacsha256 = new HMACSHA256(secretBytes))
        {
            var contentBytes = Encoding.UTF8.GetBytes(body);
            var contentHash = hmacsha256.ComputeHash(contentBytes);
            var storedHash = Convert.FromBase64String(signature);
            var validated = contentHash.SequenceEqual(storedHash);
        }
    }

    switch (eventTypeHeader)
    {
        case WebHookEventType.Ping:
            // Do your ping event related stuff here (or ignore this event)
            break;
        case WebHookEventType.TranscriptionCompletion:
            // Do your subscription related stuff here.
            break;
        default:
            break;
    }

    return this.Ok();
}

```
在此程式碼片段中, `secret`會解碼並驗證。 您也會注意到 webhook 事件種類已經切換。 每個完成的轉譯目前都有一個事件。 此程式碼會針對每個事件重試五次 (以一秒延遲), 然後放棄。

### <a name="other-webhook-operations"></a>其他 webhook 作業

若要取得所有已註冊的 webhook:GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

若要取得一個特定的 webhook:GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

若要移除一個特定的 webhook:DELETE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note]
> 在上述範例中, 區域是 ' westus '。 這應該取代為您在 Azure 入口網站中建立語音服務資源的區域。

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping 主體: 空白

將 POST 要求傳送至已註冊的 URL。 要求包含`X-MicrosoftSpeechServices-Event`具有值 ping 的標頭。 如果已使用秘密註冊 webhook, 它會包含`X-MicrosoftSpeechServices-Signature`標頭, 其中含有具有密碼的 SHA256 雜湊做為 HMAC 金鑰。 雜湊是以 Base64 編碼。

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test 主體: 空白

如果訂閱事件種類 (轉譯) 的實體出現在系統中, 而且處於適當的狀態, 則將 POST 要求傳送至註冊的 URL。 裝載將會從最後一個實體產生, 而該實體會叫用 web 攔截。 如果沒有實體存在, POST 會以204回應。 如果可以進行測試要求, 則會回應200。 要求本文的形式與 web 攔截所訂閱之特定實體的 GET 要求中相同 (例如轉譯)。 要求將具有`X-MicrosoftSpeechServices-Event`和`X-MicrosoftSpeechServices-Signature`標頭, 如之前所述。

### <a name="run-a-test"></a>執行測試

您可以使用網站 https://bin.webhookrelay.com 來進行快速測試。 您可以從該處取得回呼 Url, 以將做為參數傳遞至 HTTP POST, 以建立稍早在檔中所述的 webhook。

按一下 [建立 Bucket], 並遵循畫面上的指示來取得勾點。 然後使用此頁面中提供的資訊, 向語音服務註冊勾點。 轉送訊息的裝載–為了回應轉譯的完成, 如下所示:

```json
{
    "results": [],
    "recordingsUrls": [
        "my recording URL"
    ],
    "models": [
        {
            "modelKind": "AcousticAndLanguage",
            "datasets": [],
            "id": "a09c8c8b-1090-443c-895c-3b1cf442dec4",
            "createdDateTime": "2019-03-26T12:48:46Z",
            "lastActionDateTime": "2019-03-26T14:04:47Z",
            "status": "Succeeded",
            "locale": "en-US",
            "name": "v4.13 Unified",
            "description": "Unified",
            "properties": {
                "Purpose": "OnlineTranscription,BatchTranscription,LanguageAdaptation",
                "ModelClass": "unified-v4"
            }
        }
    ],
    "statusMessage": "None.",
    "id": "d41615e1-a60e-444b-b063-129649810b3a",
    "createdDateTime": "2019-04-16T09:35:51Z",
    "lastActionDateTime": "2019-04-16T09:38:09Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Simple transcription",
    "description": "Simple transcription description",
    "properties": {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True",
        "AddSentiment": "True",
        "Duration": "00:00:02"
    }
}
```
訊息包含記錄 URL 和用來轉譯該記錄的模型。

## <a name="next-steps"></a>後續步驟

* [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
