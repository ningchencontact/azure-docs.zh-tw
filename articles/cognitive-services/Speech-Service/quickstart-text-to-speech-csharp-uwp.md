---
title: 快速入門：合成語音，C# (UWP) - 語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，您會使用認知服務語音 SDK，來建立 C# 通用 Windows 平台 (UWP) 應用程式。 您會從文字合成語音並即時傳至裝置的喇叭。 應用程式是使用語音 SDK NuGet 套件與 Microsoft Visual Studio 2017 所建置的。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 5e1f8aea1b00cbba7fec6c7ca416a965458ab526
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607748"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>快速入門：使用語音 SDK 在 UWP 應用程式中合成語音

另備有[語音辨識](quickstart-csharp-uwp.md)、[語音翻譯](quickstart-translate-speech-uwp.md)和[語音優先虛擬助理](quickstart-virtual-assistant-csharp-uwp.md)的快速入門。

在本文中，您會使用認知服務[語音 SDK](speech-sdk.md)，來建立 C# 通用 Windows 平台 (UWP；Windows 1709 版或更新版本) 應用程式。 此程式會從文字合成語音並即時傳至裝置的喇叭。 應用程式是利用[語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget)與 Microsoft Visual Studio 2017 或更新版本 (任何版本) 所建置的。

> [!NOTE]
> 通用 Windows 平台可讓您開發應用程式，以在任何支援 Windows 10 的裝置 (包括 PC、Xbox、Surface Hub 與其他裝置) 上執行。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 或更新版本
* 語音服務的 Azure 訂用帳戶金鑰。 [免費取得一個金鑰](get-started.md)。

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 應用程式的使用者介面可使用 XAML 來定義。 在方案總管中開啟 `MainPage.xaml`。 在設計工具的 XAML 檢視中，將下列 XAML 程式碼片段插入格線標記 (在 `<Grid>` 和 `</Grid>` 之間)。

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. 開啟程式碼後置原始程式檔 `MainPage.xaml.cs` (它會分組於 `MainPage.xaml` 下)。 將其中的所有程式碼取代為下列內容。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. 在此檔案的 `Speak_ButtonClicked` 處理常式中，以您的訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 在 `Speak_ButtonClicked` 處理常式中，以和您的訂用帳戶 相關聯的[區域](regions.md) (例如，免費試用訂用帳戶的 `westus`) 取代 `YourServiceRegion` 字串。

1. 儲存專案的所有變更。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

1. 建置應用程式。 從功能表列中，選取 [建置]   >  [建置解決方案]  。 現在會編譯程式碼，而且不會出現任何錯誤。

    ![Visual Studio 應用程式的螢幕擷取畫面，其中 [建置解決方案] 選項已醒目提示](media/sdk/qs-csharp-uwp-08-build.png "成功建置")

1. 啟動應用程式。 從功能表列中，選取 [偵錯]   >  [開始偵錯]  ，或按 **F5**。

    ![Visual Studio 應用程式的螢幕擷取畫面，其中 [開始偵錯] 選項已醒目提示](media/sdk/qs-csharp-uwp-09-start-debugging.png "啟動應用程式並進行偵錯")

1. 在文字方塊中輸入一些文字，然後按一下 [說出]  。 您的文字會傳送至語音服務並合成為語音，然後以喇叭播放。

    ![語音合成使用者介面的螢幕擷取畫面](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索 GitHub 上的 C# 範例](https://aka.ms/csspeech/samples) \(英文\)

## <a name="see-also"></a>另請參閱

- [自訂語音字型](how-to-customize-voice-font.md)
- [記錄語音範例](record-custom-voice-samples.md)
