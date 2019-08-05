---
title: 什麼是語音服務？
titleSuffix: Azure Cognitive Services
description: 語音服務會將語音轉文字、文字轉語音及語音翻譯整合至單一 Azure 訂用帳戶。 藉由語音 SDK、語音裝置 SDK 或 REST API，可輕易地將語音新增至您的應用程式、工具和裝置。 將語音功能新增至現有的聊天機器人、轉換翻譯應用程式中的文字轉語音，或轉譯話務中心的大量資料。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 1d722d7e2886008aa5aa3acff8095fcf35ac38d8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554179"
---
# <a name="what-are-the-speech-services"></a>什麼是語音服務？

Azure 語音服務會將語音轉文字、文字轉語音及語音翻譯整合至單一 Azure 訂用帳戶。 藉由[語音 SDK](speech-sdk-reference.md)、[語音裝置 SDK](https://aka.ms/sdsdk-quickstart) 或 [REST API](rest-apis.md)，可輕易地透過語音來啟用您的應用程式、工具和裝置。

> [!IMPORTANT]
> 語音服務已取代 Bing 語音 API、翻譯工具語音和自訂語音。 如需移轉說明，請參閱 [操作指南] > [移轉]  。

Azure 語音服務是由以下功能所組成。 請使用此資料表中的連結，深入了解每項功能的常見使用案例，或瀏覽 API 參考。

| 服務 | 功能 | 說明 | SDK | REST |
|---------|---------|-------------|-----|------|
| [語音轉文字](speech-to-text.md) | 語音轉文字 | 語音轉文字會即時地將音訊串流轉譯成文字，以便您的應用程式、工具或裝置使用或顯示。 若搭配 [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) 使用語音轉文字，即可從轉譯的語音衍生使用者意圖，以及根據語音命令執行動作。 | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [批次轉譯](batch-transcription.md) | 批次轉譯可讓您非同步地對大量資料進行語音轉文字的轉譯。 這是以 REST 為基礎的服務，其在自訂和模型管理上使用相同端點。 | 否 | [是](https://westus.cris.ai/swagger/ui/index) |
| | [對話轉譯](conversation-transcription-service.md) | 啟用即時語音辨識、說話者識別和自動分段標記功能。 非常適合利用辨識說話者的能力來轉譯面對面會議。 | yes | 否 |
| | [建立自訂語音模型](#customize-your-speech-experience) | 如果您在獨特的環境中使用語音轉文字進行辨識及轉譯，您可以建立並定型自訂原音、語言和發音模型，以處理環境噪音或業界專有詞彙。 | 否 | [是](https://westus.cris.ai/swagger/ui/index) |
| [文字轉換語音](text-to-speech.md) | 文字轉換語音 | 文字轉語音會使用[語音合成標記語言 (SSML)](text-to-speech.md#speech-synthesis-markup-language-ssml) 將輸入文字轉換為仿真人的合成語音。 可選擇標準語音和類神經語音 (請參閱[語言支援](language-support.md))。 | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [建立自訂語音](#customize-your-speech-experience) | 建立您品牌或產品專有的自訂聲音音調。 | 否 | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [語音翻譯](speech-translation.md) | 語音翻譯 | 語音翻譯可讓您在應用程式、工具和裝置上使用即時且多語言的語音翻譯。 此服務可用於語音轉語音及語音轉文字翻譯。 | [是](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | 否 |
| [語音優先虛擬助理](voice-first-virtual-assistants.md) | 語音優先虛擬助理 | 使用 Azure 語音服務自訂虛擬助理，賦予開發人員建立自然、擬人的對話介面，供應用程式和體驗之用。 Bot Framework 的 Direct Line Speech 頻道提供相容 Bot 有組織且協調的進入點，具備低延遲性和高可靠性的互動語音功能。 | [是](voice-first-virtual-assistants.md) | 否 |

## <a name="news-and-updates"></a>新功能和更新

了解 Azure 語音服務的新功能。

* 2019 年 6 月
    * 發行的語音 SDK 1.6.0。 如需更新、增強功能和已知問題的完整清單，請參閱[版本資訊](releasenotes.md)。
* 2019 年 5 月 - [對話轉譯](conversation-transcription-service.md), [話務中心轉譯](call-center-transcription.md)和[語音優先虛擬助理](voice-first-virtual-assistants.md)的文件目前可供使用。
* 2019 年 5 月
    * 發行的語音 SDK 1.5.1。 如需更新、增強功能和已知問題的完整清單，請參閱[版本資訊](releasenotes.md)。
    * 發行的語音 SDK 1.5.0。 如需更新、增強功能和已知問題的完整清單，請參閱[版本資訊](releasenotes.md)。
* 2019 年 4 月 - 已發行語音 SDK 1.4.0，在 Windows 和 Linux 上可支援適用於 C++、C# 和 Java 的文字轉語音搶鮮版 (Beta)。 此外，此 SDK 目前在 Linux 上已支援適用於 C++ 和 C# 的 MP3 和 Opus/Ogg 音訊格式。 如需更新、增強功能和已知問題的完整清單，請參閱[版本資訊](releasenotes.md)。
* 2019 年 3 月 - 現已推出新的文字轉語音 (TTS) 端點，其可傳回特定區域中可用的完整語音清單。 此外，TTS 現在支援新的區域。 如需詳細資訊，請參閱[文字轉語音 API 參考 (REST)](rest-text-to-speech.md)。

## <a name="try-speech-services"></a>試試語音服務

我們以最受歡迎的程式設計語言提供快速入門，目的是讓您能在 10 分鐘內執行程式碼。 此資料表包含每項功能最受歡迎的快速入門。 您可以使用左側導覽列來瀏覽其他語言及平台。

| 語音轉文字 (SDK) | 文字轉語音 (SDK) | 翻譯 (SDK) |
|----------------------|----------------------|-------------------|
| [C#、.NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [C#、.NET Framework (Windows)](quickstart-text-to-speech-dotnet-windows.md) | [Java (Windows、Linux)](quickstart-translate-speech-java-jre.md) |
| [JavaScript (瀏覽器)](quickstart-js-browser.md) | [C++ (Windows)](quickstart-text-to-speech-cpp-windows.md) | [C#、.NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) |
| [Python (Windows、Linux、macOS)](quickstart-python.md) | [C++ (Linux)](quickstart-text-to-speech-cpp-linux.md) | [C#、.NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) |
| [Java (Windows、Linux)](quickstart-java-jre.md) | | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) |

> [!NOTE]
> 語音轉換文字和文字轉換語音也都具備 REST 端點和相關聯的快速入門。

當您有機會使用語音服務後，請嘗試使用我們的教學課程，其中會教導您如何使用語音 SDK 和 LUIS 從語音辨識意圖。

* [教學課程：使用語音 SDK 和 LUIS 從語音辨識意圖 (C#)](how-to-recognize-intents-from-speech-csharp.md)
* [教學課程：建置 Flask 應用程式以翻譯文字、分析情緒並將翻譯的文字合成語音，REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>取得範例程式碼

每個 Azure 語音服務的範例程式碼皆可在 GitHub 上取得。 這些範例包含常見案例，例如：從檔案或資料流讀取音訊、連續辨識、一次性辨識及使用自訂模型。 使用下列連結來檢視 SDK 和 REST 範例：

* [語音轉文字、文字轉語音及語音翻譯範例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
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
