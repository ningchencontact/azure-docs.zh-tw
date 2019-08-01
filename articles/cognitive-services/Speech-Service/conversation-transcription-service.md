---
title: 對話轉譯-語音服務
titleSuffix: Azure Cognitive Services
description: 對話轉譯是語音服務的一項先進功能, 結合了即時語音辨識、說話者識別和 diarization。 對話轉譯非常適合用來轉譯人員會議, 而且能夠區分說話者, 讓您知道誰說什麼, 以及如何讓參與者專注在會議上, 並快速追蹤後續步驟。 這項功能也改善了協助工具。 有了轉譯, 您就可以積極地吸引參與者的聽力障礙。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: acbf82335523ee226f4b899180f0f22cb94cca5e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562874"
---
# <a name="what-is-conversation-transcription"></a>什麼是對話轉譯？

對話轉譯是語音服務的一項先進功能, 結合了即時語音辨識、說話者識別和 diarization。 對話轉譯非常適合用來轉譯人員會議, 而且能夠區分說話者, 讓您知道誰說什麼, 以及如何讓參與者專注在會議上, 並快速追蹤後續步驟。 這項功能也改善了協助工具。 有了轉譯, 您就可以積極地吸引參與者的聽力障礙。   

對話轉譯透過可自訂的語音模型提供精確的辨識, 讓您可以量身打造以瞭解產業和公司專屬的詞彙。 此外, 您可以使用語音裝置 SDK 配對對話轉譯, 以優化多麥克風裝置的使用體驗。

>[!NOTE]
> 目前建議對小型會議進行對話轉譯。 如果您想要大規模延伸對大型會議的交談轉譯, 請洽詢我們。

此圖說明搭配對話轉譯一起使用的硬體、軟體和服務。

![匯入交談轉譯圖表](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> 需要具有特定幾何設定的迴圈七個麥克風陣列。 如需規格和設計詳細資料, 請參閱[Microsoft 語音裝置 SDK 麥克風](https://aka.ms/cts/microphone)。 若要深入瞭解或購買開發工具組, 請參閱[取得 Microsoft 語音裝置 SDK](https://aka.ms/cts/getsdk)。

## <a name="get-started-with-conversation-transcription"></a>開始使用對話轉譯

您需要執行三個步驟, 才能開始使用對話轉譯。

1. 收集使用者的語音範例。
2. 使用 user voice 範例產生使用者設定檔
3. 使用語音 SDK 來識別使用者 (喇叭) 和轉譯語音

## <a name="collect-user-voice-samples"></a>收集使用者語音範例

第一個步驟是從每個使用者收集音訊錄影。 使用者語音應該記錄在無背景雜音的安靜環境中。 每個音訊樣本的建議長度介於30秒到2分鐘之間。 較長的音訊範例會在識別說話者時提高準確度。 音訊必須是具有 16 KHz 取樣率的 mono 頻道。

除了前述的指導之外, 我們還會為您提供如何記錄及儲存音訊的方式--建議使用安全的資料庫。 在下一節中, 我們將探討如何使用此音訊來產生與語音 SDK 搭配使用來辨識喇叭的使用者設定檔。

## <a name="generate-user-profiles"></a>產生使用者設定檔

接下來, 您必須將收集到的錄音記錄傳送到簽章產生服務, 以驗證音訊並產生使用者設定檔。 簽章[產生服務](https://aka.ms/cts/signaturegenservice)是一組 REST api, 可讓您產生和取出使用者設定檔。

若要建立使用者設定檔, 您必須使用`GenerateVoiceSignature` API。 提供規格詳細資料和範例程式碼:

> [!NOTE]
> 對話轉譯目前可在下欄區域中的 "en-us" 和 "zh-CN" 中使用: `centralus`和。 `eastasia`

* [REST 規格](https://aka.ms/cts/signaturegenservice)
* [如何使用對話轉譯](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>轉譯和識別喇叭

對話轉譯需要多重通道音訊資料流程和使用者設定檔作為輸入, 以產生轉譯和識別說話者。 音訊和使用者設定檔資料會使用語音裝置 SDK 傳送至對話轉譯服務。 如先前所述, 需要迴圈七個麥克風陣列和語音裝置 SDK, 才能使用對話轉譯。

>[!NOTE]
> 如需規格和設計詳細資料, 請參閱[Microsoft 語音裝置 SDK 麥克風](https://aka.ms/cts/microphone)。 若要深入瞭解或購買開發工具組, 請參閱[取得 Microsoft 語音裝置 SDK](https://aka.ms/cts/getsdk)。

若要瞭解如何使用「語音裝置 SDK」進行對話轉譯, 請參閱[如何使用對話](https://aka.ms/cts/howto)轉譯。


## <a name="quick-start-with-a-sample-app"></a>範例應用程式快速入門

Microsoft 語音裝置 SDK 具有適用于所有裝置相關範例的快速入門範例應用程式。 對話轉譯是其中一種。 您可以在使用範例應用程式的[語音裝置 SDK android 快速入門](https://aka.ms/sdsdk-quickstart)和其原始程式碼中找到, 以供您參考。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入瞭解語音裝置 SDK](speech-devices-sdk.md)
