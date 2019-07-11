---
title: 快速入門：翻譯語音，C# (UWP) - 語音服務
titleSuffix: Azure Cognitive Services
description: 在此快速入門中，您將會建立簡單的通用 Windows 平台 (UWP) 應用程式來擷取使用者語音，將它翻譯為另一個語言，然後將文字輸出到命令列。 此指南是專為 Windows 使用者設計的。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 108edfeb7bfe24184219e0011f054c36c22c9890
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67602775"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>快速入門：使用適用於 C# (UWP) 的語音 SDK 來翻譯語音

另備有[語音轉文字](quickstart-csharp-uwp.md)、[文字轉語音](quickstart-text-to-speech-csharp-uwp.md)和[語音優先虛擬助理](quickstart-virtual-assistant-csharp-uwp.md)的快速入門。

在此快速入門中，您將會建立簡單的通用 Windows 平台 (UWP) 應用程式以從電腦的麥克風即時擷取使用者語音，翻譯該語音，然後將翻譯的文字轉譯到命令列。 此應用程式是設計來在 64 位元的 Windows 上執行，而且是使用[語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget) \(英文\) 和 Microsoft Visual Studio 2017 來建置。

如需可供進行語音翻譯的完整語言清單，請參閱[語言支援](language-support.md)。

> [!NOTE]
> UWP 可讓您開發應用程式，以在任何支援 Windows 10 的裝置 (包括 PC、Xbox、Surface Hub 與其他裝置) 上執行。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* 適用於語音服務的 Azure 訂用帳戶金鑰。 [免費取得一個金鑰](get-started.md)。

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 應用程式的使用者介面可使用 XAML 來定義。 在方案總管中開啟 `MainPage.xaml`。 在設計工具的 XAML 檢視中，於 `<Grid>` 和 `</Grid>` 之間插入下列 XAML 程式碼片段。

    [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. 開啟程式碼後置原始程式檔 `MainPage.xaml.cs` (它會分組於 `MainPage.xaml` 下)。 將其中的所有程式碼取代為下列內容。

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. 在此檔案的 `SpeechTranslationFromMicrophone_ButtonClicked` 處理常式中，以您的訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 在 `SpeechTranslationFromMicrophone_ButtonClicked` 處理常式中，以和您的訂用帳戶 相關聯的[區域](regions.md) (例如，免費試用訂用帳戶的 `westus`) 取代 `YourServiceRegion` 字串。

1. 儲存專案的所有變更。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

1. 建置應用程式。 從功能表列中，選取 [建置]   >  [建置解決方案]  。 現在會編譯程式碼，而且不會出現任何錯誤。

    ![Visual Studio 應用程式的螢幕擷取畫面，其中 [建置解決方案] 選項已醒目提示](media/sdk/qs-csharp-uwp-08-build.png "成功建置")

1. 啟動應用程式。 從功能表列中，選取 [偵錯]   >  [開始偵錯]  ，或按 **F5**。

    ![Visual Studio 應用程式的螢幕擷取畫面，其中 [開始偵錯] 選項已醒目提示](media/sdk/qs-csharp-uwp-09-start-debugging.png "啟動應用程式並進行偵錯")

1. 此時會出現快顯視窗。 選取 [啟用麥克風]  ，並確認彈出的權限要求。

    ![權限要求的螢幕擷取畫面](media/sdk/qs-csharp-uwp-10-access-prompt.png "啟動應用程式進入偵錯模式")

1. 選取 [使用麥克風輸入的語音辨識]  ，然後對裝置的麥克風說出英文片語或句子。 您的語音會傳送到語音服務，並且轉譯為文字，出現在視窗中。

    ![語音辨識使用者介面的螢幕擷取畫面](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索 GitHub 上的 C# 範例](https://aka.ms/csspeech/samples) \(英文\)

## <a name="see-also"></a>另請參閱

- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
