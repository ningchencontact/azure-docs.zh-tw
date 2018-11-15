---
title: 快速入門：辨識和轉譯語音，.NET Framework (Windows) - 語音服務
titleSuffix: Azure Cognitive Services
description: 您可以使用本指南，透過 .NET Framework for Windows 和語音 SDK 建立語音轉換文字的主控台應用程式。 完成之後，您可以使用電腦的麥克風將語音即時轉譯為文字。
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/05/2018
ms.author: wolfma
ms.openlocfilehash: 502f59c88808480f26e6ea5a6d2c5b362c78869b
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2018
ms.locfileid: "51683487"
---
# <a name="quickstart-recognize-and-transcribe-speech-using-the-speech-sdk-and-net-framework-windows"></a>快速入門：使用語音 SDK 和 .NET Framework (Windows) 來辨識和轉譯語音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

您可以使用本指南，透過 .NET Framework for Windows 和語音 SDK 建立語音轉換文字的主控台應用程式。 完成之後，您可以使用電腦的麥克風將語音即時轉譯為文字。

本快速入門需要已啟用 Microsoft 語音的 [Azure 認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果您還沒有帳戶，可以使用[免費試用](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started)來取得訂用帳戶金鑰。

## <a name="prerequisites"></a>必要條件

若要完成此專案，您需要：

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* 語音服務的訂用帳戶金鑰
* 能夠存取電腦的麥克風

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

[!INCLUDE [Create project ](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

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

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
此程式碼可從 `quickstart/csharp-dotnet-windows` 資料夾中取得。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用適用於 C# 的語音 SDK 從語音辨識意圖](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>另請參閱

- [轉譯語音](how-to-translate-speech-csharp.md)
- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
