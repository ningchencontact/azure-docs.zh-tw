---
title: 語音轉換文字與 Azure 的語音服務
titleSuffix: Azure Cognitive Services
description: 語音轉換文字從 Azure 語音服務，也就是語音轉換文字，啟用即時轉譯的音訊資料流為可使用您的應用程式、 工具或裝置，文字會顯示，，並採取動作做為命令的輸入。 這項服務具備相同，Microsoft 會使用 Cortana 和 Office 產品的完美搭配的轉譯和文字轉換語音辨識技術。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: fcb09720d188150736dbe7da0c925f5762ff6a10
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072323"
---
# <a name="what-is-speech-to-text"></a>什麼是語音轉換文字？

語音轉換文字從 Azure 語音服務，也就是語音轉換文字，啟用即時轉譯的音訊資料流為可使用您的應用程式、 工具或裝置，文字會顯示，，並採取動作做為命令的輸入。 這項服務具備相同，Microsoft 會使用 Cortana 和 Office 產品的完美搭配的轉譯和文字轉換語音辨識技術。  如需可用的語音轉換文字語言的完整清單，請參閱 <<c0> [ 支援的語言](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text)。

根據預設，語音轉換文字服務會使用通用的語言模型。 此模型已定型使用 Microsoft 所擁有的資料，並部署在雲端中。 它是適合交談和語音輸入案例。 如果您在獨特的環境中使用語音轉文字進行辨識及轉譯，您可以建立並定型自訂原音、語言和發音模型，以處理環境噪音或業界專有詞彙。 

您可以輕鬆地擷取從麥克風的音訊、 讀取從資料流中，或從儲存體與 Speech SDK 和 REST Api 存取音訊檔案。 語音 SDK 支援 WAV PCM/16 位元，16 kHz/8 kHz、 單一通道語音辨識的音訊。 使用可支援額外的音訊格式[語音轉換文字 REST 端點](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)或[batch 轉譯服務](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)。

## <a name="core-features"></a>核心功能

以下是功能可透過語音 SDK 和 REST Api:

| 使用案例 | SDK | REST |
|----------|-----|------|
| Ip-pbx 簡短的表達方式 (< 15 秒)。 僅支援最終轉譯結果。 | 是 | 是 |
| 連續的 long 談話和串流音訊轉譯項目 (> 15 秒)。 支援中期與最終轉譯結果。 | 是 | 否 |
| 是衍生自與辨識結果的意圖[LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)。 | 是 | 否\* |
| 以非同步方式的批次音訊檔轉譯項的目。 | 否 | 是\** |
| 建立和管理語音模型。 | 否 | 是\** |
| 建立和管理自訂模型部署。 | 否 | 是\** |
| 建立精確度測試以測量基準模型，與自訂模型的精確度。 | 否 | 是\** |
| 管理訂用帳戶。 | 否 | 是\** |

\* *您可以使用個別的 LUIS 訂用帳戶來衍伸出 LUIS 意圖和實體。與此訂用帳戶，可以為您呼叫 LUIS SDK，並將其提供實體和意圖結果中。若使用 REST API，您可以自行呼叫 LUIS，以使用您的 LUIS 訂用帳戶來衍伸出意圖和實體。*

\** *這些服務可使用 cris.ai 端點。請參閱[Swagger 參考](https://westus.cris.ai/swagger/ui/index)。*

## <a name="get-started-with-speech-to-text"></a>開始使用語音轉換文字

我們以最受歡迎的程式設計語言提供快速入門，目的是讓您能在 10 分鐘內執行程式碼。 此資料表包括依語言的語音 SDK 快速入門的完整清單。

| 快速入門 | 平台 | API 參考資料 |
|------------|----------|---------------|
| [C#, .NET Core](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnetcore-windows) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#.NET framework](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnet-windows) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-uwp) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-windows) | Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-linux) | Linux | [Browse](https://aka.ms/csspeech/cppref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-android) | Android | [Browse](https://aka.ms/csspeech/javaref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-jre) | Windows、Linux、macOS | [Browse](https://aka.ms/csspeech/javaref) |
| [JavaScript 中，瀏覽器](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-browser) | Browser、Windows、Linux、macOS | [Browse](https://aka.ms/AA434tv) |
| [JavaScript、 Node.js](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-node) | Windows、Linux、macOS | [Browse](https://aka.ms/AA434tv) |
| [Objective-C](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-objectivec-ios) | iOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| [Python](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-python) | Windows、Linux、macOS | [Browse](https://aka.ms/AA434tr)  |

如果您想要使用語音轉換文字 REST 服務，請參閱[REST Api](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)。

## <a name="tutorials-and-sample-code"></a>教學課程和範例程式碼

當您有機會使用語音服務後，請嘗試使用我們的教學課程，其中會教導您如何使用語音 SDK 和 LUIS 從語音辨識意圖。

* [教學課程：使用語音 SDK 和 LUIS 從語音辨識意圖 (C#)](how-to-recognize-intents-from-speech-csharp.md)

使用 GitHub 上適用於語音 SDK 的範例程式碼。 這些範例包含常見案例，例如：從檔案或資料流讀取音訊、連續辨識、一次性辨識及使用自訂模型。

* [語音轉換文字範例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [批次轉譯範例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>自訂

除了使用語音服務的通用模型，您可以建立體驗的特定自訂的原音、 語言和發音模型。 以下是自訂選項的清單：

| 模型 | 描述 |
|-------|-------------|
| [原音模型](how-to-customize-acoustic-models.md) | 建立自訂的原音模型是有幫助，如果您的應用程式、 工具或裝置中使用特定的環境中，例如在車上或特定的記錄條件的處理站。 例如，帶有口音的語音、特定背景雜音或使用特定麥克風來錄音。 |
| [語言模型](how-to-customize-language-model.md) | 建立自訂語言模型來提升特定產業的詞彙和文法轉譯，例如醫療術語或 IT 專業術語。 |
| [發音模型](how-to-customize-pronunciation.md) | 使用自訂發音模型，您可以定義語音形式和顯示字組或字詞。 它可用於處理自訂的字詞，如產品名稱或縮略字。 您只需要有發音檔 - 簡單的 .txt 檔。 |

> [!NOTE]
> 語言/地區而異的自訂選項 (請參閱[支援的語言](supported-languages.md))。

## <a name="migration-guides"></a>移轉指南

> [!WARNING]
> Bing 語音晚會於 2019 年 10 月 15 日。

如果您的應用程式、 工具或產品正在使用自訂語音的 Bing 語音 Api，我們建立了指南，以協助您移轉至語音服務。

* [從 Bing 語音移轉至語音服務](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [從客製化語音移轉至語音服務](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>參考文件

* [語音 SDK](speech-sdk-reference.md)
* [語音裝置 SDK](speech-devices-sdk.md)
* [REST API：語音轉文字](rest-speech-to-text.md)
* [REST API：文字轉語音](rest-text-to-speech.md)
* [REST API：批次轉譯與自訂](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
* [取得語音 SDK](speech-sdk.md)
