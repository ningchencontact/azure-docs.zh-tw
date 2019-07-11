---
title: 快速入門：合成語音，C# (.NET Core) - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 SDK 在 Windows 上以 .NET Core 下的 C# 合成語音
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 7b4a018e38ca625e38dc1658a95d3ce0e677f711
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467294"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-core"></a>快速入門：使用適用於 .NET Core 的語音 SDK 來合成語音

另備有[語音辨識](quickstart-csharp-dotnetcore-windows.md)和[語音翻譯](quickstart-translate-speech-dotnetcore-windows.md)的快速入門。

在本文中，您會透過使用認知服務[語音 SDK](speech-sdk.md)，在 Windows 上的 .NET Core 建立 C# 主控台應用程式。 您會從文字合成語音並即時傳至電腦喇叭。 應用程式是利用[語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget)與 Microsoft Visual Studio 2017 (任何版本) 所建置的。

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

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnetcore/helloworld/Program.cs#code)]

1. 在相同檔案中，以您的訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 同時以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](regions.md)取代 `YourServiceRegion` 字串。

1. 儲存專案的變更。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

1. 建置應用程式。 從功能表列中，選擇 [建置]   > [建置解決方案]  。 這應該會編譯程式碼，而且不會出現任何錯誤。

    ![Visual Studio 應用程式的螢幕擷取畫面，其中 [建置解決方案] 選項已醒目提示](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "成功建置")

1. 啟動應用程式。 從功能表列中，選擇 [偵錯]   > [開始偵錯]  ，或按 **F5**。

    ![Visual Studio 應用程式的螢幕擷取畫面，其中 [開始偵錯] 選項已醒目提示](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "啟動應用程式並進行偵錯")

1. 主控台視窗隨即顯示，提示您輸入某些文字。 請輸入幾個字或句子。 您輸入的文字會傳送至語音服務並合成為語音，然後以喇叭播放。

    ![成功合成後主控台輸出的螢幕擷取畫面](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "成功合成後的主控台輸出")

## <a name="next-steps"></a>後續步驟

在 GitHub 上可取得其他範例，例如如何將語音合成至音訊檔案。

> [!div class="nextstepaction"]
> [探索 GitHub 上的 C# 範例](https://aka.ms/csspeech/samples) \(英文\)

## <a name="see-also"></a>另請參閱

- [自訂語音字型](how-to-customize-voice-font.md)
- [記錄語音範例](record-custom-voice-samples.md)
