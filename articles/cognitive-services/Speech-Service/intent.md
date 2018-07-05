---
title: 意圖辨識範例 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 以下為意圖辨識範例。
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 38f7f038a803546adb83245519efc5de0c0d1599
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045005"
---
# <a name="sample-for-intent-recognition"></a>意圖辨識範例

> [!NOTE]
> 如需下載此範例和其他範例的指示，請參閱[語音 SDK 的範例](samples.md)。

> [!NOTE]
> 請先取得訂用帳戶金鑰。 相較於認知服務語音 SDK 支援的其他服務，意圖辨識服務需要特定的訂用帳戶金鑰。 您可以在[這裡](https://www.luis.ai)找到更多的意圖辨識技術，以及如何取得訂用帳戶金鑰的資訊。 將您自己的訂用帳戶金鑰、服務區域，以及您意圖模型的應用程式識別碼重新放回範例中適當的位置。

> [!NOTE]
> 以下所有範例都應該具備下列最上層宣告：
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="intent-recognition-using-microphone"></a>使用麥克風的意圖辨識

下方程式碼片段示範如何以預設語言 (`en-US`) 辨識麥克風語音輸入的意圖。

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

- - -

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>辨識麥克風傳來之指定語言音訊的意圖

下列程式碼片段顯示如何辨識麥克風輸入中的指定語言音訊的意圖，在本例中為德文 (`de-de`)。

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

- - -

## <a name="intent-recognition-from-a-file"></a>從檔案進行意圖辨識

下列程式碼片段辨識以預設語言訴說之 (`en-US`) 音訊檔案的意圖，支援的格式是取樣率為 16 KHz 的單一頻道 (單聲道) WAV/PCM。

[!include[Sample Audio](includes/sample-audio.md)]

[!code-cpp[Intent Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithFile)]

- - -

## <a name="intent-recognition-using-events"></a>使用事件辨識意圖

程式碼片段顯示如何以連續的方式辨識意圖。 此程式碼允許存取其他資訊，例如中繼結果。 

[!code-cpp[Intent Recognition Using Events](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>範例原始程式碼

如需最新的範例集，請參閱[認知服務語音 SDK 範例 GitHub 存放庫](https://aka.ms/csspeech/samples) (英文)。

## <a name="next-steps"></a>後續步驟

- [語音辨識](./speech-to-text-sample.md)

- [翻譯](./translation.md)
