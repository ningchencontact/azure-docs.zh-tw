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
ms.date: 08/28/2019
ms.author: wolfma
ms.openlocfilehash: cb140647394858fbc0a9a00ea125365d5b7a08d5
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327065"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>快速入門：使用適用於 .NET Framework (Windows) 的語音 SDK 來辨識語音

[語音合成](quickstart-text-to-speech-dotnet-windows.md)和[語音翻譯](quickstart-translate-speech-dotnetframework-windows.md)也提供快速入門。

您也可以選擇不同的程式設計語言和/或環境：<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

您可以使用本指南，透過 .NET Framework for Windows 和語音 SDK 建立語音轉換文字的主控台應用程式。 完成之後，您可以使用電腦的麥克風將語音即時轉譯為文字。

如需快速示範 (而非如本文所述，自行建立 Visual Studio 專案)，請從 GitHub 取得最新的[認知服務語音 SDK 範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk)。

## <a name="prerequisites"></a>必要條件

若要完成此專案，您需要：

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。
* 適用於語音服務的訂用帳戶金鑰。 [免費取得一個金鑰](get-started.md)。
* 能夠存取電腦的麥克風。

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 開啟 **Program.cs**，並以此範例取代自動產生的程式碼：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. 尋找字串 `YourSubscriptionKey`，然後將其取代為您的語音服務訂用帳戶金鑰。

1. 尋找字串 `YourServiceRegion`，並將它取代為與您的訂用帳戶相關聯的[區域](regions.md)。 例如，如果您使用免費試用訂用帳戶，則區域為 `westus`。

1. 從功能表列中，選擇 [檔案]   > [全部儲存]  。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

1. 從功能表列中，選擇 [建置]   > [建置方案]  來建置應用程式。 現在會編譯程式碼，而且不會出現任何錯誤。

1. 選擇 [偵錯]   > [開始偵錯]  (或選取 **F5**) 以啟動 **helloworld** 應用程式。

1. 對裝置的麥克風說出英文片語或句子。 應用程式會將您的語音傳送到語音服務，該服務會將轉換的文字傳送回應用程式以供顯示。

   ![語音辨識使用者介面](media/sdk/qs-csharp-dotnet-windows-10-console-output.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索 GitHub 上的 C# 範例](https://aka.ms/csspeech/samples) \(英文\)

## <a name="see-also"></a>另請參閱

- [讓自訂語音模型定型](how-to-custom-speech-train-model.md)
