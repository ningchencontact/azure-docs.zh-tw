---
title: Stream 轉碼器壓縮的音訊與 Speech SDK-語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何將串流處理至 Azure 的語音服務與 Speech SDK 壓縮的音訊。 適用於C++， C#，和適用於 Linux 的 Java。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: amishu
ms.openlocfilehash: 41a55eca321cbe1bfa23a889b8e3ce7c701ce769
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468314"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>使用的轉碼器壓縮音訊輸入與 Speech SDK

語音 SDK**壓縮音訊輸入 Stream** API 可用來串流處理壓縮的音訊到語音服務使用 PullStream 或 PushStream。

> [!IMPORTANT]
> 壓縮的音訊資料流處理，才支援C++， C#，和在 Linux （Ubuntu 16.04，Ubuntu 18.04，Debian 9） 上的 Java。

Wav/PCM，請參閱主線語音文件。  外部 wav/PCM，支援下列的轉碼器壓縮輸入的格式：

- MP3
- 這種門外漢只/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>若要使用轉碼器的必要條件壓縮音訊輸入

安裝這些其他的相依性，以壓縮的音訊輸入搭配適用於 Linux 的語音 SDK:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>範例程式碼使用的轉碼器壓縮音訊輸入

若要串流處理以壓縮的音訊格式的語音服務，建立`PullAudioInputStream`或`PushAudioInputStream`。 接著，建立`AudioConfig`從您的資料流類別的執行個體，指定資料流的壓縮格式。

假設您有呼叫的輸入資料流類別`myPushStream`，而使用這種門外漢只/OGG。 您的程式碼可能如下所示：

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
