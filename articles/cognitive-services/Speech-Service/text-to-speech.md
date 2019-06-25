---
title: 文字轉換語音與 Azure 的語音服務
titleSuffix: Azure Cognitive Services
description: 文字轉換語音，Azure 語音服務是一項服務，可讓應用程式、 工具或裝置，若要將文字轉換成自然的人性合成語音。 從標準和類神經的語音，選擇或建立您自己自訂的語音特有您的產品或品牌。 75 個以上標準語音有 45 個以上的語言和地區設定，和 5 的類神經語音有 4 個語言和地區設定。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: cbabdde2dd5df2ca40ee45fe0f7b0437f095673b
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020371"
---
# <a name="what-is-text-to-speech"></a>文字轉換語音是什麼？

文字轉換語音，Azure 語音服務是一項服務，可讓應用程式、 工具或裝置，若要將文字轉換成自然的人性合成語音。 從標準和類神經的語音，選擇或建立您自己自訂的語音特有您的產品或品牌。 75 個以上標準語音有 45 個以上的語言和地區設定，和 5 的類神經語音有 4 個語言和地區設定。 如需完整清單，請參閱 <<c0> [ 支援的語言](language-support.md#text-to-speech)。

文字轉換語音技術可讓內容作者與使用者互動，以不同的方式。 文字轉換語音可以讓使用者能用語音與內容互動的選項，以改善協助工具。 無論使用者有視覺障礙，學習傷殘保險，還是需要瀏覽資訊在駕駛時，文字轉換語音可以改善現有的體驗。 文字轉換語音也是重要的附加元件，如語音 bot 和虛擬的助理。

### <a name="standard-voices"></a>標準語音

標準的語音會建立使用統計的參數式合成及/或串連合成的技術。 這些語音高容易理解，聽起來很自然。 您可以輕鬆地啟用您的應用程式，說出超過 45 國語言，使用各種不同的語音選項。 這些語音提供高發音精確度，包括支援的縮寫、 首字母縮略字擴充、 日期/時間的方式解讀、 polyphones，和更多功能。 若要改善您的應用程式和服務的協助工具可讓使用者與內容互動的輪流使用標準的聲音。

### <a name="neural-voices"></a>神經語音

類神經的語音使用深度類神經網路來克服在比對的壓力和口說語言中，在和中由單位的語音合成到電腦聲音的聲調模式中的傳統文字轉換語音系統的限制。 標準的文字轉換語音細分成個別的語言分析和獨立的模型所控管的原音預測步驟的韻律。 會導致 muffled、 buzzy 語音合成。 我們的類神經功能會韻律預測和語音合成同時，這會導致更流暢且自然發音的語音。

神經語音可用來讓與聊天機器人及虛擬小幫手的互動變得更加自然有趣；例如將數位文字 (例如電子書) 轉換成有聲書；以及增強車上導航系統。 人性自然韻律和表明的字數，類神經語音大幅降低接聽疲勞 AI 系統互動時。 

類神經的語音支援不同的樣式，例如中性和聽起來。 比方說，傑 (EN-US) 的語音可以唸出 cheerfully，其中最適合用於暖、 快樂的交談。 您可以調整語音輸出，例如音，音調、 並加快速度使用[語音合成標記語言](speech-synthesis-markup.md)。 可用的語音的完整清單，請參閱 <<c0> [ 支援的語言](language-support.md#text-to-speech)。

若要深入了解類神經的語音的優點，請參閱[Microsoft 的新的類神經文字轉換語音服務可協助機器等人說](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/)。

### <a name="custom-voices"></a>自訂語音

語音自訂可讓您建立可辨識、 一個獨特的聲音您的品牌。 若要建立自訂的語音字型，您可以讓 studio 錄製，並上傳做為訓練資料相關聯的指令碼。 此服務會接著建立專為您的錄音調整的獨特語音模型。 您可以使用這個自訂的語音字型合成語音。 如需詳細資訊，請參閱 <<c0> [ 自訂語音](how-to-customize-voice-font.md)。

## <a name="core-features"></a>核心功能

下表列出文字轉換語音的核心功能︰

| 使用案例 | SDK | REST |
|----------|-----|------|
| 文字轉換成語音。 | 是 | 是 |
| 上傳聲音調適的資料集。 | 否 | 是\* |
| 建立和管理語音字型模型。 | 否 | 是\* |
| 建立和管理語音字型部署。 | 否 | 是\* |
| 建立和管理語音字型測試。 | 否 | 是\* |
| 管理訂用帳戶。 | 否 | 是\* |

\* *這些服務可使用 cris.ai 端點。請參閱[Swagger 參考](https://westus.cris.ai/swagger/ui/index)。這些自訂的語氣訓練課程和 Api 管理會實作節流，限制為 5 秒，每 25 的要求而語音合成 API 本身會實作節流，允許以最高每秒 200 個要求。當節流發生時，透過訊息標頭就會通知您。*

## <a name="get-started-with-text-to-speech"></a>開始使用文字轉換語音

我們提供可讓您在 10 分鐘內執行程式碼的快速入門。 此資料表包含一份依語言的文字轉換語音快速入門。

### <a name="sdk-quickstarts"></a>SDK 快速入門

| 快速入門 (SDK) | 平台 | API 參考資料 |
|------------|----------|---------------|
| [C#.NET framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Browse](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Browse](https://aka.ms/csspeech/cppref) |

### <a name="rest-quickstarts"></a>REST 快速入門

| 快速入門 (REST) | 平台 | API 參考資料 |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows、 macOS、 Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Windows、macOS、Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Windows、macOS、Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>範例程式碼

使用 GitHub 上的文字轉換語音程式碼範例。 這些範例涵蓋最受歡迎的程式設計語言中的文字轉換語音轉換。

* [文字轉換語音的範例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
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
