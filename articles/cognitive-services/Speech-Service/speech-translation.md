---
title: 使用 Azure 語音服務的語音翻譯
titlesuffix: Azure Cognitive Services
description: 語音服務可讓您將端對端、 即時、 多國語言的語音翻譯新增至您的應用程式、 工具和裝置。 相同的 API 可以用於語音轉換語音和語音轉換文字翻譯。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 84d212129c5225fd0efebfca5640cfc3d32e8a40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072403"
---
# <a name="what-is-speech-translation"></a>什麼是語音翻譯？

從 Azure 語音服務，語音翻譯可讓您即時、 多國語言語音-語音及語音轉文字轉譯的音訊串流。 使用語音 SDK，您的應用程式、 工具和裝置可以存取提供音訊來源轉譯及轉譯輸出。 偵測到語音，以及總決賽結果可以轉換成語音合成，則會傳回暫時的轉譯及轉譯結果。

Microsoft 的轉譯引擎由兩個不同的方法： 統計機器翻譯 (SMT) 和類神經機器翻譯 (NMT)。 SMT 使用進階統計分析，來估計最佳的可能轉譯指定內容的幾個字。 使用 NMT，類神經網路會利用藉由使用轉譯文字的句子的完整內容，提供更精確、 更自然發音的翻譯。

現在，Microsoft 會使用 NMT 最受歡迎的語言的翻譯。 所有[可用於語音轉換語音翻譯的語言](language-support.md#speech-translation)都是採用 NMT 技術。 根據語言配對，語音轉換文字翻譯可能會使用 SMT 或 NMT。 當 NMT 支援目標語言時，完整的譯文是 NMT 架構。 目標語言不支援 NMT，轉譯時 NMT 和 SMT，使用英文為 「 樞紐 」 兩種語言之間的混合式。

## <a name="core-features"></a>核心功能

以下是功能可透過語音 SDK 和 REST Api:

| 使用案例 | SDK | REST |
|----------|-----|------|
| 與辨識結果的語音轉換文字翻譯。 | 是 | 否 |
| 語音-語音翻譯。 | 是 | 否 |
| 暫時的辨識及轉譯結果。 | 是 | 否 |

## <a name="get-started-with-speech-translation"></a>開始使用語音翻譯

我們提供可讓您在 10 分鐘內執行程式碼的快速入門。 此資料表包含一份依語言的語音轉譯快速入門。

| 快速入門 | 平台 | API 參考資料 |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-translate-speech-dotnetcore-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#.NET framework](quickstart-translate-speech-dotnetframework-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-translate-speech-uwp.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-translate-speech-cpp-windows.md) | Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [Java](quickstart-translate-speech-java-jre.md) | Windows、Linux、macOS | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>範例程式碼

使用 GitHub 上適用於語音 SDK 的範例程式碼。 這些範例涵蓋常見的案例，例如讀取音訊檔案或資料流，連續和一次性辨識/轉譯和使用自訂的模型。

* [語音轉換文字和轉譯範例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>移轉指南

> [!WARNING]
> Translator Speech 晚會於 2019 年 10 月 15 日。

如果您的應用程式、 工具或產品使用 Translator Speech，我們建立了指南，以協助您移轉至語音服務。

* [Translator Speech API 從移轉到語音服務](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>參考文件

* [語音 SDK](speech-sdk-reference.md)
* [語音裝置 SDK](speech-devices-sdk.md)
* [REST API：語音轉文字](rest-speech-to-text.md)
* [REST API：文字轉語音](rest-text-to-speech.md)
* [REST API：批次轉譯與自訂](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
* [取得語音 SDK](speech-sdk.md)
