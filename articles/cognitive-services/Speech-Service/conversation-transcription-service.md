---
title: 交談文字記錄-語音服務
titleSuffix: Azure Cognitive Services
description: 交談文字記錄是結合了即時語音辨識、 說話者識別和 diarization 語音服務的進階的功能。 交談文字記錄非常適合能夠區別喇叭謄寫參與會議，它可讓您知道誰說作用及時，將焦點放在會議上快速地讓參與者看過接下來的步驟。 這項功能也會改善協助工具。 使用轉譯，您可以主動連絡聽力障礙的參與者。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: a31921e229a4bbfccd6fdd871666aad1eeef3232
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243852"
---
# <a name="what-is-conversation-transcription"></a>交談文字記錄是什麼？

交談文字記錄是結合了即時語音辨識、 說話者識別和 diarization 語音服務的進階的功能。 交談文字記錄非常適合能夠區別喇叭謄寫參與會議，它可讓您知道誰說作用及時，將焦點放在會議上快速地讓參與者看過接下來的步驟。 這項功能也會改善協助工具。 使用轉譯，您可以主動連絡聽力障礙的參與者。   

交談文字記錄會提供正確辨識具有可自訂的語音模型，您可以調整以了解業界和公司特定詞彙。 此外，您可以將交談文字記錄語音裝置 sdk，以最佳化多麥克風裝置的體驗。

>[!NOTE]
> 目前，小型的會議建議交談文字記錄。 如果您想要擴充規模的大型會議的交談文字記錄，請與我們連絡。

此圖說明硬體、 軟體和服務的交談文字記錄搭配運作。

![匯入交談文字記錄圖表](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> 需要特定的幾何組態的循環七個麥克風陣列。 規格和設計的詳細資訊，請參閱[Microsoft 語音裝置 SDK 麥克風](https://aka.ms/cts/microphone)。 若要了解更多或購買一種開發套件，請參閱[取得 Microsoft 語音裝置 SDK](https://aka.ms/cts/getsdk)。

## <a name="get-started-with-conversation-transcription"></a>開始使用交談文字記錄

有三個步驟，您需要採取來開始交談文字記錄。

1. 從使用者收集語音範例。
2. 產生在使用者語音範例使用的使用者設定檔
3. 識別使用者 （喇叭） 和 ip-pbx 語音使用語音 SDK

## <a name="collect-user-voice-samples"></a>收集使用者語音範例

第一個步驟是從每個使用者收集音訊錄製。 使用者語音應該記錄在安靜的環境，而不需要背景雜音。 每個音訊範例建議的長度為 30 秒到兩分鐘之間。 識別說話者時，較長的音訊範例將會導致改善的精確度。 音訊必須是 16 KHz 範例速率的單聲道。

超出上述的指引，如何音訊記錄並儲存您必須負責-安全的資料庫，建議您使用。 在下一步 區段中，我們會檢閱此音訊如何用來產生語音 sdk 可用來辨識說話者的使用者設定檔。

## <a name="generate-user-profiles"></a>產生使用者設定檔

接下來，您必須傳送音訊錄製您收集到簽章產生服務驗證的音訊，並產生使用者設定檔。 [簽章產生服務](https://aka.ms/cts/signaturegenservice)是一組 REST Api，可讓您產生及擷取使用者設定檔。

若要建立使用者設定檔，您必須使用`GenerateVoiceSignature`API。 規格詳細資料和範例程式碼可：

> [!NOTE]
> 交談文字記錄是目前可供 「 EN-US 」 和 「 ZH-CN 」 在以下區域內：`centralus`和`eastasia`。

* [REST 規格](https://aka.ms/cts/signaturegenservice)
* [如何使用交談文字記錄](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>轉譯及識別的說話者

交談文字記錄需要多頻道的音訊資料流和使用者設定檔做為輸入來產生轉譯，並識別的說話者。 音訊和使用者設定檔資料會傳送至使用語音裝置 SDK 的交談文字記錄服務。 如先前所述，循環的七個麥克風陣列和語音裝置 SDK 才能使用交談文字記錄。

>[!NOTE]
> 規格和設計的詳細資訊，請參閱[Microsoft 語音裝置 SDK 麥克風](https://aka.ms/cts/microphone)。 若要了解更多或購買一種開發套件，請參閱[取得 Microsoft 語音裝置 SDK](https://aka.ms/cts/getsdk)。

若要了解如何使用語音裝置 SDK 中的交談文字記錄，請參閱[如何使用交談文字記錄](https://aka.ms/cts/howto)。


## <a name="quick-start-with-a-sample-app"></a>範例應用程式的快速入門

Microsoft 語音裝置 SDK 快速入門範例應用程式的所有裝置的相關範例。 交談文字記錄是其中一個。 您可以找到它[語音裝置 SDK android 快速入門](https://aka.ms/sdsdk-quickstart)範例應用程式和其原始碼，供您參考。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 Speech 裝置 SDK](speech-devices-sdk.md)
