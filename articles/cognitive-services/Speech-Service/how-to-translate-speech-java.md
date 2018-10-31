---
title: 使用適用於 Java 的語音 SDK 來轉譯語音
titleSuffix: Azure Cognitive Services
description: 示範如何使用適用於 Java 的語音 SDK 來轉譯語音。
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 53d15e56284c24e6bacf382aa9aab7548b930191
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2018
ms.locfileid: "49464109"
---
# <a name="translate-speech-with-the-speech-sdk-for-java"></a>使用適用於 Java 的語音 SDK 來轉譯語音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-translate-speech-selector.md)]

[!INCLUDE [Introduction](../../../includes/cognitive-services-speech-service-how-to-translate-speech-intro.md)]

[!INCLUDE [Introduction to top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#toplevel)]

[!INCLUDE [Introduction to using a microphone](../../../includes/cognitive-services-speech-service-how-to-translate-speech-microphone.md)]

[!code-java [Translation from a microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#TranslationWithMicrophoneAsync)]

[!INCLUDE [Introduction to using a file](../../../includes/cognitive-services-speech-service-how-to-translate-speech-file.md)]

[!code-java [Translation from file input](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#TranslationWithFileAsync)]

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 samples/java/jre/console 資料夾中尋找本文使用的程式碼。

## <a name="next-steps"></a>後續步驟

- [如何辨識語音](how-to-recognize-speech-java.md)
- [如何從語音辨識意圖](how-to-recognize-intents-from-speech-java.md)
