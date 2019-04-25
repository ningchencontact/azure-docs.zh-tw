---
title: 快速入門：合成語音，C++ (Windows) - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 SDK 在 Windows 桌面上以 C++ 合成語音
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 4/03/2019
ms.author: yinhew
ms.openlocfilehash: 732816ce10836d5828e7f325a3eb3fe31627d4f4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59012487"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>快速入門：使用語音 SDK 在 Windows 上以 C++ 合成語音

在本文中，您會建立適用於 Windows 的 C++ 主控台應用程式。 您將使用認知服務[語音 SDK](speech-sdk.md) 即時從文字合成語音，並以電腦的喇叭播放語音。 應用程式是利用[語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget)與 Microsoft Visual Studio 2017 (任何版本) 所建置的。

本文說明的是可從[語音 SDK 1.4.0](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/1.4.0) 使用的功能。

如需可進行語音合成的完整語言/語音清單，請參閱[語言支援](language-support.md#text-to-speech)。

## <a name="prerequisites"></a>必要條件

您需要語音服務訂用帳戶金鑰，才能完成本快速入門。 您可以免費取得一個金鑰。 如需詳細資訊，請參閱[免費試用語音服務](get-started.md)。

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 開啟來源檔案 helloworld.cpp。 使用下列項目取代開頭 include 陳述式 (`#include "stdafx.h"` 或 `#include "pch.h"`) 下方的所有程式碼：

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. 在相同檔案中，以您的訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](regions.md)取代 `YourServiceRegion` 字串。

1. 儲存專案的變更。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

1. 建置應用程式。 從功能表列中，選擇 [建置] > [建置解決方案]。 這應該會編譯程式碼，而且不會出現任何錯誤。

   ![Visual Studio 應用程式的螢幕擷取畫面，其中 [建置解決方案] 選項已醒目提示](media/sdk/qs-cpp-windows-06-build.png)

1. 啟動應用程式。 從功能表列中，選擇 [偵錯] > [開始偵錯]，或按 **F5**。

   ![Visual Studio 應用程式的螢幕擷取畫面，其中 [開始偵錯] 選項已醒目提示](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. 主控台視窗隨即顯示，提示您輸入某些文字。 請輸入幾個字或句子。 您輸入的文字會傳送至語音服務並合成為語音，然後以喇叭播放。

   ![此螢幕擷取畫面顯示成功合成後的主控台輸出](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>後續步驟

在 GitHub 上可取得其他範例，例如如何將語音儲存至音訊檔案。

> [!div class="nextstepaction"]
> [探索 GitHub 上的 C++ 範例](https://aka.ms/csspeech/samples) \(英文\)

## <a name="see-also"></a>另請參閱

- [自訂語音字型](how-to-customize-voice-font.md)
- [記錄語音範例](record-custom-voice-samples.md)
