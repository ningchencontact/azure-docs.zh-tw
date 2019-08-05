---
title: 快速入門：翻譯語音，C++ (Windows) - 語音服務
titleSuffix: Azure Cognitive Services
description: 在此快速入門中，您將會建立簡單的 C++ 應用程式來擷取使用者語音，將它翻譯為另一個語言，然後將文字輸出到命令列。 此指南是專為 Windows 使用者所設計。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 379087ca94eee6ce3d45bfd97b4771c5f08d6333
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607694"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c"></a>快速入門：使用適用於 C++ 的語音 SDK 來翻譯語音

另備有[語音辨識](quickstart-cpp-windows.md)和[文字轉換語音](quickstart-text-to-speech-cpp-windows.md)的快速入門。

在此快速入門中，您將會建立簡單的 C++ 應用程式以從電腦的麥克風即時擷取使用者語音，翻譯該語音，然後將翻譯的文字轉譯到命令列。 此應用程式是設計來在 64 位元的 Windows 上執行，而且是使用[語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget) \(英文\) 和 Microsoft Visual Studio 2017 或更新版本來建置。

如需可供進行語音翻譯的完整語言清單，請參閱[語言支援](language-support.md)。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 或更新版本
* 語音服務的 Azure 訂用帳戶金鑰。 [免費取得一個金鑰](get-started.md)。

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 開啟來源檔案 helloworld.cpp  。 使用下列項目取代開頭 include 陳述式 (`#include "stdafx.h"` 或 `#include "pch.h"`) 下方的所有程式碼：

    [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/cpp-windows/helloworld/helloworld.cpp#code)]

1. 在相同檔案中，以您的訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](regions.md)取代 `YourServiceRegion` 字串。

1. 儲存專案的變更。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

1. 建置應用程式。 從功能表列中，選擇 [建置]   > [建置解決方案]  。 這應該會編譯程式碼，而且不會出現任何錯誤。

   ![Visual Studio 應用程式的螢幕擷取畫面，其中 [建置解決方案] 選項已醒目提示](media/sdk/qs-cpp-windows-06-build.png)

1. 啟動應用程式。 從功能表列中，選擇 [偵錯]   > [開始偵錯]  ，或按 **F5**。

   ![Visual Studio 應用程式的螢幕擷取畫面，其中 [開始偵錯] 選項已醒目提示](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. 一個主控台視窗隨即顯示，提示您說話。 請說英文片語或句子。 您的語音會傳送到語音服務，並翻譯並轉譯為文字；該文字會出現在相同的視窗中。

   ![成功翻譯後主控台輸出的螢幕擷取畫面](media/sdk/qs-translate-cpp-windows-output.png)

## <a name="next-steps"></a>後續步驟

在 GitHub 上可取得其他範例，例如如何讀取音訊檔案中的語音，以及將翻譯的文字輸出為合成語音。

> [!div class="nextstepaction"]
> [探索 GitHub 上的 C++ 範例](https://aka.ms/csspeech/samples) \(英文\)

## <a name="see-also"></a>另請參閱

- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
