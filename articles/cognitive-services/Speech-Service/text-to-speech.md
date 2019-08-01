---
title: 文字轉換語音-語音服務
titleSuffix: Azure Cognitive Services
description: 語音服務中的文字轉換語音功能是一項功能, 可讓您的應用程式、工具或裝置將文字轉換成自然的人類合成語音。 從標準和類神經語音中選擇, 或為您的產品或品牌建立專屬的自訂語音。 75 + standard 語音提供45以上的語言和地區設定, 而5類神經語音則提供4種語言和地區設定。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: erhopf
ms.openlocfilehash: 3d2c3e2bf693f763db13d90961a31e871aa25235
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558868"
---
# <a name="what-is-text-to-speech"></a>什麼是文字轉換語音？

來自 Azure 語音服務的文字轉換語音是一種服務, 可讓您的應用程式、工具或裝置將文字轉換成自然的人類合成語音。 從標準和類神經語音中選擇, 或為您的產品或品牌建立專屬的自訂語音。 75 + standard 語音提供45以上的語言和地區設定, 而5類神經語音則提供4種語言和地區設定。 如需完整清單, 請參閱[支援的語言](language-support.md#text-to-speech)。

文字轉換語音技術可讓內容建立者以不同的方式與使用者互動。 文字轉換語音可以提供使用者與 content 語音互動的選項, 以改善協助工具。 無論使用者是否有視覺障礙、學習障礙, 或需要流覽資訊, 同時進行文字轉換語音, 都可以改善現有的體驗。 文字轉換語音也是語音 bot 和虛擬助理的重要附加元件。


藉由使用語音合成標記語言 (SSML) (以 XML 為基礎的標記語言), 使用文字轉換語音服務的開發人員可以指定將輸入文字轉換成合成語音的方式。 透過 SSML, 您可以調整音調、發音、說話速度、音量等等。 如需詳細資訊, 請參閱[SSML](#speech-synthesis-markup-language-ssml)。

### <a name="standard-voices"></a>標準語音

標準語音是使用統計參數合成和/或串連合成技術所建立。 這些聲音具有高度可理解和自然的音效。 您可以輕鬆地讓您的應用程式以多種語音選項, 在45以上的語言中說話。 這些聲音提供高發音精確度, 包括縮寫、縮寫擴充、日期/時間解讀、polyphones 等等的支援。 使用標準語音, 讓使用者能夠與您的內容語音互動, 以改善應用程式和服務的協助工具。

### <a name="neural-voices"></a>神經語音

神經語音使用深度類神經網路, 以克服傳統文字轉換語音系統的限制, 以符合語音的壓力和聲調模式, 以及將語音的單位合成為電腦語音。 標準文字轉換語音會將韻律細分成獨立模型所管理的個別語言分析和聲場預測步驟, 這可能會導致 muffled 的語音合成。 我們的類神經功能會同時韻律預測和語音合成, 這會導致更流暢且自然發音的語音。

神經語音可用來讓與聊天機器人及虛擬小幫手的互動變得更加自然有趣；例如將數位文字 (例如電子書) 轉換成有聲書；以及增強車上導航系統。 有了人類的自然韻律和清楚的單字文字清晰度, 神經語音會在您與 AI 系統互動時, 大幅減少聆聽的疲勞。

神經語音支援不同的樣式, 例如中性和 cheerful。 例如, Jessa (en-us) voice 可以說話 cheerfully, 這是針對暖、快樂的交談而優化的。 您可以使用[語音合成標記語言](speech-synthesis-markup.md)來調整語音輸出, 例如音調、音調和速度。 如需可用語音的完整清單, 請參閱[支援的語言](language-support.md#text-to-speech)。

若要深入瞭解神經語音的優點, 請參閱[Microsoft 的新類神經文字轉換語音服務可協助機器說話](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/)。

### <a name="custom-voices"></a>自訂語音

語音自訂可讓您為品牌建立可辨識的一種語音。 若要建立自訂語音字型, 您可以製作 studio 錄製, 並上傳相關聯的腳本做為定型資料。 此服務會接著建立專為您的錄音調整的獨特語音模型。 您可以使用此自訂語音字型來合成語音。 如需詳細資訊, 請參閱[自訂語音](how-to-customize-voice-font.md)。

## <a name="speech-synthesis-markup-language-ssml"></a>語音合成標記語言 (SSML)

語音合成標記語言 (SSML) 是以 XML 為基礎的標記語言, 可讓開發人員指定如何使用文字轉換語音服務, 將輸入文字轉換成合成語音。 相較于純文字, SSML 讓開發人員能夠微調文字到語音轉換輸出的音調、發音、說話速度、音量和更多。 一般標點符號 (例如在句號之後暫停), 或使用正確的聲調 (當句子以問號結尾時) 會自動處理。

所有傳送至文字轉換語音服務的文字輸入, 都必須結構化為 SSML。 如需詳細資訊, 請參閱[語音合成標記語言](speech-synthesis-markup.md)。

### <a name="pricing-note"></a>定價注意事項

使用文字轉換語音服務時, 會向您收取轉換成語音的每個字元的費用, 包括標點符號。 雖然 SSML 檔本身無法計費, 但用來調整文字轉換成語音的選擇性元素 (例如音素和音調) 會視為計費字元。 以下是可計費的清單:

* 在要求的 SSML 主體中傳遞至文字轉換語音服務的文字
* 在要求本文中, 以 SSML 格式的文字欄位內的所有標記, 但`<speak>`和`<voice>`標記除外
* 字母、標點符號、空格、定位字元、標記和所有空白字元
* 以 Unicode 定義的每個字碼指標

如需詳細資訊, 請參閱[定價](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

> [!IMPORTANT]
> 每個中文、日文和韓文語言字元會視為兩個要計費的字元。

## <a name="core-features"></a>核心功能

下表列出文字轉換語音的核心功能:

| 使用案例 | SDK | REST |
|----------|-----|------|
| 將文字轉換成語音。 | 是 | 是 |
| 上傳資料集以進行語音調適。 | 否 | 是\* |
| 建立和管理語音字型模型。 | 否 | 是\* |
| 建立和管理語音字型部署。 | 否 | 是\* |
| 建立和管理語音字型測試。 | 否 | 是\* |
| 管理訂閱。 | 否 | 是\* |

\**這些服務可以使用 cris.ai 端點來取得。請參閱[Swagger 參考](https://westus.cris.ai/swagger/ui/index)。這些自訂語音訓練和管理 Api 會執行節流, 將要求限制為每5秒25個, 而語音合成 API 本身則會實行允許每秒200個要求最高的節流。進行節流時, 您會收到訊息標頭的通知。*

## <a name="get-started-with-text-to-speech"></a>開始使用文字轉換語音

我們提供的快速入門是為了讓您在10分鐘內執行程式碼而設計的。 下表包含依語言組織的文字轉換語音快速入門清單。

### <a name="sdk-quickstarts"></a>SDK 快速入門

| 快速入門 (SDK) | 平台 | API 參考 |
|------------|----------|---------------|
| [C#、.NET Core](quickstart-text-to-speech-dotnetcore.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-text-to-speech-csharp-uwp.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#、Unity](quickstart-text-to-speech-csharp-unity.md) | Windows、Android | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Browse](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Browse](https://aka.ms/csspeech/cppref) |

### <a name="rest-quickstarts"></a>REST 快速入門

| 快速入門 (REST) | 平台 | API 參考 |
|------------|----------|---------------|
| [C#、.NET Core](quickstart-dotnet-text-to-speech.md) | Windows、macOS、Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Windows、macOS、Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Windows、macOS、Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>範例程式碼

您可以在 GitHub 上取得文字轉換語音的範例程式碼。 這些範例涵蓋了最受歡迎的程式設計語言的文字轉換語音。

* [文字轉換語音範例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [文字轉語音範例 (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>參考文件

* [語音 SDK](speech-sdk-reference.md)
* [語音裝置 SDK](speech-devices-sdk.md)
* [REST API：語音轉文字](rest-speech-to-text.md)
* [REST API：文字轉語音](rest-text-to-speech.md)
* [REST API：批次轉譯與自訂](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>後續步驟

* [取得免費語音服務訂用帳戶](get-started.md)
* [建立自訂語音音調](how-to-customize-voice-font.md)
