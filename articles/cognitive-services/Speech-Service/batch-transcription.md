---
title: 如何使用批次轉譯 - 語音服務
titlesuffix: Azure Cognitive Services
description: 如果您想要轉譯儲存體 (例如 Azure Blob) 中數量龐大的音訊，則適用批次轉譯。 透過使用該專屬 REST API，您可以使用共用存取簽章 (SAS) URI 來指向音訊檔案，並以非同步方式接收轉譯。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 0e03c388dac4a70fc45150287154406551ac2672
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867115"
---
# <a name="why-use-batch-transcription"></a>為何使用 Batch 轉譯？

如果您想要轉譯儲存體 (例如 Azure Blob) 中數量龐大的音訊，則適用批次轉譯。 透過使用該專屬 REST API，您可以使用共用存取簽章 (SAS) URI 來指向音訊檔案，並以非同步方式接收轉譯。

>[!NOTE]
> 語音服務的標準訂用帳戶 (S0) 才能使用批次轉譯。 免費訂用帳戶金鑰 (F0) 將無法運作。 如需詳細資訊，請參閱[定價和限制](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/speech-services/)。

## <a name="the-batch-transcription-api"></a>Batch 轉譯 API

Batch 轉譯 API 提供非同步語音轉換文字轉譯及其他功能。 此 REST API 所公開的方法可以：

1. 建立批次的處理要求
1. 查詢狀態
1. 下載轉譯

> [!NOTE]
> Batch 轉譯 API 很適合話務中心，它們通常會累積數千小時的音訊。 此 API 遵循「自主導引」哲學，可讓您輕鬆轉譯大量錄製的音訊。

### <a name="supported-formats"></a>支援的格式

Batch 轉譯 API 支援下列格式：

| 格式 | 轉碼器 | Bitrate | 採樣速率 |
|--------|-------|---------|-------------|
| WAV | PCM | 16 位元 | 8 或 16 kHz、單聲道、立體聲 |
| MP3 | PCM | 16 位元 | 8 或 16 kHz、單聲道、立體聲 |
| OGG | OPUS | 16 位元 | 8 或 16 kHz、單聲道、立體聲 |

> [!NOTE]
> Batch 轉換 API 需要 S0 金鑰 (付費層)。 它無法搭配免費 (f0) 金鑰使用。

針對立體聲音訊資料流，Batch 轉譯 API 會在轉譯期間分離左右聲道。 這會建立兩個 JSON 結果檔案，每個聲道各建立一個。 每個語句的時間戳記可讓開發人員建立排序的最終文字記錄。 下列 JSON 顯示範例要求，包括設定不雅內容過濾、標點符號模型和字組等級時間戳記的屬性

```json
{
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "models": [],
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "description": "An optional description of the transcription.",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True"
  },
```

> [!NOTE]
> Batch 轉譯 API 使用 REST 服務來要求轉譯、其狀態及相關結果。 您可以使用任何語言的 API。 下一節描述 API 的使用方式。

### <a name="query-parameters"></a>查詢參數

REST 要求的查詢字串中可能包括這些參數。

| 參數 | 說明 | 必要/選用 |
|-----------|-------------|---------------------|
| `ProfanityFilterMode` | 指定如何處理辨識結果中的不雅內容。 接受的值為 `none` (會停用不雅內容過濾)、`masked` (為以星號取代不雅內容)、`removed` (會移除結果中的所有不雅內容) 或 `tags` (會新增「不雅內容」標記)。 預設設定為 `masked`。 | 選用 |
| `PunctuationMode` | 指定如何處理辨識結果中的標點符號。 接受的值為`none` (會停用標點符號)、`dictated` (暗示明確的標點符號)、`automatic` (會讓解碼器處理標點符號) 或 `dictatedandautomatic` (暗示口述的標點符號或自動)。 | 選用 |


## <a name="authorization-token"></a>授權權杖

如同語音服務的所有功能，您可以依照我們的[快速入門指南](get-started.md)從 [Azure 入口網站](https://portal.azure.com)建立訂用帳戶金鑰。 如果您打算從我們的基準模型取得轉譯，您只要建立金鑰即可。

如果您打算自訂並使用自訂模型，請執行下列步驟，以將訂用帳戶金鑰新增到自訂語音入口網站：

1. 登入[自訂語音](https://customspeech.ai)。

2. 在右上方，選取 [訂用帳戶]。

3. 選取 [連線到現有的訂用帳戶]。

4. 在快顯視窗中，新增訂用帳戶金鑰和別名。

    ![[新增訂用帳戶] 視窗](media/stt/Subscriptions.jpg)

5. 複製該金鑰並貼到下列範例的用戶端程式碼中。

> [!NOTE]
> 如果您打算使用自訂模型，您也需要該模型的識別碼。 請注意，此識別碼不是您在 [端點詳細資料] 檢視中所找到的端點識別碼。 這是您選取該模型的詳細資料時可擷取的模型識別碼。

## <a name="sample-code"></a>範例程式碼

使用訂用帳戶金鑰和 API 金鑰來自訂下列範例程式碼。 此動作可讓您取得持有人權杖。

```cs
     public static CrisClient CreateApiV2Client(string key, string hostName, int port)

        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

            return new CrisClient(client);
        }
```

取得權杖之後，指定指向需要轉譯之音訊檔案的 SAS URI。 其餘的程式碼會逐一查看狀態，並顯示結果。 首先，您設定要使用的金鑰、區域、模型和 SA，如下列程式碼片段所示。 接下來，您將用戶端與 POST 要求具現化。

```cs
            private const string SubscriptionKey = "<your Speech subscription key>";
            private const string HostName = "westus.cris.ai";
            private const int Port = 443;

            // SAS URI
            private const string RecordingsBlobUri = "SAS URI pointing to the file in Azure Blob Storage";

            // adapted model Ids
            private static Guid AdaptedAcousticId = new Guid("guid of the acoustic adaptation model");
            private static Guid AdaptedLanguageId = new Guid("guid of the language model");

            // Creating a Batch Transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);

            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);
```

您現在已經提出要求，您可以查詢並下載轉譯結果，如下列程式碼片段所示：

```cs

            // get all transcriptions for the user
            transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);

            // for each transcription in the list we check the status
            foreach (var transcription in transcriptions)
            {
                switch(transcription.Status)
                {
                    case "Failed":
                    case "Succeeded":

                            // we check to see if it was one of the transcriptions we created from this client.
                        if (!createdTranscriptions.Contains(transcription.Id))
                        {
                            // not created from here, continue
                            continue;
                        }

                        completed++;

                        // if the transcription was successful, check the results
                        if (transcription.Status == "Succeeded")
                        {
                            var resultsUri = transcription.ResultsUrls["channel_0"];
                            WebClient webClient = new WebClient();
                            var filename = Path.GetTempFileName();
                            webClient.DownloadFile(resultsUri, filename);
                            var results = File.ReadAllText(filename);
                            Console.WriteLine("Transcription succeeded. Results: ");
                            Console.WriteLine(results);
                        }

                    break;
                    case "Running":
                    running++;
                     break;
                    case "NotStarted":
                    notStarted++;
                    break;

                    }
                }
            }
        }
```

如需上述呼叫的完整詳細資訊，請參閱我們的 [swagger 文件](https://westus.cris.ai/swagger/ui/index) \(英文\)。 如需此處所顯示範例的完整檔案，請移至 [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI) \(英文\)。

> [!NOTE]
> 在上述程式碼中，訂用帳戶金鑰來自您在 Azure 入口網站中建立的語音資源。 您從自訂語音服務資源取得的金鑰無法運作。

請留意張貼音訊和接收轉譯狀態的非同步設定。 您建立的用戶端是 .NET HTTP 用戶端。 `PostTranscriptions` 方法可傳送音訊檔案詳細資料，而 `GetTranscriptions` 方法可接收結果。 `PostTranscriptions` 會傳回控制代碼，然後 `GetTranscriptions` 使用它來取得轉譯狀態。

目前的範例程式碼沒有指定自訂模型。 該服務會使用基準模型來轉譯一或多個檔案。 若要指定模型，您可以傳遞相同的方法，如同原音和語言模型的模型識別碼。

如果您不想要使用基準，則必須傳遞原音和語言模型的模型識別碼。

> [!NOTE]
> 針對基準轉譯，您不需要宣告基準模型的端點。 如果您想要使用自訂模型，就必須提供其端點識別碼作為[範例](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)。 如果您想要搭配使用原音基準和基準語言模型，您只需宣告自訂模型的端點識別碼。 Microsoft 會偵測協力廠商基準模型&mdash;原音或語言&mdash;並使用它來完成轉譯要求。

### <a name="supported-storage"></a>支援的儲存體

儲存體目前僅支援 Azure Blob 儲存體。

## <a name="download-the-sample"></a>下載範例

您可以在 [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI) \(英文\) 找到這篇文章中的範例。

> [!NOTE]
> 我們不提供批次音訊轉譯的時間 SLA。 不過一般來說，轉譯作業進行時 (在執行中狀態)，處理速度會比即時快。

## <a name="next-steps"></a>後續步驟

* [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
