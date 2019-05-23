---
title: 轉譯多名參與者交談與 Speech SDK-語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用 Speech SDK 中的交談文字記錄。 適用於C++， C#，和 Java。
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jhakulin
ms.openlocfilehash: 80ec606fee30c239d47bca94188d3b9cbb7c82d5
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604405"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>轉譯多名參與者交談與 Speech SDK

語音 SDK **ConversationTranscriber** API 可讓您能夠新增、 移除及識別語音服務使用的音訊資料流處理的參與者 ip-pbx 會議/交談`PullStream`或`PushStream`.

## <a name="limitations"></a>限制

* 交談手寫程式 來支援C++， C#，並在 Windows、 Linux 和 Android 上的 Java。
* ROOBO DevKit 是建立交談則轉譯，因為，可以利用的循環多麥克風陣列提供有效率地說話者識別支援的硬體環境。 [如需詳細資訊，請參閱語音裝置 SDK](speech-devices-sdk.md)。
* 限制使用的音訊提取和推送模式資料流，具有八個通道的 16 位元 16 kHz PCM 音訊交談文字記錄語音 SDK 支援。
* 交談文字記錄是目前提供 「 EN-US 」 和 「 ZH-CN 」 在以下區域內的語言版本： centralus 美國東部和東亞。

## <a name="prerequisites"></a>必要條件

* [了解如何使用 Speech SDK 中的語音轉換文字。](quickstart-csharp-dotnet-windows.md)
* [取得語音試用版訂用帳戶。](https://azure.microsoft.com/try/cognitive-services/)
* 語音 SDK 1.5.1 版或更新版本。

## <a name="create-voice-signatures-for-participants"></a>建立參與者的語音簽章

第一個步驟是建立交談的參與者的語音簽章。 需要有效率的說話者識別建立語音簽章。

### <a name="requirements-for-input-wave-file"></a>輸入的 wave 檔案的需求

* 建立語音簽章的輸入音訊 wave 檔應該放在 16 位元範例、 16 kHz 範例速率，與單一通道 (Mono) 格式。
* 每個音訊範例建議的長度為 30 秒到兩分鐘之間。

下列範例示範兩種不同的方式，來建立語音簽章 [使用 REST API]。(https://aka.ms/cts/signaturegenservice)從C#:

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribing-conversations"></a>謄寫交談

若要轉譯與多個參與者的交談，建立`ConversationTranscriber`相關聯的物件`AudioConfig`交談工作階段和串流音訊使用所建立的物件`PullAudioInputStream`或`PushAudioInputStream`。

假設您有一個叫做 ConversationTranscriber 類別`MyConversationTranscriber`。 您的程式碼可能如下所示：

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Conversation;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own subscription key and region.
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file.
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            // Creates a conversation transcriber using audio stream input.
            using (var transcriber = new ConversationTranscriber(config, audioInput))
            {
                // Subscribes to events.
                transcriber.Recognizing += (s, e) =>
                {
                    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                };

                transcriber.Recognized += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                transcriber.Canceled += (s, e) =>
                {
                    Console.WriteLine($"CANCELED: Reason={e.Reason}");

                    if (e.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                        Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        stopTranscription.TrySetResult(0);
                    }
                };

                transcriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine("\nSession started event.");
                };

                transcriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine("\nSession stopped event.");
                    Console.WriteLine("\nStop recognition.");
                    stopTranscription.TrySetResult(0);
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create voice signatures using REST API described in the earlier section in this document. 
                // Voice signature needs to be in the following format:
                // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

                var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                transcriber.AddParticipant(speakerA);
                transcriber.AddParticipant(speakerB);
                transcriber.AddParticipant(speakerC);

                // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                await transcriber.StartTranscribingAsync().ConfigureAwait(false);

                // Waits for completion.
                // Use Task.WaitAny to keep the task rooted.
                Task.WaitAny(new[] { stopTranscription.Task });

                // Stop transcribing the conversation.
                await transcriber.StopTranscribingAsync().ConfigureAwait(false);

                // Ends the conversation.
                await transcriber.EndConversationAsync().ConfigureAwait(false);
            }
        }
    }
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 GitHub 上探索我們的範例](https://aka.ms/csspeech/samples)
