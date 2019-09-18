---
title: 快速入門：翻譯語音，C# (UWP) - 語音服務
titleSuffix: Azure Cognitive Services
description: 在此快速入門中，您將會建立通用 Windows 平台 (UWP) 應用程式來擷取使用者語音，將它翻譯為另一個語言，然後將文字輸出到命令列。 此指南是專為 Windows 使用者所設計。
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 08/19/2019
ms.author: erhopf
ms.topic: quickstart
ms.openlocfilehash: e513cbbc615965ef196a830351aab8ac241c3f20
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382622"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>快速入門：使用適用於 C# (UWP) 的語音 SDK 來翻譯語音

另備有[語音辨識](quickstart-csharp-uwp.md)、[語音合成](quickstart-text-to-speech-csharp-uwp.md)和[語音優先虛擬助理](quickstart-virtual-assistant-csharp-uwp.md)的快速入門。

在此快速入門中，您將會建立通用 Windows 平台 (UWP) 應用程式以從電腦的麥克風即時擷取使用者語音，翻譯該語音，然後將翻譯的文字轉譯到命令列。 此應用程式是設計來在 64 位元的 Windows 上執行，而且是使用[語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget) \(英文\) 和 Microsoft Visual Studio 2019 來建置。

如需可供進行語音翻譯的完整語言清單，請參閱[語言支援](language-support.md)。

> [!NOTE]
> UWP 可讓您開發應用程式，以在任何支援 Windows 10 的裝置 (包括 PC、Xbox、Surface Hub 與其他裝置) 上執行。

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

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. 在 [方案總管]  中，開啟程式碼後置原始檔 `MainPage.xaml.cs`。 (它分組在 `MainPage.xaml` 底下。)

1. 將其中的所有程式碼取代為下列程式碼片段：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. 在此檔案的 `SpeechTranslationFromMicrophone_ButtonClicked` 處理常式中，尋找 `YourSubscriptionKey` 字串，並以您的訂用帳戶金鑰取代它。

1. 在 `SpeechTranslationFromMicrophone_ButtonClicked` 處理常式中，尋找字串 `YourServiceRegion`，並將它取代為與您的訂用帳戶相關聯的[區域](regions.md)。 (例如，針對免費試用訂用帳戶使用 `westus`。)

1. 從功能表列中，選擇 [檔案]   > [全部儲存]  來儲存變更。

## <a name="build-and-run-the-application"></a>建置並執行應用程式

現在，您可以開始建置和測試應用程式。

1. 從功能表列中，選取 [建置]   > [建置方案]  來建置應用程式。 現在會編譯程式碼，而且不會出現任何錯誤。

1. 選擇 [偵錯]   > [開始偵錯]  (或按 **F5**) 以啟動應用程式。 **helloworld** 視窗會出現。

   ![C# 範例 UWP 翻譯應用程式 - 快速入門](media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. 選取 [啟用麥克風]  ，當存取權限要求出現時，選取 [是]  。

   ![麥克風存取權限要求](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. 選取 [轉譯麥克風輸入的語音]  ，然後對裝置的麥克風說出英文片語或句子。 應用程式會將您的語音傳送到語音服務，語音服務會將語音翻譯為另一個語言的文字 (在此案例中為德文)。 語音服務會將翻譯的文字傳送回應用程式，應用程式會在視窗中顯示翻譯。

   ![語音翻譯使用者介面](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索 GitHub 上的 C# 範例](https://aka.ms/csspeech/samples) \(英文\)

## <a name="see-also"></a>另請參閱

- [讓自訂語音模型定型](how-to-custom-speech-train-model.md)
