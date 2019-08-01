---
title: 使用語音 SDK 轉譯多參與者交談-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何搭配語音 SDK 使用對話轉譯。 適用于C++、 C#和 JAVA。
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: jhakulin
ms.openlocfilehash: 8c4ecc017d058900297f2220173e064700e7051b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559466"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>使用語音 SDK 轉譯多參與者交談

語音 SDK 的**ConversationTranscriber** API 可讓您透過使用`PullStream`或`PushStream`, 將音訊串流至語音服務, 以轉譯會議/交談功能來新增、移除和識別參與者。

## <a name="limitations"></a>限制

* Windows、Linux 和 Android C++上C#的、和 JAVA 都支援對話 transcriber。
* ROOBO DevKit 是支援建立交談轉譯的硬體環境, 因為它提供迴圈的多麥克風陣列, 可有效率地用於說話者識別。 [如需詳細資訊, 請參閱語音裝置 SDK](speech-devices-sdk.md)。
* 對對話轉譯的語音 SDK 支援僅限於使用音訊提取和推播模式串流, 其中8個通道的16位 16 kHz PCM 音訊。
* 對話轉譯目前以 "en-us" 和 "zh-CN" 語言提供, 位於下欄區域: centralus 和 eastasia。

## <a name="prerequisites"></a>必要條件

* [瞭解如何搭配語音 SDK 使用語音轉換文字。](quickstart-csharp-dotnet-windows.md)
* [取得您的語音試用版訂用帳戶。](https://azure.microsoft.com/try/cognitive-services/)
* 需要1.5.1 或更新版本的語音 SDK。

## <a name="create-voice-signatures-for-participants"></a>為參與者建立語音簽名

第一個步驟是為對話參與者建立語音簽名。 需要建立語音簽名, 才能有效說話者識別。

### <a name="requirements-for-input-wave-file"></a>輸入 wave 檔案的需求

* 用於建立語音簽名的輸入音訊 wave 檔案, 應為16位樣本、16 kHz 採樣速率和單一通道 (Mono) 格式。
* 每個音訊樣本的建議長度介於30秒到2分鐘之間。

下列範例示範[使用 REST API](https://aka.ms/cts/signaturegenservice)從C#建立語音簽名的兩種不同方式:

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

## <a name="transcribing-conversations"></a>轉譯交談

若要轉譯與多個參與者的交談`ConversationTranscriber` , 請建立`AudioConfig`與為對話會話建立的物件相關聯的物件, 並`PullAudioInputStream`使用`PushAudioInputStream`或串流音訊。

假設您有一個名`MyConversationTranscriber`為的 ConversationTranscriber 類別。 您的程式碼看起來可能像這樣:

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
