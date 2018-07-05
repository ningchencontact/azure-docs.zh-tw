---
title: 語音服務 REST API | Microsoft Docs
description: 語音服務 REST API 的參考。
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/09/2018
ms.author: v-jerkin
ms.openlocfilehash: a25c2b7ea7fdfcc6bcaa10baff3a5ae14ae9753b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37044813"
---
# <a name="speech-service-rest-apis"></a>語音服務 REST API

統一語音服務的 REST API 與[語音 API](https://docs.microsoft.com/azure/cognitive-services/Speech) (之前稱為 Bing 語音服務) 所提供的 API 類似。 而端點不同於先前語音服務所使用的端點。

## <a name="speech-to-text"></a>語音轉換文字

在語音轉換文字 API 中，只有使用的端點與先前語音服務的語音辨識 API 不同。 下表會顯示新的端點。 請使用與訂用帳戶區域相符的端點。

[!include[](includes/endpoints-speech-to-text.md)]

語音轉換文字 API 在其他方面類似於先前語音 API 的 [REST API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest)。

語音轉換文字 REST API 只支援簡短語句。 要求可能包含多達 10 秒的音訊，整體最多可長達 14 秒。 REST API 只會傳回最終結果，而不是部分或中間的結果。

> [!NOTE]
> 如果您自訂原音模型或語言模型或是發音，請改用自訂端點。

## <a name="text-to-speech"></a>文字轉語音

新的文字轉換語音 API 支援 24-KHz 音訊輸出。 `X-Microsoft-OutputFormat` 標頭現在可能包含下列值。

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `audio-16khz-16kbps-mono-siren`
`riff-16khz-16kbps-mono-siren`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

語音服務現在提供兩種 24-KHz 語音：

地區設定 | 語言   | 性別 | 服務名稱對應
-------|------------|--------|------------
zh-TW  | 美式英文 | 女性 | "Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)" 
zh-TW  | 美式英文 | 男性   | "Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)"

以下是統一語音服務之文字轉換語音 API 的 REST 端點。 請使用與訂用帳戶區域相符的端點。

[!include[](includes/endpoints-text-to-speech.md)]

當您參閱先前語音 API 的 [REST API 文件](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/bingvoiceoutput)時，請記住這些差異。

## <a name="authentication"></a>驗證

將要求傳送至語音服務的 REST API 需要存取權杖。 您可以藉由提供訂用帳戶金鑰給區域語音服務的 `issueToken` 端點來取得權杖，如下表所示。 請使用與訂用帳戶區域相符的端點。

[!include[](includes/endpoints-token-service.md)]

每一個存取權杖的有效時間為 10 分鐘。 您可以隨時取得新的權杖—包括，如有需要，可在每個語音 REST API 要求之前取得。 不過，為了盡量降低網路流量和延遲，建議您使用相同的權杖九分鐘。

下列幾節將示範如何取得權杖，以及如何在要求中使用它。

### <a name="getting-a-token-http"></a>取得權杖：HTTP

以下是用於取得權杖的範例 HTTP 要求。 以您的語音服務訂用帳戶金鑰取代 `YOUR_SUBSCRIPTION_KEY`。 如果您的訂用帳戶不是在美國西部區域，請以區域的主機名稱取代 `Host`。

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

此要求的回應本文是使用 Java Web 權杖 (JWT) 格式的存取權杖。

### <a name="getting-a-token-powershell"></a>取得權杖：PowerShell

下列 Windows PowerShell 指令碼說明如何取得存取權杖。 以您的語音服務訂用帳戶金鑰取代 `YOUR_SUBSCRIPTION_KEY`。 如果您的訂用帳戶不是在美國西部區域，請據此變更指定 URI 的主機名稱。

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

### <a name="getting-a-token-curl"></a>取得權杖：cURL

cURL 是 Linux (以及適用於 Linux 的 Windows 子系統) 中可用的命令列工具。 下列 cURL 命令說明如何取得存取權杖。 以您的語音服務訂用帳戶金鑰取代 `YOUR_SUBSCRIPTION_KEY`。 如果您的訂用帳戶不是在美國西部區域，請據此變更指定 URI 的主機名稱。

> [!NOTE]
> 為了方便閱讀，命令會以多行顯示，但應該在殼層提示字元中輸入為一行。

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="getting-a-token-c"></a>取得權杖：C#

下列 C# 類別說明如何取得存取權杖。 在具現化類別時，請傳遞您的語音服務訂用帳戶金鑰。 如果您的訂用帳戶不是在美國西部區域，請據此變更 `FetchTokenUri` 的主機名稱。

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

### <a name="using-a-token"></a>使用權杖

若要在 REST API 要求中使用權杖，請在 `Authorization` 標頭中的 `Bearer` 這個字之後提供它。 例如，以下是包含權杖的範例文字轉換語音 REST 要求。 以實際權杖替代 `YOUR_ACCESS_TOKEN`，並在 `Host` 標頭中使用正確的主機名稱。

```xml
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

### <a name="renewing-authorization"></a>更新授權

授權權杖會在 10 分鐘後到期。 請在權杖到期之前，藉由取得新的權杖來更新您的授權—例如，在九分鐘之後。 

下列 C# 程式碼是之前所提及類別的簡易取代項目。 `Authentication` 類別會使用計時器，每隔九分鐘自動取得新的存取權杖。 這個方法可確保您的程式執行時，一律可以使用有效的權杖。

> [!NOTE]
> 您可以儲存取得目前權杖時的時間戳記，而不使用計時器，然後只有在目前權杖即將到期時，才要求新的權杖。 這種方法可避免不必要地要求新的權杖，並且可能比較適合於不頻繁提出語音要求的程式。

如往常一般，請確定 `FetchTokenUri` 值符合您的訂用帳戶區域。 在具現化類別時，請傳遞您的訂用帳戶金鑰。

```cs
    /*
     * This class demonstrates how to maintain a valid access token.
     */
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

## <a name="next-steps"></a>後續步驟

* [取得語音試用版訂用帳戶](https://azure.microsoft.com/try/cognitive-services/)
* [參閱如何自訂語音模型](how-to-customize-speech-models.md)
