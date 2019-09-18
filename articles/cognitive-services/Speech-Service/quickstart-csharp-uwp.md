---
title: 快速入門：辨識語音，C# (UWP) - 語音服務
titleSuffix: Azure Cognitive Services
description: 在此文章中，您會使用認知服務語音 SDK，來建立 C# 通用 Windows 平台 (UWP) 應用程式。 您會將來自裝置麥克風的語音即時轉譯為文字。 應用程式是使用語音 SDK NuGet 套件與 Microsoft Visual Studio 2019 所建置的。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: fe5ff376a7895e2ca5246c0b9eb575752b07c7a1
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382293"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>快速入門：使用語音 SDK 在 UWP 應用程式中辨識語音

另有[語音合成](quickstart-text-to-speech-csharp-uwp.md)、[語音翻譯](quickstart-translate-speech-uwp.md)和[語音優先虛擬助理](quickstart-virtual-assistant-csharp-uwp.md)的快速入門。

您也可以選擇不同的程式設計語言和/或環境：<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

在此文章中，您會使用認知服務[語音 SDK](speech-sdk.md)，來開發 C# 通用 Windows 平台 (UWP) 應用程式。 該程式會從您裝置的麥克風將語音即時轉換成文字。 應用程式是利用[語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget) \(英文\) 與 Microsoft Visual Studio 2019 (任何版本) 所建置的。

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

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. 在 [方案總管]  中，開啟程式碼後置原始檔 `MainPage.xaml.cs`。 (它分組在 `MainPage.xaml` 底下。)

1. 將其中的所有程式碼取代為下列程式碼片段：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. 在來源檔案的 `SpeechRecognitionFromMicrophone_ButtonClicked` 處理常式中，尋找字串 `YourSubscriptionKey`，並將其取代為與您的訂用帳戶金鑰相關聯的區域。

1. 在 `SpeechRecognitionFromMicrophone_ButtonClicked` 處理常式中，尋找字串 `YourServiceRegion`，並將它取代為與您的訂用帳戶相關聯的[區域](regions.md)。 (例如，針對免費試用訂用帳戶使用 `westus`。)

1. 從功能表列中，選擇 [檔案]   > [全部儲存]  來儲存變更。

## <a name="build-and-run-the-application"></a>建置並執行應用程式

現在，您可以開始建置和測試應用程式。

1. 從功能表列中，選擇 [建置]   > [建置方案]  來建置應用程式。 現在會編譯程式碼，而且不會出現任何錯誤。

1. 選擇 [偵錯]   > [開始偵錯]  (或按 **F5**) 以啟動應用程式。 **helloworld** 視窗會出現。

   ![C# 範例 UWP 語音辨識應用程式 - 快速入門](media/sdk/qs-csharp-uwp-helloworld-window.png)

1. 選取 [啟用麥克風]  ，當存取權限要求出現時，選取 [是]  。

   ![麥克風存取權限要求](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. 選取 [使用麥克風輸入的語音辨識]  ，然後對裝置的麥克風說出英文片語或句子。 您的語音會傳送到語音服務，並且轉譯為文字，出現在視窗中。

   ![語音辨識使用者介面](media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索 GitHub 上的 C# 範例](https://aka.ms/csspeech/samples) \(英文\)

## <a name="see-also"></a>另請參閱

- [快速入門：使用適用於 C# (UWP) 的語音 SDK 來翻譯語音](quickstart-translate-speech-uwp.md)
- [讓自訂語音模型定型](how-to-custom-speech-train-model.md)
