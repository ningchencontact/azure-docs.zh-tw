---
title: 使用語音 SDK 的串流編解碼器壓縮音訊-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用語音 SDK 將壓縮的音訊串流至 Azure 語音服務。 適用于C++Linux C#的、和 JAVA。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: b29b42dea9522526d49c1bda017a522855946def
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559556"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>搭配使用編解碼器壓縮的音訊輸入與語音 SDK

語音 SDK 的**壓縮音訊輸入資料流程**API 提供了使用 PullStream 或 PushStream 將壓縮的音訊串流至語音服務的方式。

> [!IMPORTANT]
> 只有、 C#和 Linux (ubuntu 16.04 C++、ubuntu 18.04、Debian 9) 上的 JAVA 支援串流壓縮音訊。
> 需要1.4.0 或更高版本的語音 SDK。

針對 wav/PCM, 請參閱主線語音檔。  在 wav/PCM 以外, 支援下列編解碼器壓縮的輸入格式:

- MP3
- OPUS/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>使用編解碼器壓縮音訊輸入的必要條件

安裝這些額外的相依性, 以搭配適用于 Linux 的語音 SDK 來使用壓縮的音訊輸入:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>使用編解碼器壓縮音訊輸入的範例程式碼

若要以壓縮的音訊格式串流到語音服務, 請`PullAudioInputStream`建立`PushAudioInputStream`或。 然後, `AudioConfig`從您的資料流程類別的實例建立, 並指定資料流程的壓縮格式。

假設您有一個名`myPushStream`為的輸入資料流程類別, 而且使用 OPUS/OGG。 您的程式碼看起來可能像這樣:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
var audioFormat = AudioStreamFormat.GetCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
var audioConfig = AudioConfig.FromStreamInput(myPushStream, audioFormat);

var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

var result = await recognizer.RecognizeOnceAsync();

var text = result.GetText();
```

## <a name="next-steps"></a>後續步驟

- [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
- [了解如何以 C# 辨識語音](quickstart-csharp-dotnet-windows.md) (英文)
