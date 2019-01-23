---
title: 語音服務 REST API - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音轉換文字和文字轉換語音的 REST API。 在本文中，您會了解到授權選項、查詢選項，以及如何建構要求與接收回應。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: b7f5d4683f0042b95399b86cd4f53c93518c3c56
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330665"
---
# <a name="speech-service-rest-apis"></a>語音服務 REST API

語音服務可做為[語音 SDK](speech-sdk.md) 的替代方案，能使用一組 REST API 將語音轉換成文字，並將文字轉換成語音。 每個可存取的端點皆與區域相關聯。 您的應用程式需要您打算使用之端點的訂用帳戶金鑰。

在使用 REST API 之前，需先了解以下事項：
* 使用 REST API 的語音轉換文字要求只能包含 10 秒的錄製音訊。
* 語音轉文字 REST API 只會傳回最終結果， 不提供部分的結果。
* 文字轉語音 REST API 需要授權標頭。 這表示需要完成權杖交換，才能存取服務。 如需詳細資訊，請參閱[驗證](#authentication)。

## <a name="authentication"></a>驗證

每個語音轉文字或文字轉語音的 REST API 要求都需要授權標頭。 下表會列出各項服務支援的標頭：

| 支援的授權標頭 | 語音轉文字 | 文字轉換語音 |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | 是 | 否 |
| 授權：Bearer | 是 | 是 |

當使用 `Ocp-Apim-Subscription-Key` 標頭，只需要提供您的訂用帳戶金鑰。 例如︰

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

使用 `Authorization: Bearer` 標頭時，需要對 `issueToken` 端點提出要求。 在此要求中，要以訂用帳戶金鑰交換有效期間 10 分鐘的存取權杖。 在下幾節中，可了解到如何取得權杖、使用權杖，及重新整理權杖。

### <a name="how-to-get-an-access-token"></a>如何取得存取權杖

若要取得存取權杖，您必須使用 `Ocp-Apim-Subscription-Key` 和您的訂用帳戶金鑰，對 `issueToken` 端點提出要求。

以下為支援的區域與端點：

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

使用以下範例建立您的存取權杖要求。

#### <a name="http-sample"></a>HTTP 範例

這個範例是可取得權杖的簡單 HTTP 要求。 使用您的語音服務訂用帳戶金鑰來取代 `YOUR_SUBSCRIPTION_KEY`。 如果您的訂用帳戶不在美國西部區域，請以您區域的主機名稱取代 `Host`。

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

回應的本文包含採用 JAVA Web 權杖 (JWT) 格式的存取權杖。

#### <a name="powershell-sample"></a>PowerShell 範例

這個範例是簡單的 PowerShell 指令碼，用來取得存取權杖。 使用您的語音服務訂用帳戶金鑰來取代 `YOUR_SUBSCRIPTION_KEY`。 請務必使用符合您訂用帳戶區域的正確端點。 此範例目前設為「美國西部」。

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>cURL 範例

cURL 是 Linux (以及適用於 Linux 的 Windows 子系統) 中可用的命令列工具。 此 cURL 命令說明如何取得存取權杖。 使用您的語音服務訂用帳戶金鑰來取代 `YOUR_SUBSCRIPTION_KEY`。 請務必使用符合您訂用帳戶區域的正確端點。 此範例目前設為「美國西部」。

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C# 範例

此 C# 類別說明了如何取得存取權杖。 在您具現化類別時，請傳遞您的語音服務訂用帳戶金鑰。 如果您的訂用帳戶不在美國西部區域，請變更 `FetchTokenUri` 的值以符合您訂用帳戶的區域。

```cs
/*
    * This class demonstrates how to get a valid access token.
    */
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="how-to-use-an-access-token"></a>如何使用存取權杖

存取權杖應傳送到服務作為 `Authorization: Bearer <TOKEN>` 標頭。 每一個存取權杖的有效時間為 10 分鐘。 您可以隨時取得新權杖，但為了盡量降低網路流量和延遲，建議您使用相同的權杖九分鐘。

以下是文字轉語音 REST API 的範例 HTTP 要求：

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.tts.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Hello, world!
</voice></speak>
```

### <a name="how-to-renew-an-access-token-using-c"></a>如何使用 C# 更新存取權杖

此 C# 程式碼是之前所提及類別的簡易取代項目。 `Authentication` 類別會使用計時器，每隔九分鐘自動取得新的存取權杖。 這個方法可確保您的程式執行時，一律可以使用有效的權杖。

> [!NOTE]
> 您可以儲存取得最後一個權杖時的時間戳記，而不是使用計時器。 而後您只能在時間戳記即將過期時要求新的時間戳記。 這種方法可避免不必要地要求新的權杖，並且可能比較適合於不頻繁提出語音要求的程式。

如往常一般，請確定 `FetchTokenUri` 值符合您的訂用帳戶區域。 在您具現化類別時，請傳遞您的訂用帳戶金鑰。

```cs
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;
    private Timer accessTokenRenewer;

    //Access token expires every 10 minutes. Renew it every 9 minutes.
    private const int RefreshTokenDuration = 9;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

        // renew the token on set duration.
        accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                        this,
                                        TimeSpan.FromMinutes(RefreshTokenDuration),
                                        TimeSpan.FromMilliseconds(-1));
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private void RenewAccessToken()
    {
        this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
        Console.WriteLine("Renewed token.");
    }

    private void OnTokenExpiredCallback(object stateInfo)
    {
        try
        {
            RenewAccessToken();
        }
        catch (Exception ex)
        {
            Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
        }
        finally
        {
            try
            {
                accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
            }
        }
    }

    private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

## <a name="speech-to-text-api"></a>語音轉文字 API

語音轉文字 REST API 只支援簡短語句。 要求可包含多達 10 秒的音訊，總長最多可達 14 秒。 REST API 只會傳回最終結果，而不是部分或中間結果。

如果您的應用程式需要傳送更長的音訊，請考慮使用[語音 SDK](speech-sdk.md) 或[批次轉譯](batch-transcription.md)。

### <a name="regions-and-endpoints"></a>區域與端點

支援使用 REST API 對以下區域進行語音轉文字的文字轉錄。 請確定選取的是符合您訂用帳戶區域的端點。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="query-parameters"></a>查詢參數

REST 要求的查詢字串中可能包括這些參數。

| 參數 | 說明 | 必要/選用 |
|-----------|-------------|---------------------|
| `language` | 識別正在辨識的口說語言。 請參閱[支援的語言](language-support.md#speech-to-text)。 | 必要 |
| `format` | 指定結果格式。 接受的值為 `simple` 和 `detailed`。 簡單的結果包含 `RecognitionStatus`、`DisplayText`、`Offset` 和 `Duration`。 詳細的回應包含多個具有信賴值的結果和四個不同的表示法。 預設設定為 `simple`。 | 選用 |
| `profanity` | 指定如何處理辨識結果中的不雅內容。 接受的值為 `masked` (以星號取代不雅內容)、`removed` (移除結果中所有的不雅內容)，或 `raw` (結果中包含不雅內容)。 預設設定為 `masked`。 | 選用 |

### <a name="request-headers"></a>要求標頭

下表列出了語音轉文字要求的必要標頭和選用標頭。

|頁首| 說明 | 必要/選用 |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | 您的語音服務訂用帳戶金鑰。 | 必須有此標頭或 `Authorization`。 |
| `Authorization` | 前面加入 `Bearer` 這個字的授權權杖。 如需詳細資訊，請參閱[驗證](#authentication)。 | 必須有此標頭或 `Ocp-Apim-Subscription-Key`。 |
| `Content-type` | 描述所提供音訊資料的格式和轉碼器。 接受的值為 `audio/wav; codecs=audio/pcm; samplerate=16000` 和 `audio/ogg; codecs=opus`。 | 必要 |
| `Transfer-Encoding` | 指定正在傳送的音訊資料區塊，而不是單一檔案。 只有在以區塊處理音訊資料時，才能使用此標頭。 | 選用 |
| `Expect` | 如果使用區塊傳輸，請傳送 `Expect: 100-continue`。 語音服務會確認初始要求並等候其他資料。| 如果傳送的是音訊資料區塊，則為必要。 |
| `Accept` | 如果提供，則必須是 `application/json`。 語音服務提供 JSON 格式的結果。 如果您未指定預設值，則有些 Web 要求架構會提供不相容的預設值，因此一律包含 `Accept` 是很好的做法。 | 此為選用步驟，但建議執行。 |

### <a name="audio-formats"></a>自動格式

音訊是在 HTTP `POST` 要求的主體中傳送。 它必須是此表格中的格式之一：

| 格式 | 轉碼器 | Bitrate | 採樣速率 |
|--------|-------|---------|-------------|
| WAV | PCM | 16 位元 | 16 kHz，單聲道 |
| OGG | OPUS | 16 位元 | 16 kHz，單聲道 |

>[!NOTE]
>透過「語音服務」中的 REST API 和 WebSocket 可支援上述格式。 [語音 SDK](speech-sdk.md) 目前僅支援具備 PCM 轉碼器的 WAV 格式。

### <a name="sample-request"></a>範例要求

這是一般的 HTTP 要求。 下列範例包含主機名稱和必要標頭。 請務必注意，此服務也預期會有此範例中未包含的音訊資料。 如先前所述，建議以區塊處理，但非必要。

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status-codes"></a>HTTP 狀態碼

每個回應的 HTTP 狀態碼會指出成功或常見的錯誤。

| HTTP 狀態碼 | 說明 | 可能的原因 |
|------------------|-------------|-----------------|
| 100 | Continue | 已接受初始要求。 繼續傳送其餘的資料。 (搭配區塊傳輸使用。) |
| 200 | OK | 要求成功；回應主體是 JSON 物件。 |
| 400 | 不正確的要求 | 未提供語言代碼，或不是支援的語言；無效的音訊檔案。 |
| 401 | 未經授權 | 訂用帳戶金鑰或授權權杖在指定的區域中無效，或是無效的端點。 |
| 403 | 禁止 | 遺漏訂用帳戶金鑰或授權權杖。 |

### <a name="chunked-transfer"></a>區塊傳輸

區塊傳輸 (`Transfer-Encoding: chunked`) 可協助減少辨識延遲，因為它可讓語音服務在音訊檔案進行傳輸時開始處理。 REST API 不會提供部分或中間的結果。 此選項僅僅為了改善回應能力。

此程式碼範例說明如何以區塊傳送音訊。 只有第一個區塊應該包含音訊檔案的標頭。 `request` 是連線到適當 REST 端點的 HTTPWebRequest 物件。 `audioFile` 是音訊檔案在磁碟上的路徑。

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

### <a name="response-parameters"></a>回應參數

結果以 JSON 格式提供。 `simple` 格式包含以下的最上層欄位。

| 參數 | 說明  |
|-----------|--------------|
|`RecognitionStatus`|狀態，例如 `Success` 代表辨識成功。 請參閱下一個表格。|
|`DisplayText`|在大寫字、標點符號、反向文字正規化 (將語音文字轉換為較短的形式，例如 200 表示 "two hundred" 或將 "Dr.Smith" 表示 "doctor smith")，以及不雅內容遮罩之後辨識的文字。 只會在成功時呈現。|
|`Offset`|辨識的語音在音訊資料流中開始的時間 (以 100 奈秒為單位)。|
|`Duration`|辨識的語音在音訊資料流中的持續時間 (以 100 奈秒為單位)。|

`RecognitionStatus` 欄位可包含下列的值：

| 狀態 | 說明 |
|--------|-------------|
| `Success` | 辨識成功並顯示 `DisplayText` 欄位。 |
| `NoMatch` | 音訊串流中偵測到語音，但目標語言中沒有符合的字組。 通常表示辨識語言與使用者的口語語言不同。 |
| `InitialSilenceTimeout` | 音訊串流的開頭沒有任何聲音，而且等候語音的服務已逾時。 |
| `BabbleTimeout` | 音訊串流的開頭只有雜音，而且等候語音的服務已逾時。 |
| `Error` | 辨識服務發生內部錯誤，無法繼續。 可能的話，再試一次。 |

> [!NOTE]
> 如果音訊只包含不雅內容，而且 `profanity` 查詢參數設為 `remove`，則服務不會傳回語音結果。

`detailed` 格式包含與 `simple` 格式相同的資料，並包含 `NBest`，這是相同語音辨識結果的替代解譯。 這些結果是從最可能排列到最不可能。第一個項目與主要辨識結果相同。  使用 `detailed` 格式時，系統會提供 `DisplayText` 作為 `NBest` 清單中每個結果的 `Display`。

`NBest` 清單中的每個物件包括：

| 參數 | 說明 |
|-----------|-------------|
| `Confidence` | 項目的信賴分數從 0.0 (不信賴) 到 1.0 (完全信賴) |
| `Lexical` | 已辨識文字的語彙形式：已辨識的實際文字。 |
| `ITN` | 已辨識文字的反向文字正規化 (「標準」) 形式，包含電話號碼、數字、縮寫 ("doctor smith" 縮短為 "dr smith")，以及其他已套件的轉換。 |
| `MaskedITN` | 如果要求，已套用不雅內容遮罩的 ITN 形式。 |
| `Display` | 已辨識文字的顯示形式，已新增標點符號和大寫。 此參數與當格式設定為 `simple` 時，所提供的 `DisplayText` 相同。 |

### <a name="sample-responses"></a>回應範例

以下是 `simple` 辨識的典型回應。

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

以下是 `detailed` 辨識的典型回應。

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="text-to-speech-api"></a>文字轉語音 API

文字轉語音 API 支援類神經和標準文字轉語音，且各支援依地區設定所識別的特定語言和方言。

* 如語音的完整清單，請參閱[ 語言支援](language-support.md#text-to-speech)。
* 如需區域可用性的詳細資訊，請參閱[區域](regions.md#text-to-speech)。

### <a name="request-headers"></a>要求標頭

下表列出了語音轉文字要求的必要標頭和選用標頭。

| 頁首 | 說明 | 必要/選用 |
|--------|-------------|---------------------|
| `Authorization` | 前面加入 `Bearer` 這個字的授權權杖。 如需詳細資訊，請參閱[驗證](#authentication)。 | 必要 |
| `Content-Type` | 指定所提供文字的內容類型。 接受的值為 `application/ssml+xml`。 | 必要 |
| `X-Microsoft-OutputFormat` | 指定音訊輸出格式。 如需接受值的完整清單，請參閱[音訊輸出](#audio-outputs)。 | 必要 |
| `User-Agent` | 應用程式名稱。 必須少於 255 個字元。 | 必要 |

### <a name="audio-outputs"></a>音訊輸出

此清單列出了每個要求中系統做為 `X-Microsoft-OutputFormat` 標頭的傳送的支援音訊格式。 每個格式皆包含位元速率和編碼類型。 語音服務支援 24 KHz 和 16 KHz 的音訊輸出。

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-mulaw` | `riff-16khz-16bit-mono-pcm` |
| `audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3` |
| `audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm` |
| `riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3` |
| `audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3` |

> [!NOTE]
> 如果您選取的語音和輸出格式具有不同的位元速率，則會視需要重新進行音訊取樣。 不過，24KHz 語音不支援 `audio-16khz-16kbps-mono-siren` 和 `riff-16khz-16kbps-mono-siren` 輸出格式。

### <a name="request-body"></a>Request body

每個 `POST` 要求的本文都會以[語音合成標記語言 (SSML)](speech-synthesis-markup.md) 形式傳送。 SSML 可讓您選擇文字轉換語音服務所傳回合成語音的語音和語言。 如需支援的完整語音清單，請參閱[語言支援](language-support.md#text-to-speech)。

> [!NOTE]
> 如果使用自訂語音，可以純文字 (ASCII 或 UTF-8) 形式傳送要求本文。

### <a name="sample-request"></a>範例要求

此 HTTP 要求使用 SSML 指定語音與語言。 本文不能超過 1,000 個字元。

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>HTTP 狀態碼

每個回應的 HTTP 狀態碼會指出成功或常見的錯誤。

| HTTP 狀態碼 | 說明 | 可能的原因 |
|------------------|-------------|-----------------|
| 200 | OK | 要求成功；回應主體是音訊檔案。 |
| 400 | 不正確的要求 | 必要的參數遺失、為空白或 Null。 或者，傳遞至必要或選用參數的值無效。 常見的問題是標頭太長。 |
| 401 | 未經授權 | 要求未經授權。 請檢查以確定您的訂用帳戶金鑰或權杖有效，並且位於正確的區域。 |
| 413 | 要求實體太大 | SSML 輸入的長度大於 1024 個字元。 |
| 429 | 太多要求 | 您已超出訂用帳戶允許的配額或要求率。 |
| 502 | 錯誤的閘道 | 網路或伺服器端問題。 也可能表示標頭無效。 |

如果 HTTP 狀態為 `200 OK`，則回應主體會包含所要求格式的音訊檔案。 會在將此檔案傳輸、儲存到緩衝區或儲存到檔案時播放。

## <a name="next-steps"></a>後續步驟

- [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
