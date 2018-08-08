---
title: 使用適用於 C++ 的語音 SDK 從語音辨識意圖
titleSuffix: Microsoft Cognitive Services
description: >
  使用適用於 C++ 的語音 SDK，示範各種從語音辨識意圖的方式 (從檔案或從麥克風)。
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: bbcaabd85ef11ba162b78857d0ba3f3e7c28277a
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331240"
---
# <a name="recognize-intents-from-speech-by-using-the-speech-sdk-for-c"></a>使用適用於 C++ 的語音 SDK 從語音辨識意圖

[!include[Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-selector.md)]

[!include[Intro](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-intro.md)]

[!include[Intro - top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#toplevel)]

[!include[Intro - using microphone](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-microphone.md)]

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

[!include[Intro - using microphone and language](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-microphone-language.md)]

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

[!include[Intro - continuous file](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-continuous.md)]

[!code-cpp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentContinuousRecognitionWithFile)]

[!include[Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
您可以在 `samples/cpp/windows/console` 資料夾中尋找本文中的程式碼。

## <a name="next-steps"></a>後續步驟

- [如何辨識語音](how-to-recognize-speech-cpp.md)
- [如何轉譯語音](how-to-translate-speech-cpp.md)
