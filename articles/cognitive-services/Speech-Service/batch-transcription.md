---
title: Azure 批次轉譯 API | Azure Microsoft Docs
description: 範例
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: 9dd7479ae95f74123d9b762e42ec95e8dbf25818
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346434"
---
# <a name="batch-transcription"></a>批次轉譯

批次轉譯適用於具有大量音訊的使用案例。 它可讓開發人員以非同步模式指向音訊檔案並取回轉譯。

## <a name="batch-transcription-api"></a>批次轉譯 API

批次轉譯 API 可執行上述案例。 它提供非同步語音轉換文字轉譯及其他功能。

> [!NOTE]
> 批次轉譯 API 適用於通常會累積數千個小時音訊的話務中心。 此 API 的「射後不理」哲學可讓您輕鬆轉譯大量錄製的音訊。

### <a name="supported-formats"></a>支援的格式

批次轉譯 API 旨在成為所有離線話務中心相關案例的實作者，並提供所有相關格式的支援。 目前支援的格式：

Name| 通道  |
----|----------|
mp3 |   Mono   |   
mp3 |  立體聲  | 
wav |   Mono   |
wav |  立體聲  |

針對立體聲音訊資料流，批次轉譯會在轉譯期間分割左右聲道。 這會建立兩個 JSON 結果檔案，每個聲道各建立一個。 每個語句的時間戳記可讓開發人員建立排序的最終文字記錄。 下列 JSON 範例顯示聲道的輸出。

```json
       {
        "recordingsUrl": "https://mystorage.blob.core.windows.net/cris-e2e-datasets/TranscriptionsDataset/small_sentence.wav?st=2018-04-19T15:56:00Z&se=2040-04-21T15:56:00Z&sp=rl&sv=2017-04-17&sr=b&sig=DtvXbMYquDWQ2OkhAenGuyZI%2BYgaa3cyvdQoHKIBGdQ%3D",
        "resultsUrls": {
            "channel_0": "https://mystorage.blob.core.windows.net/bestor-87a0286f-304c-4636-b6bd-b3a96166df28/TranscriptionData/24265e4c-e459-4384-b572-5e3e7795221f?sv=2017-04-17&sr=b&sig=IY2qd%2Fkgtz2PwRe2C88BphH4Hv%2F1VCb1UVJ33xsw%2BEY%3D&se=2018-04-23T14:48:24Z&sp=r"
        },
        "statusMessage": "None.",
        "id": "0bb95356-ff06-469d-acc7-81f9144a269a",
        "createdDateTime": "2018-04-20T14:11:57.167",
        "lastActionDateTime": "2018-04-20T14:12:54.643",
        "status": "Succeeded",
        "locale": "en-US"
    },
```

> [!NOTE]
> 批次轉譯 API 使用 REST 服務來要求轉譯、其狀態及相關結果。 任何語言都可以使用 API。 下一節描述其使用方式。

## <a name="authorization-token"></a>授權權杖

如同統一語音服務的所有功能，使用者需要從 [Azure 入口網站](https://portal.azure.com)建立訂用帳戶金鑰。 此外，必須從語音入口網站取得 API 金鑰。 產生 API 金鑰的步驟：

1. 登入 https://customspeech.ai。

2. 按一下 [訂用帳戶]。

3. 按一下選項 `Generate API Key`。

    ![上傳檢視](media/stt/Subscriptions.jpg)

4. 複製該金鑰並貼到下列範例的用戶端程式碼中。

> [!NOTE]
> 如果您打算使用自訂模型，您也需要該模型的識別碼。 請注意，這不是您在 [端點詳細資料] 檢視中所找到的部署或端點識別碼，而是您按一下該模型的 [詳細資料] 時可擷取的模型識別碼

## <a name="sample-code"></a>範例程式碼

使用 API 相當簡單。 下列範例程式碼必須使用訂用帳戶金鑰和 API 金鑰自訂，才能讓開發人員取得持有人權杖，如下列程式碼片段所示：

```cs
    public static async Task<CrisClient> CreateApiV1ClientAsync(string username, string key, string hostName, int port)
        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;

            var tokenProviderPath = "/oauth/ctoken";
            var clientToken = await CreateClientTokenAsync(client, hostName, port, tokenProviderPath, username, key).ConfigureAwait(false);
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", clientToken.AccessToken);

            return new CrisClient(client);
        }
```

一旦取得權杖之後，開發人員必須指定指向需要轉譯之音訊檔案的 SAS Uri。 其餘的程式碼只會逐一查看狀態，並顯示結果。

```cs
   static async Task TranscribeAsync()
        { 
            // Creating a Batch transcription API Client
            var client = 
                await CrisClient.CreateApiV1ClientAsync(
                    "<your msa>", // MSA email
                    "<your api key>", // API key
                    "stt.speech.microsoft.com",
                    443).ConfigureAwait(false);
            
            var newLocation = 
                await client.PostTranscriptionAsync(
                    "<selected locale i.e. en-us>", // Locale 
                    "<your subscription key>", // Subscription Key
                    new Uri("<SAS URI to your file>")).ConfigureAwait(false);

            var transcription = await client.GetTranscriptionAsync(newLocation).ConfigureAwait(false);

            while (true)
            {
                transcription = await client.GetTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                if (transcription.Status == "Failed" || transcription.Status == "Succeeded")
                {
                    Console.WriteLine("Transcription complete!");

                    if (transcription.Status == "Succeeded")
                    {
                        var resultsUri = transcription.ResultsUrls["channel_0"];

                        WebClient webClient = new WebClient();

                        var filename = Path.GetTempFileName();
                        webClient.DownloadFile(resultsUri, filename);

                        var results = File.ReadAllText(filename);
                        Console.WriteLine(results);
                    }

                    await client.DeleteTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                    break;
                }
                else
                {
                    Console.WriteLine("Transcription status: " + transcription.Status);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }

            Console.ReadLine();
        }
```

> [!NOTE]
> 上述程式碼片段中所提及的訂用帳戶金鑰，是您在 Azure 入口網站上建立之語音 (預覽) 資源中的金鑰。 從自訂語音服務資源取得的金鑰將無法運作。


請注意，張貼音訊和接收轉譯狀態是非同步設定。 建立的用戶端是 .NET HTTP 用戶端。 您可以使用 `PostTranscriptions` 方法來傳送音訊檔案詳細資料，並使用 `GetTranscriptions` 方法來接收結果。 `PostTranscriptions` 會傳回控制代碼，而 `GetTranscriptions` 方法會使用此控制代碼來建立控制代碼，以取得轉譯狀態。

目前的範例程式碼未指定任何自訂模型。 該服務將會使用基準模型來轉譯檔案。 如果使用者想要指定模型，使用者可以將原音和語言模型的模型識別碼傳遞給相同的方法。 

如果使用者不想要使用基準，使用者必須傳遞原音和語言模型的模型識別碼。

> [!NOTE]
> 針對基準轉譯，使用者不需要宣告基準模型的端點。 如果使用者想要使用自訂模型，就必須提供其端點識別碼作為[範例](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)。 如果使用者想要搭配使用原音基準和基準語言模型，則只需要宣告自訂模型的端點識別碼。 我們的系統會在內部找出夥伴基準模型 (原音或語言)，然後使用該模型來完成轉譯要求。

### <a name="supported-storage"></a>支援的儲存體

Azure Blob 是目前唯一支援的儲存體。

## <a name="downloading-the-sample"></a>下載範例

此處顯示的範例是在 [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI) 上。

> [!NOTE]
> 音訊轉譯所需的時間長度，通常相當於音訊檔案持續時間加上 2-3 分鐘的額外時間。

## <a name="next-steps"></a>後續步驟

* [取得語音試用版訂用帳戶](https://azure.microsoft.com/try/cognitive-services/)
