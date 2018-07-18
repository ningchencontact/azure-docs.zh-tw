---
title: 語音轉換文字的範例 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 以下是語音轉換文字的範例。
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 2a1850e6a4f3c8eebd1b947aabe1374bdaab3fc8
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030245"
---
# <a name="sample-for-speech-to-text"></a>語音轉換文字的範例

> [!NOTE]
> 如需下載這個範例和其他範例的指示，請參閱[語音 SDK 的範例](samples.md)。

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> 以下所有範例都應該具備下列最上層宣告：
>
> [!code-csharp[](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#toplevel)]
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="speech-recognition-using-the-microphone"></a>使用麥克風的語音辨識

下列程式碼片段示範如何以預設語言 (`en-US`) 辨識來自麥克風的語音輸入。

[!code-csharp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionWithMicrophone)]

[!code-cpp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionWithMicrophone)]

- - -

## <a name="speech-recognition-from-a-file"></a>從檔案進行的語音辨識

下列程式碼片段會以預設語言 (`en-US`) 辨識來自音訊檔案的語音輸入，支援的格式是取樣率為 16 KHz 的單一頻道 (單聲道) WAV / PCM。

[!include[Sample Audio](includes/sample-audio.md)]

[!code-csharp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs?name=recognitionFromFile)]

[!code-cpp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp?name=SpeechRecognitionWithFile)]

- - -

## <a name="speech-recognition-using-a-customized-model"></a>使用自訂模型的語音辨識

[自訂語音服務 (CRIS)](https://www.cris.ai/) 允許為您的應用程式自訂 Microsoft 語音轉換文字引擎。 下列程式碼片段示範如何使用 CRIS 模型辨識來自麥克風的語音；在執行它之前，請填入您的 CRIS 訂用帳戶金鑰與您自己的部署識別。

[!code-csharp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionCustomized)]

[!code-cpp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionUsingCustomizedModel)]

- - -

## <a name="continuous-speech-recognition"></a>持續語音辨識

[!code-csharp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionContinuous)]

[!code-cpp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>範例原始程式碼

最新版的範例和更多進階的範例位於專用的 [GitHub 存放庫](https://github.com/Azure-Samples/cognitive-services-speech-sdk)中。

## <a name="next-steps"></a>後續步驟

- [意圖辨識](./intent.md)

- [翻譯](./translation.md)
