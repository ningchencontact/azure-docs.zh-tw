---
title: 快速入門：辨識語音，.NET Framework (Windows) - 語音服務
titleSuffix: Azure Cognitive Services
description: 您可以使用本指南，透過 .NET Framework for Windows 和語音 SDK 建立語音轉換文字的主控台應用程式。 完成之後，您可以使用電腦的麥克風將語音即時轉譯為文字。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: wolfma
ms.openlocfilehash: 2f190ccbead9e6349543d04e2419f458888fba2c
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2019
ms.locfileid: "59008742"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>快速入門：使用適用於 .NET Framework (Windows) 的語音 SDK 來辨識語音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

您可以使用本指南，透過 .NET Framework for Windows 和語音 SDK 建立語音轉換文字的主控台應用程式。 完成之後，您可以使用電腦的麥克風將語音即時轉譯為文字。

如需快速示範 (不自行建置 Visual Studio 專案，如下所示)：

從 GitHub 取得最新的[認知服務語音 SDK 範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk)。

## <a name="prerequisites"></a>必要條件

若要完成此專案，您需要：

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* 適用於語音服務的訂用帳戶金鑰。 [免費取得一個金鑰](get-started.md)。
* 能夠存取電腦的麥克風

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 開啟 `Program.cs`，並將自動產生的程式碼取代下列範例：

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. 找出字串 `YourSubscriptionKey` 並將其取代為您的語音服務訂用帳戶金鑰。

1. 找出字串 `YourServiceRegion` 並將其取代為與您的訂用帳戶相關聯的[區域](regions.md)。 例如，如果您使用的是免費試用版，區域將是 `westus`。

1. 儲存專案的變更。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

1. 從功能表列中，選取 [建置]  >  [建置解決方案]。 現在會編譯程式碼，而且不會出現任何錯誤。

    ![Visual Studio 應用程式的螢幕擷取畫面，其中 [建置解決方案] 選項已醒目提示](media/sdk/qs-csharp-dotnet-windows-08-build.png "成功建置")

1. 從功能表列中選取 [偵錯] > [開始偵錯]，或按 **F5** 以啟動應用程式。

    ![Visual Studio 應用程式的螢幕擷取畫面，其中 [開始偵錯] 選項已醒目提示](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "啟動應用程式並進行偵錯")

1. 此時會出現一個主控台視窗，提示您開始說話。 現在，請說一些英文。 您的語音會傳送到語音服務，並即時轉譯為文字。 結果會印出到主控台。

    ![成功辨識後主控台輸出的螢幕擷取畫面](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "成功辨識後的主控台輸出")

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索 GitHub 上的 C# 範例](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>另請參閱

- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
