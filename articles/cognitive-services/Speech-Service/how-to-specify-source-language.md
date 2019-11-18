---
title: 如何指定語音轉換文字的來來源語言
titleSuffix: Azure Cognitive Services
description: 語音 SDK 可讓您在將語音轉換成文字時，指定來源語言。 本文說明如何使用 FromConfig 和 SourceLanguageConfig 方法，讓語音服務知道來源語言，並提供自訂模型目標。
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 94b8fb026b61b52e8096cf54e1db30a6c260c04b
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109948"
---
# <a name="specify-source-language-for-speech-to-text"></a>指定語音轉換文字的來來源語言

在本文中，您將瞭解如何針對傳遞至語音 SDK 以進行語音辨識的音訊輸入，指定來源語言。 此外，也會提供範例程式碼來指定自訂語音模型，以改善辨識。

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>如何在中指定來源語言C#

第一個步驟是建立 `SpeechConfig`：

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

接下來，使用 `SpeechRecognitionLanguage`來指定音訊的來來源語言：

```csharp
speechConfig.SpeechRecognitionLanguage = "de-DE";
```

如果您使用自訂模型進行辨識，您可以指定具有 `EndpointId`的端點：

```csharp
speechConfig.EndpointId = "The Endpoint ID for your custom model.";
```

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>如何在中指定來源語言C++

在此範例中，會使用 `FromConfig` 方法，將原始語言明確地提供給參數。

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

在此範例中，會使用 `SourceLanguageConfig`來提供來來源語言。 然後，在建立 `recognizer`時，`sourceLanguageConfig` 會當做參數傳遞給 `FromConfig`。

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

在此範例中，會使用 `SourceLanguageConfig`來提供來來源語言和自訂端點。 建立 `recognizer`時，`sourceLanguageConfig` 會當做參數傳遞給 `FromConfig`。

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` 和 `SetEndpointId` 在和 JAVA 的 `SpeechConfig` 類別中C++是已被取代的方法。 不建議使用這些方法，而在建立 `SpeechRecognizer`時則不應使用。

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>如何在 JAVA 中指定來源語言

在此範例中，建立新的 `SpeechRecognizer`時，會明確地提供來來源語言。

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

在此範例中，會使用 `SourceLanguageConfig`來提供來來源語言。 然後，在建立新的 `SpeechRecognizer`時，會以參數的形式傳遞 `sourceLanguageConfig`。

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

在此範例中，會使用 `SourceLanguageConfig`來提供來來源語言和自訂端點。 然後，在建立新的 `SpeechRecognizer`時，會以參數的形式傳遞 `sourceLanguageConfig`。

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` 和 `setEndpointId` 在和 JAVA 的 `SpeechConfig` 類別中C++是已被取代的方法。 不建議使用這些方法，而在建立 `SpeechRecognizer`時則不應使用。

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>如何在 Python 中指定來源語言

第一個步驟是建立 `speech_config`：

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

接下來，使用 `speech_recognition_language`來指定音訊的來來源語言：

```Python
speech_config.speech_recognition_language="de-DE"
```

如果您使用自訂模型進行辨識，您可以指定具有 `endpoint_id`的端點：

```Python
speech_config.endpoint_id = "The Endpoint ID for your custom model."
```

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>如何在 JAVAscript 中指定來源語言

第一個步驟是建立 `SpeechConfig`：

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

接下來，使用 `speechRecognitionLanguage`來指定音訊的來來源語言：

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

如果您使用自訂模型進行辨識，您可以指定具有 `endpointId`的端點：

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>如何指定目標中的來源語言-C

第一個步驟是建立 `speechConfig`：

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

接下來，使用 `speechRecognitionLanguage`來指定音訊的來來源語言：

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

如果您使用自訂模型進行辨識，您可以指定具有 `endpointId`的端點：

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>另請參閱

* 如需支援的語言和語音轉換文字的地區設定清單，請參閱[語言支援](language-support.md)。

## <a name="next-steps"></a>後續步驟

* [語音 SDK 參考檔](speech-sdk.md)
