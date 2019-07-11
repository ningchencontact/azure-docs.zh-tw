---
title: Webhook-語音服務
titlesuffix: Azure Cognitive Services
description: Webhook 是 HTTP 回撥適合用來最佳化您的解決方案，在處理長時間執行處理程序，例如匯入、 調整、 正確性測試或轉譯的長時間執行的檔案。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: a100049ddfc9d4859e303546c1b10e814cf96ebb
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606213"
---
# <a name="webhooks-for-speech-services"></a>語音服務的 Webhook

Webhook 就像是可讓您的應用程式接受從語音服務的資料，可供使用時的 HTTP 回呼。 使用 webhook，您可以藉由消除不必持續輪詢回應最佳化我們的 REST Api 的使用。 在下一步 的幾節中，您將了解如何使用 webhook 與語音服務。

## <a name="supported-operations"></a>支援的作業

語音服務會支援 webhook，所有長時間執行的作業。 每個下面所列的作業可以觸發程序完成時的 HTTP 回撥。

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

接下來，讓我們建立 webhook。

## <a name="create-a-webhook"></a>建立 Webhook

讓我們建立離線的轉譯的 webhook。 案例： 使用者已是他們想要以非同步方式轉譯，使用 Batch 轉譯 API 的長時間執行的音訊檔案。

您可以建立 Webhook POST 要求至 https://\<地區\>.cris.ai/api/speechtotext/v2.1/transcriptions/hooks。

要求的組態參數提供為 JSON:

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
Batch 轉譯 api 的所有 POST 要求都需要`name`。 `description`和`properties`參數是選擇性的。

`Active`屬性用來切換回呼至您的 URL 開啟和關閉而不需要刪除和重新建立 webhook 註冊。 如果您只需要上一步一次之後呼叫程序已完成，然後刪除 webhook 和交換器`Active`屬性設定為 false。

事件類型`TranscriptionCompletion`所提供的事件陣列。 它將會回呼到您的端點時轉譯進入終止狀態 (`Succeeded`或`Failed`)。 當回呼的註冊 URL，就會包含要求`X-MicrosoftSpeechServices-Event`標頭包含其中一個已註冊的事件類型。 沒有每個已註冊的事件類型的一項要求。

還有一種事件類型，您無法訂閱。 它是`Ping`事件類型。 這種類型的要求會傳送至完成使用 ping URL （如下所示） 時建立 webhook 的 URL。  

在組態中，`url`屬性是必要項。 POST 要求傳送到此 URL。 `secret`用來建立 SHA256 雜湊的承載，以做為 HMAC 金鑰密碼。 雜湊會設定為`X-MicrosoftSpeechServices-Signature`標頭時呼叫的已註冊的 URL。 此標頭是 Base64 編碼。

此範例說明如何裝載使用驗證C#:

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
在此程式碼片段中，`secret`會解碼和驗證。 您也會發現已切換 webhook 事件類型。 目前沒有已完成轉譯每一個事件。 程式碼會重試每個事件 （一秒的延遲） 的五次後才放棄。

### <a name="other-webhook-operations"></a>其他 webhook 作業

若要取得所有已註冊的 webhook︰GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

若要取得一個特定的 webhook︰GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

若要移除一個特定的 webhook:DELETE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note]
> 在上述範例中，區域會是 'westus'。 應該取代您已在其中建立在 Azure 入口網站中您的語音服務資源的區域。

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping 主體： 空白

將 POST 要求傳送到已註冊的 URL。 此要求包含`X-MicrosoftSpeechServices-Event`標頭值的 ping。 如果祕密已註冊 webhook，它會包含`X-MicrosoftSpeechServices-Signature`標頭與裝載使用 HMAC 金鑰與祕密的 SHA256 雜湊。 雜湊是 Base64 編碼。

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test 主體： 空白

如果訂閱的事件類型 （轉譯） 的實體存在系統中，並且處於適當狀態，請傳送 POST 要求到已註冊的 URL。 從最後一個實體，會叫用 web 勾點，就會產生承載。 如果實體不存在，會回應 POST 204。 如果可以進行測試要求，它會回應 200。 要求主體是圖形的在相同與特定實體的 GET 要求的 web 勾點訂閱 （例如轉譯）。 要求將會有`X-MicrosoftSpeechServices-Event`和`X-MicrosoftSpeechServices-Signature`之前所述的標頭。

### <a name="run-a-test"></a>執行測試

可以進行快速測試使用網站 https://bin.webhookrelay.com 。 您可以從該處取得呼叫後要當做參數傳遞至 HTTP POST 來建立文件稍早所述的 webhook Url。

按一下 建立貯體 ' 並遵循螢幕上指示取得攔截程序。 要向語音服務勾點，然後使用此頁面中提供的資訊。 – 以回應轉譯完成 – 轉送訊息的裝載如下所示：

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
此訊息包含錄製 URL 及用來轉譯該記錄的模型。

## <a name="next-steps"></a>後續步驟

* [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
