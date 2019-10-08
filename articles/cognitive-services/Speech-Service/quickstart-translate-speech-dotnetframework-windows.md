---
title: 快速入門：翻譯語音，C# (.NET Framework Windows) - 語音服務
titleSuffix: Azure Cognitive Services
description: 在此快速入門中，您將會建立 .NET Framework 應用程式來擷取使用者語音，將它翻譯為另一個語言，然後將文字輸出到命令列。 此指南是專為 Windows 使用者所設計。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: erhopf
ms.openlocfilehash: bb710a3e3adb13aa3999c13043c8bb93f6b885f1
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327351"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-framework-windows"></a>快速入門：使用適用於 .NET Framework 的語音 SDK 來翻譯語音 (Windows)

[語音辨識](quickstart-csharp-dotnet-windows.md)和[語音合成](quickstart-text-to-speech-dotnet-windows.md)也提供快速入門。

在此快速入門中，您將會建立 .NET Framework 應用程式以從電腦的麥克風即時擷取使用者語音，翻譯該語音，然後將翻譯的文字轉譯到命令列。 此應用程式會在 32 位元或 64 位元的 Windows 上執行，而且是使用[語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget)和 Microsoft Visual Studio 2019 來建置。

如需可供進行語音翻譯的完整語言清單，請參閱[語言支援](language-support.md)。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。
* 語音服務的 Azure 訂用帳戶金鑰。 [免費取得一個金鑰](get-started.md)。

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 開啟 **Program.cs**，並以下列程式碼取代其中的所有程式碼。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. 尋找字串 `YourSubscriptionKey`，然後將其取代為您的訂用帳戶金鑰。

1. 尋找字串 `YourServiceRegion`，並將它取代為與您的訂用帳戶相關聯的[區域](regions.md)。 例如，如果您使用免費試用訂用帳戶，則區域為 `westus`。

1. 從功能表列中，選擇 [檔案]   > [全部儲存]  。

## <a name="build-and-run-the-application"></a>建置並執行應用程式

1. 從功能表列中，選擇 [建置]   > [建置方案]  來建置應用程式。 現在會編譯程式碼，而且不會出現任何錯誤。

1. 選擇 [偵錯]   > [開始偵錯]  (或選取 **F5**) 以啟動 **helloworld** 應用程式。

1. 對裝置的麥克風說出英文片語或句子。 應用程式會將您的語音傳送到語音服務，語音服務會將語音翻譯為另一個語言的文字 (在此案例中為德文)。 語音服務會將翻譯的文字傳送回應用程式，應用程式會在視窗中顯示翻譯。

   ![語音翻譯使用者介面](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png)

## <a name="next-steps"></a>後續步驟

在 GitHub 上可取得其他範例，例如如何讀取音訊檔案中的語音，以及將翻譯的文字輸出為合成語音。

> [!div class="nextstepaction"]
> [探索 GitHub 上的 C# 範例](https://aka.ms/csspeech/samples) \(英文\)

## <a name="see-also"></a>另請參閱

- [讓自訂語音模型定型](how-to-custom-speech-train-model.md)
