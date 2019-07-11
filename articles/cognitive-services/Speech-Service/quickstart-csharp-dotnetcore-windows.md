---
title: 快速入門：辨識語音，C# (.NET Core) - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 SDK 在 Windows 或 macOS 上以 .NET Core 下的 C# 辨識語音
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 9c445ea685518cec77089ce644a79305ff7f3e9e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606593"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-core"></a>快速入門：使用適用於 .NET Core 的語音 SDK 來辨識語音

另備有[文字轉換語音](quickstart-text-to-speech-dotnetcore.md)和[語音翻譯](quickstart-translate-speech-dotnetcore-windows.md)的快速入門。

如有需要，請選擇不同的程式設計語言和/或環境：<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

在本文中，您會透過使用認知服務[語音 SDK](speech-sdk.md)，在 Windows 或 macOS 上的 .NET Core 建立 C# 主控台應用程式。 您將來自電腦麥克風的語音即時轉譯為文字。 應用程式是利用[語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget)與 Microsoft Visual Studio 2017 (任何版本) 所建置的。

> [!NOTE]
> .NET Core 是實作[.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) 規格的開放原始碼、跨平台 .NET 平台。

您需要語音服務訂用帳戶金鑰，才能完成本快速入門。 您可以免費取得一個金鑰。 如需詳細資訊，請參閱[免費試用語音服務](get-started.md)。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* 適用於語音服務的 Azure 訂用帳戶金鑰。 [免費取得一個金鑰](get-started.md)。

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 開啟 `Program.cs` 並將其中的所有程式碼取代為下列程式碼。

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore/helloworld/Program.cs#code)]

1. 在相同檔案中，以您的訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 同時以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](regions.md)取代 `YourServiceRegion` 字串。

1. 儲存專案的變更。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

1. 建置應用程式。 從功能表列中，選擇 [建置]   > [建置解決方案]  。 這應該會編譯程式碼，而且不會出現任何錯誤。

    ![Visual Studio 應用程式的螢幕擷取畫面，其中 [建置解決方案] 選項已醒目提示](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "成功建置")

1. 啟動應用程式。 從功能表列中，選擇 [偵錯]   > [開始偵錯]  ，或按 **F5**。

    ![Visual Studio 應用程式的螢幕擷取畫面，其中 [開始偵錯] 選項已醒目提示](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "啟動應用程式並進行偵錯")

1. 一個主控台視窗隨即顯示，提示您說話。 請說英文片語或句子。 您的語音會傳送到語音服務，並且轉譯為文字，該文字會出現在相同視窗中。

    ![成功辨識後主控台輸出的螢幕擷取畫面](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "成功辨識後的主控台輸出")

## <a name="next-steps"></a>後續步驟

在 GitHub 上可取得其他範例，例如如何讀取音訊檔案中的語音。

> [!div class="nextstepaction"]
> [瀏覽 GitHub 上的 C# 範例](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>另請參閱

- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
