---
title: 快速入門：合成語音，C# (UWP) - 語音服務
titleSuffix: Azure Cognitive Services
description: 在此文章中，您會使用認知服務語音 SDK，來建立 C# 通用 Windows 平台 (UWP) 應用程式。 您會從文字合成語音並即時傳至裝置的喇叭。 應用程式是使用語音 SDK NuGet 套件與 Microsoft Visual Studio 2019 所建置的。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 65b65c9af377b6a9951f9f328e0732850d3b9c1d
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382239"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>快速入門：使用語音 SDK 在 UWP 應用程式中合成語音

另有[語音辨識](quickstart-csharp-uwp.md)、[語音翻譯](quickstart-translate-speech-uwp.md)和[語音優先虛擬助理](quickstart-virtual-assistant-csharp-uwp.md)的快速入門。

在此文章中，您會使用認知服務[語音 SDK](speech-sdk.md)，來開發 C# 通用 Windows 平台 (UWP) 應用程式。 此程式會從文字合成語音並即時傳至裝置的喇叭。 您是利用[語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget) \(英文\) 與 Microsoft Visual Studio 2019 (任何版本) 建置應用程式。

> [!NOTE]
> 通用 Windows 平台可讓您開發應用程式，以在任何支援 Windows 10 的裝置 (包括 PC、Xbox、Surface Hub 與其他裝置) 上執行。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。
* 語音服務的 Azure 訂用帳戶金鑰。 [免費取得一個金鑰](get-started.md)。

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>新增範例程式碼

現在，新增可定義應用程式使用者介面的 XAML 程式碼，並新增程式 C# 程式碼後置實作。

1. 在 [方案總管]  中開啟 `MainPage.xaml`。

1. 在設計工具的 XAML 檢視中，將下列 XAML 程式碼片段插入**格線**標記 (在 `<Grid>` 和 `</Grid>` 之間)：

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. 在 [方案總管]  中，開啟程式碼後置原始檔 `MainPage.xaml.cs`。 (它分組在 `MainPage.xaml` 底下。)

1. 將其中的所有程式碼取代為下列程式碼片段：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. 在來源檔案的 `Speak_ButtonClicked` 處理常式中，尋找字串 `YourSubscriptionKey`，並將其取代為與您的訂用帳戶金鑰相關聯的區域。

1. 在 `Speak_ButtonClicked` 處理常式中，尋找字串 `YourServiceRegion`，並將它取代為與您的訂用帳戶相關聯的[區域](regions.md)。 (例如，針對免費試用訂用帳戶使用 `westus`。)

1. 從功能表列中，選擇 [檔案]   > [全部儲存]  來儲存變更。

## <a name="build-and-run-the-application"></a>建置並執行應用程式

現在，您可以開始建置和測試應用程式。

1. 從功能表列中，選擇 [建置]   > [建置方案]  來建置應用程式。 現在會編譯程式碼，而且不會出現任何錯誤。

1. 選擇 [偵錯]   > [開始偵錯]  (或按 **F5**) 以啟動應用程式。 **helloworld** 視窗會出現。

   ![C# 範例 UWP 語音合成應用程式 - 快速入門](media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. 在文字方塊中輸入一些文字，然後按一下 [說出]  。 您的文字會傳送至語音服務並合成為語音，然後以喇叭播放。

    ![語音合成使用者介面](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索 GitHub 上的 C# 範例](https://aka.ms/csspeech/samples) \(英文\)

## <a name="see-also"></a>另請參閱

- [建立和使用自訂語音模型](how-to-custom-voice-create-voice.md)
- [記錄語音範例](record-custom-voice-samples.md)
