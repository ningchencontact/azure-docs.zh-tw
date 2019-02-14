---
title: 快速入門：辨識語音，C++ (Windows) - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音服務 SDK 在 Windows 桌面上以 C++ 辨識語音
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: wolfma
ms.openlocfilehash: 3068c4e3c1bb7ddb41feb45f999398d533b66afa
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863035"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>快速入門：使用語音 SDK 在 Windows 上以 C++ 辨識語音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

在本文中，您會建立適用於 Windows 的 C++ 主控台應用程式。 您將使用認知服務[語音 SDK](speech-sdk.md)，即時從電腦的麥克風將語音轉譯為文字。 應用程式是利用[語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget)與 Microsoft Visual Studio 2017 (任何版本) 所建置的。

## <a name="prerequisites"></a>必要條件

您需要語音服務訂用帳戶金鑰，才能完成本快速入門。 您可以免費取得一個金鑰。 如需詳細資訊，請參閱[免費試用語音服務](get-started.md)。

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 開啟來源檔案 helloworld.cpp。 使用下列項目取代開頭 include 陳述式 (`#include "stdafx.h"` 或 `#include "pch.h"`) 下方的所有程式碼：

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. 在相同檔案中，以您的訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](regions.md)取代 `YourServiceRegion` 字串。

1. 儲存專案的變更。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

1. 建置應用程式。 從功能表列中，選擇 [建置] > [建置解決方案]。 這應該會編譯程式碼，而且不會出現任何錯誤。

   ![Visual Studio 應用程式的螢幕擷取畫面，其中 [建置解決方案] 選項已醒目提示](media/sdk/qs-cpp-windows-06-build.png)

1. 啟動應用程式。 從功能表列中，選擇 [偵錯] > [開始偵錯]，或按 **F5**。

   ![Visual Studio 應用程式的螢幕擷取畫面，其中 [開始偵錯] 選項已醒目提示](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. 一個主控台視窗隨即顯示，提示您說話。 請說英文片語或句子。 您的語音會傳送到語音服務，並且轉譯為文字，該文字會出現在相同視窗中。

   ![成功辨識後主控台輸出的螢幕擷取畫面](media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>後續步驟

在 GitHub 上可取得其他範例，例如如何讀取音訊檔案中的語音。

> [!div class="nextstepaction"]
> [瀏覽 GitHub 上的 C++ 範例](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>另請參閱

- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
