---
title: 快速入門：翻譯語音，C++ (Windows) - 語音服務
titleSuffix: Azure Cognitive Services
description: 在此快速入門中，您將會建立簡單的 C++ 應用程式來擷取使用者語音，將它翻譯為另一個語言，然後將文字輸出到命令列。 此指南是專為 Windows 使用者所設計。
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 7c5ab43aad4f4a34a688e373ac8c125ae4063157
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223862"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c"></a>快速入門：使用適用於 C++ 的語音 SDK 來翻譯語音

在此快速入門中，您將會建立簡單的 C++ 應用程式以從電腦的麥克風即時擷取使用者語音，翻譯該語音，然後將翻譯的文字轉譯到命令列。 此應用程式是設計來在 64 位元的 Windows 上執行，並使用[語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget) \(英文\) 與 Microsoft Visual Studio 2017 所建置。

如需可供進行語音翻譯的完整語言清單，請參閱[語言支援](language-support.md)。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* 適用於語音服務的 Azure 訂用帳戶金鑰。 [免費取得一個金鑰](get-started.md)。

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 開啟來源檔案 helloworld.cpp。 使用下列項目取代開頭 include 陳述式 (`#include "stdafx.h"` 或 `#include "pch.h"`) 下方的所有程式碼：

    ```cpp
    #include "pch.h"
    #include <iostream>
    #include <vector>
    #include <speechapi_cxx.h>

    using namespace std;
    using namespace Microsoft::CognitiveServices::Speech;
    using namespace Microsoft::CognitiveServices::Speech::Translation;

    // Translation with microphone input.
    void TranslationWithMicrophone()
    {
        // Creates an instance of a speech translation config with specified subscription key and service region.
        // Replace with your own subscription key and service region (e.g., "westus").
        auto config = SpeechTranslationConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

        // Sets source and target languages
        // Replace with the languages of your choice.
        auto fromLanguage = "en-US";
        config->SetSpeechRecognitionLanguage(fromLanguage);
        config->AddTargetLanguage("de");
        config->AddTargetLanguage("fr");

        // Creates a translation recognizer using microphone as audio input.
        auto recognizer = TranslationRecognizer::FromConfig(config);
        cout << "Say something...\n";

        // Starts translation. RecognizeOnceAsync() returns when the first utterance has been recognized,
        // so it is suitable only for single shot recognition like command or query. For long-running
        // recognition, use StartContinuousRecognitionAsync() instead.
        auto result = recognizer->RecognizeOnceAsync().get();

        // Checks result.
        if (result->Reason == ResultReason::TranslatedSpeech)
        {
            cout << "RECOGNIZED: Text=" << result->Text << std::endl
                << "  Language=" << fromLanguage << std::endl;

            for (const auto& it : result->Translations)
            {
                cout << "TRANSLATED into '" << it.first.c_str() << "': " << it.second.c_str() << std::endl;
            }
        }
        else if (result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << result->Text << " (text could not be translated)" << std::endl;
        }
        else if (result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
        else if (result->Reason == ResultReason::Canceled)
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;

            if (cancellation->Reason == CancellationReason::Error)
            {
                cout << "CANCELED: ErrorCode=" << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
        }
    }

    int wmain()
    {
        TranslationWithMicrophone();
        cout << "Please press a key to continue.\n";
        cin.get();
        return 0;
    }
    ```

1. 在相同檔案中，以您的訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](regions.md)取代 `YourServiceRegion` 字串。

1. 儲存專案的變更。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

1. 建置應用程式。 從功能表列中，選擇 [建置] > [建置解決方案]。 這應該會編譯程式碼，而且不會出現任何錯誤。

   ![Visual Studio 應用程式的螢幕擷取畫面，其中 [建置解決方案] 選項已醒目提示](media/sdk/qs-cpp-windows-06-build.png)

1. 啟動應用程式。 從功能表列中，選擇 [偵錯] > [開始偵錯]，或按 **F5**。

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
