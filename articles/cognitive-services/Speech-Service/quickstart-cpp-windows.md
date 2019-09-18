---
title: 快速入門：辨識語音，C++ (Windows) - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 SDK 在 Windows 桌面上以 C++ 辨識語音
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: wolfma
ms.openlocfilehash: a9c43e1d27a396a2c3e9123ce5ce538296c6870c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/05/2019
ms.locfileid: "70381818"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>快速入門：使用語音 SDK 在 Windows 上以 C++ 辨識語音

[語音合成](quickstart-text-to-speech-cpp-windows.md)和[語音翻譯](quickstart-translate-speech-cpp-windows.md)也提供快速入門。

您也可以選擇不同的程式設計語言和環境：<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

在此文章中，您會建立適用於 Windows 的 C++ 主控台應用程式。 您將使用認知服務[語音 SDK](speech-sdk.md)，即時從電腦的麥克風將語音轉譯為文字。 應用程式是利用[語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget) \(英文\) 與 Microsoft Visual Studio 2019 (任何版本) 所建置的。

## <a name="prerequisites"></a>必要條件

您需要語音服務訂用帳戶金鑰，才能完成本快速入門。 您可以免費取得一個金鑰。 如需詳細資訊，請參閱[免費試用語音服務](get-started.md)。

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 開啟來源檔案 helloworld.cpp  。

1. 使用下列程式碼片段取代所有程式碼：

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. 在相同檔案中，以您的訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](regions.md)取代 `YourServiceRegion` 字串。

1. 從功能表列中，選擇 [檔案]   > [全部儲存]  。

## <a name="build-and-run-the-application"></a>建置並執行應用程式

1. 從功能表列中，選取 [建置]   > [建置方案]  來建置應用程式。 現在會編譯程式碼，而且不會出現任何錯誤。

1. 選擇 [偵錯]   > [開始偵錯]  \(或按 **F5**\) 以啟動 **helloworld** 應用程式。

1. 請說英文片語或句子。 應用程式會將您的語音傳送到語音服務，語音會轉換為文字並傳送回應用程式顯示。

   ![成功辨識後的主控台輸出](media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>後續步驟

在 GitHub 上可取得其他範例，例如如何讀取音訊檔案中的語音。

> [!div class="nextstepaction"]
> [瀏覽 GitHub 上的 C++ 範例](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>另請參閱

- [讓自訂語音模型定型](how-to-custom-speech-train-model.md)
