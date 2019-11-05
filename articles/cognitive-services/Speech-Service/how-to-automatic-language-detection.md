---
title: 如何：使用語音轉換文字語音服務的自動語言偵測
titleSuffix: Azure Cognitive Services
description: 語音 SDK 支援語音轉換文字的自動語言偵測。 使用這項功能時，所提供的音訊會與提供的語言清單進行比較，並決定最可能的相符項。 然後，傳回的值可以用來選取用於語音轉換文字的語言模型。
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: qiohu
ms.openlocfilehash: e156704d3ad54c3437f921fae536a497e1d94868
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500876"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>語音轉換文字的自動語言偵測

自動語言偵測是用來判斷與所提供語言清單相較之下，傳遞至語音 SDK 的音訊最可能相符的情況。 然後，自動語言偵測傳回的值會用來選取語音轉換文字的語言模型，為您提供更精確的轉譯。 若要查看有哪些語言可供使用，請參閱[語言支援](language-support.md)。

在本文中，您將瞭解如何使用 `AutoDetectSourceLanguageConfig` 來建立 `SpeechRecognizer` 物件，並取出偵測到的語言。

> [!IMPORTANT]
> 這項功能僅適用于的語音 SDK C++和適用于 JAVA 的語音 sdk。

## <a name="automatic-language-detection-with-the-speech-sdk"></a>使用語音 SDK 進行自動語言偵測

自動語言偵測目前在每次偵測時有兩種語言的服務端限制。 在建設您的 `AudoDetectSourceLanguageConfig` 物件時，請記住這項限制。 在下列範例中，您將建立 `AutoDetectSourceLanguageConfig`，然後使用它來建立 `SpeechRecognizer`。

>[!TIP]
> 您也可以指定要在執行語音轉換文字時使用的自訂模型。 如需詳細資訊，請參閱[使用自訂模型進行自動語言偵測](#use-a-custom-model-for-automatic-language-detection)。

下列程式碼片段說明如何在您的應用程式中使用自動語言偵測：

```C++
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

```Java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

## <a name="use-a-custom-model-for-automatic-language-detection"></a>使用自訂模型進行自動語言偵測

除了使用語音服務模型偵測語言之外，您還可以指定自訂模型來進行增強式辨識。 如果未提供自訂模型，服務將會使用預設語言模型。

下列程式碼片段說明如何在您的語音服務呼叫中指定自訂模型。 如果偵測到的語言 `en-US`，則會使用預設模型。 如果偵測到的語言 `fr-FR`，則會使用自訂模型的端點：

```C++
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(sourceLanguageConfigs);
```

```Java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(sourceLanguageConfigs);
```

## <a name="next-steps"></a>後續步驟

* [語音 SDK 參考檔](speech-sdk.md)
