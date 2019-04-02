---
title: 什麼是 Azure 語音服務？
titleSuffix: Azure Cognitive Services
description: Azure 語音服務會將語音轉文字、文字轉語音及語音翻譯整合至單一 Azure 訂用帳戶。 藉由語音 SDK、語音裝置 SDK 或 REST API，可輕易地將語音新增至您的應用程式、工具和裝置。 將語音功能新增至現有的聊天機器人、轉換翻譯應用程式中的文字轉語音，或轉譯話務中心的大量資料。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 03/13/2019
ms.author: erhopf
ms.openlocfilehash: 06b2a5211c720d50a2f14e5fa56fa296cb80d41f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519624"
---
# <a name="what-are-the-speech-services"></a>什麼是語音服務？

Azure 語音服務會將語音轉文字、文字轉語音及語音翻譯整合至單一 Azure 訂用帳戶。 藉由[語音 SDK](speech-sdk-reference.md)、[語音裝置 SDK](speech-devices-sdk-qsg.md) 或 [REST API](rest-apis.md)，可輕易地透過語音來啟用您的應用程式、工具和裝置。

> [!IMPORTANT]
> 語音服務已取代 Bing 語音 API、翻譯工具語音和自訂語音。 如需移轉說明，請參閱 [操作指南] > [移轉]。

Azure 語音服務是由以下功能所組成。 請使用此資料表中的連結，深入了解每項功能的常見使用案例，或瀏覽 API 參考。

| 服務 | 功能 | 說明 | SDK | REST |
|---------|---------|-------------|-----|------|
| [語音轉文字](speech-to-text.md) | 語音轉文字 | 語音轉文字會即時地將音訊串流轉譯成文字，以便您的應用程式、工具或裝置使用或顯示。 若搭配 [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) 使用語音轉文字，即可從轉譯的語音衍生使用者意圖，以及根據語音命令執行動作。 | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [批次轉譯](batch-transcription.md) | 批次轉譯可讓您非同步地對大量資料進行語音轉文字的轉譯。 這是以 REST 為基礎的服務，其在自訂和模型管理上使用相同端點。 | 否 | [是](https://westus.cris.ai/swagger/ui/index) |
| | [自訂](#customize-your-speech-experience) | 如果您在獨特的環境中使用語音轉文字進行辨識及轉譯，您可以建立並定型自訂原音、語言和發音模型，以處理環境噪音或業界專有詞彙。 | 否 | [是](https://westus.cris.ai/swagger/ui/index) |
| [文字轉換語音](text-to-speech.md) | 文字轉換語音 | 文字轉語音會將輸入文字轉換為仿真人的合成語音。 可選擇標準語音和類神經語音 (請參閱[語言支援](language-support.md))。 | 否 | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [自訂](#customize-your-speech-experience) | 建立您品牌或產品專有的自訂聲音音調。 | 否 | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [語音翻譯](speech-translation.md) | 語音翻譯 | 語音翻譯可讓您在應用程式、工具和裝置上使用即時且多語言的語音翻譯。 此服務可用於語音轉語音及語音轉文字翻譯。 | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | 否 |

## <a name="news-and-updates"></a>新功能和更新

了解 Azure 語音服務的新功能。

* 2019 年 3 月 - 現已推出新的文字轉語音端點，其可傳回特定區域中可用的完整語音清單。 如需詳細資訊，請參閱[文字轉語音 API 參考 (REST)](rest-text-to-speech.md)。
* 2019 年 2 月 - 發行支援 [Unity (Beta)](quickstart-csharp-unity.md) 的語音 SDK 1.3.0。 新增 `AudioInput` 類別的支援，可讓您選擇音訊的串流來源。 如需增強功能和已知問題的完整清單，請參閱[版本資訊](releasenotes.md)。
* 2018 年 12 月 - 發行支援 [Python](quickstart-python.md)、[Node.js](quickstart-js-node.md) 和 Ubuntu 18.04 LTS 的語音 SDK 1.2.0。 如需詳細資訊，請參閱[版本資訊](releasenotes.md)。

## <a name="try-speech-services"></a>試試語音服務

我們以最受歡迎的程式設計語言提供快速入門，目的是讓您能在 10 分鐘內執行程式碼。 此資料表包含每項功能最受歡迎的快速入門。 您可以使用左側導覽列來瀏覽其他語言及平台。

| 語音轉文字 (SDK) | 翻譯 (SDK) | 文字轉語音 (REST) |
|-------------------|-------------------|-----------------------|
| [C#、.NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [Java (Windows、Linux)](quickstart-translate-speech-java-jre.md) | [Python (Windows、Linux、macOS)](quickstart-python-text-to-speech.md) |
| [Javascript (瀏覽器)](quickstart-js-browser.md) | [C#、.NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) | [C#、.NET Core (Windows、Linux、macOS)](quickstart-dotnet-text-to-speech.md) |
| [Python (Windows、Linux、macOS)](quickstart-python.md) | [C#、.NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) | [Node.js (Windows、Linux、macOS)](quickstart-nodejs-text-to-speech.md) |
| [Java (Windows、Linux)](quickstart-java-jre.md) | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) | |

當您有機會使用語音服務後，請嘗試使用我們的教學課程，其中會教導您如何使用語音 SDK 和 LUIS 從語音辨識意圖。

* [教學課程：使用語音 SDK 和 LUIS 從語音辨識意圖 (C#)](how-to-recognize-intents-from-speech-csharp.md)

## <a name="get-sample-code"></a>取得範例程式碼

每個 Azure 語音服務的範例程式碼皆可在 GitHub 上取得。 這些範例包含常見案例，例如：從檔案或資料流讀取音訊、連續辨識、一次性辨識及使用自訂模型。 使用下列連結來檢視 SDK 和 REST 範例：

* [語音轉文字及語音翻譯範例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [批次轉譯範例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [文字轉語音範例 (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customize-your-speech-experience"></a>自訂語音體驗

Azure 語音服務可順利地與內建模型搭配使用，不過，您可以進一步自訂及調整體驗，以搭配您的產品或環境。 從原音模型調整到專屬於自身品牌的獨特聲音音調，都是自訂選項的範圍。 建立自訂模型之後，您可以將其與任一 Azure 語音服務搭配使用。

| 語音服務 | 模型 | 說明 |
|----------------|-------|-------------|
| 語音轉文字 | [原音模型](how-to-customize-acoustic-models.md) | 針對用於特定環境 (例如汽車或工廠) 的應用程式、工具或裝置建立自訂原音模型，而這每一個的錄音條件都較特殊。 例如，帶有口音的語音、特定背景雜音或使用特定麥克風來錄音。 |
| | [語言模型](how-to-customize-language-model.md) | 建立自訂語言模型來提升特定領域的詞彙和文法轉譯，例如醫療術語或 IT 專業術語。 |
| | [發音模型](how-to-customize-pronunciation.md) | 使用自訂發音模型，您可以定義語音形式和顯示字組或字詞。 它可用於處理自訂的字詞，如產品名稱或縮略字。 您只需要有發音檔 - 簡單的 .txt 檔。 |
| 文字轉語音 | [聲音音調](how-to-customize-voice-font.md) | 自訂聲音音調可讓您為自己的品牌建立可辨識的獨特聲音。 只需少量資料即可開始建立。 提供的資料愈多，您的聲音音調聽起來就愈自然且愈像真人。 |

## <a name="reference-docs"></a>參考文件

* [語音 SDK](speech-sdk-reference.md)
* [語音裝置 SDK](speech-devices-sdk.md)
* [REST API：語音轉文字](rest-speech-to-text.md)
* [REST API：文字轉語音](rest-text-to-speech.md)
* [REST API：批次轉譯與自訂](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [免費取得語音服務的訂用帳戶金鑰](get-started.md)
