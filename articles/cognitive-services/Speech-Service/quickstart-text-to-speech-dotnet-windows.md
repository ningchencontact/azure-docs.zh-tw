---
title: 快速入門：合成語音，.NET Framework (Windows) - 語音服務
titleSuffix: Azure Cognitive Services
description: 您可以使用本指南，透過 .NET Framework for Windows 和語音 SDK 建立文字轉語音的主控台應用程式。 完成後，您可以從文字合成語音，並即時以喇叭聽取語音。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 4/03/2019
ms.author: yinhew
ms.openlocfilehash: a013189e45b1c1c8eeb88d62a718d495c0c415a2
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012489"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>快速入門：使用適用於 .NET Framework (Windows) 的語音 SDK 來合成語音

您可以使用本指南，透過 .NET Framework for Windows 和語音 SDK 建立文字轉語音的主控台應用程式。 完成後，您可以從文字合成語音，並即時以喇叭聽取語音。

如需快速示範 (不自行建置 Visual Studio 專案，如下所示)：

從 GitHub 取得最新的[認知服務語音 SDK 範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk)。

## <a name="prerequisites"></a>必要條件

若要完成此專案，您需要：

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* 適用於語音服務的訂用帳戶金鑰。 [免費取得一個金鑰](get-started.md)。
* 可用的喇叭 (或耳機)。

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 開啟 `Program.cs`，並將自動產生的程式碼取代下列範例：

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. 找出字串 `YourSubscriptionKey` 並將其取代為您的語音服務訂用帳戶金鑰。

1. 找出字串 `YourServiceRegion` 並將其取代為與您的訂用帳戶相關聯的[區域](regions.md)。 例如，如果您使用的是免費試用版，區域將是 `westus`。

1. 儲存專案的變更。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

1. 從功能表列中，選取 [建置]  >  [建置解決方案]。 現在會編譯程式碼，而且不會出現任何錯誤。

    ![Visual Studio 應用程式的螢幕擷取畫面，其中 [建置解決方案] 選項已醒目提示](media/sdk/qs-csharp-dotnet-windows-08-build.png "成功建置")

1. 從功能表列中選取 [偵錯] > [開始偵錯]，或按 **F5** 以啟動應用程式。

    ![Visual Studio 應用程式的螢幕擷取畫面，其中 [開始偵錯] 選項已醒目提示](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "啟動應用程式並進行偵錯")

1. 此時會出現一個主控台視窗，提示您輸入某些文字。 請輸入幾個字或句子。 您輸入的文字會傳送至語音服務並合成為語音，然後以喇叭播放。

    ![成功辨識後主控台輸出的螢幕擷取畫面](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "成功辨識後的主控台輸出")

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索 GitHub 上的 C# 範例](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>另請參閱

- [自訂語音字型](how-to-customize-voice-font.md)
- [記錄語音範例](record-custom-voice-samples.md)
