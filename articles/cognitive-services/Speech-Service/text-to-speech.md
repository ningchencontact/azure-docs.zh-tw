---
title: 文字轉換語音與 Azure 的語音服務
titleSuffix: Azure Cognitive Services
description: 文字轉換語音，Azure 語音服務是以 REST 為基礎的服務，可讓應用程式、 工具或裝置，若要將文字轉換成自然的人性合成語音。 從標準和類神經的語音，選擇或建立您自己自訂的語音特有您的產品或品牌。 75 個以上標準語音有 45 個以上的語言和地區設定，和 5 的類神經語音有 4 個語言和地區設定。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 05028704c08ebd06f9b9e4e3f45c5137eb1e6b58
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226907"
---
# <a name="what-is-text-to-speech"></a>文字轉換語音是什麼？

文字轉換語音，Azure 語音服務是以 REST 為基礎的服務，可讓應用程式、 工具或裝置，若要將文字轉換成自然的人性合成語音。 從標準和類神經的語音，選擇或建立您自己[自訂語音](#custom-voice-fonts)特有您的產品或品牌。 75 個以上標準語音有 45 個以上的語言和地區設定，和 5 的類神經語音有 4 個語言和地區設定。 如需完整清單，請參閱 <<c0> [ 支援的語言](language-support.md#text-to-speech)。

文字轉換語音技術可讓內容作者與使用者互動，以不同的方式。 文字轉換語音可以讓使用者能用語音與內容互動的選項，以改善協助工具。 無論使用者有視覺障礙，學習傷殘保險，還是需要瀏覽資訊在駕駛時，文字轉換語音可以改善現有的體驗。 文字轉換語音也是重要的附加元件，如語音 bot 和虛擬的助理。

### <a name="neural-voices"></a>神經語音

神經語音可用來讓與聊天機器人及虛擬小幫手的互動變得更加自然有趣；例如將數位文字 (例如電子書) 轉換成有聲書；以及增強車上導航系統。 人性自然韻律和表明的字數，類神經語音大幅降低接聽疲勞 AI 系統互動時。 如需神經語音的詳細資訊，請參閱[支援的語言](language-support.md#text-to-speech)。

### <a name="custom-voices"></a>自訂語音

語音自訂可讓您建立可辨識、 一個獨特的聲音您的品牌。 若要建立自訂的語音字型，您可以讓 studio 錄製，並上傳做為訓練資料相關聯的指令碼。 此服務會接著建立專為您的錄音調整的獨特語音模型。 您可以使用這個自訂的語音字型合成語音。 如需詳細資訊，請參閱 <<c0> [ 自訂語音](how-to-customize-voice-font.md)。

## <a name="core-features"></a>核心功能

下表列出文字轉換語音的核心功能︰

| 使用案例 | SDK | REST |
|----------|-----|------|
| 文字轉換成語音。 | 否 | 是 |
| 上傳聲音調適的資料集。 | 否 | 是\* |
| 建立和管理語音字型模型。 | 否 | 是\* |
| 建立和管理語音字型部署。 | 否 | 是\* |
| 建立和管理語音字型測試。 | 否 | 是\* |
| 管理訂用帳戶。 | 否 | 是\* |

\* *這些服務可使用 cris.ai 端點。請參閱[Swagger 參考](https://westus.cris.ai/swagger/ui/index)。*

> [!NOTE]
> 文字轉換語音的端點會實作節流會限制為 25，每 5 秒的要求。 當節流發生時，透過訊息標頭就會通知您。

## <a name="get-started-with-text-to-speech"></a>開始使用文字轉換語音

我們提供可讓您在 10 分鐘內執行程式碼的快速入門。 此資料表包含一份依語言的文字轉換語音快速入門。

| 快速入門 | 平台 | API 參考資料 |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows、 macOS、 Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | 視窗、 macOS、 Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) |
| [Python](quickstart-python-text-to-speech.md) | 視窗、 macOS、 Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) |

## <a name="sample-code"></a>範例程式碼

使用 GitHub 上的文字轉換語音程式碼範例。 這些範例涵蓋最受歡迎的程式設計語言中的文字轉換語音轉換。

* [文字轉換語音的範例 (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>參考文件

* [語音 SDK](speech-sdk-reference.md)
* [語音裝置 SDK](speech-devices-sdk.md)
* [REST API：語音轉換文字](rest-speech-to-text.md)
* [REST API：Text-to-speech](rest-text-to-speech.md)
* [REST API：Batch 轉譯和自訂](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>後續步驟

* [取得免費語音服務訂用帳戶](get-started.md)
* [建立自訂語音音調](how-to-customize-voice-font.md)
