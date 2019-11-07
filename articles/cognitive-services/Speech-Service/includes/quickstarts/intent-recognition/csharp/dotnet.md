---
title: 快速入門：辨識語音、意圖和實體，C# - 語音服務
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a54d01aa78ad77c9328f79f31d21da570e7f5676
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500728"
---
## <a name="prerequisites"></a>必要條件

開始之前，請務必：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [建立 LUIS 應用程式並取得端點金鑰](../../../../quickstarts/create-luis.md)
> * [設定開發環境](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [建立空的範例專案](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>在 Visual Studio 中開啟您的專案

第一個步驟是確定您已在 Visual Studio 中開啟專案。

1. 啟動 Visual Studio 2019。
2. 載入您的專案，並開啟 `Program.cs`。

## <a name="start-with-some-boilerplate-code"></a>從重複使用程式碼開始著手

我們將新增程式碼，作為專案的基本架構。 請注意，您已建立名為 `RecognizeIntentAsync()` 的非同步方法。
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>建立語音設定

您必須先建立使用 LUIS 端點金鑰和區域的設定，才能夠初始化 `IntentRecognizer` 物件。 在 `RecognizeIntentAsync()` 方法中插入此程式碼。

此範例會使用 `FromSubscription()` 方法來建置 `SpeechConfig`。 如需可用方法的完整清單，請參閱 [SpeechConfig 類別](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)。

> [!NOTE]
> 請務必使用 LUIS 端點金鑰，而不是 Starter 或 Authroing 金鑰，因為只有端點金鑰才適用於語音轉意圖辨識。 如需如何取得正確金鑰的指示，請參閱[建立 LUIS 應用程式並取得端點金鑰](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md)。

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

## <a name="initialize-a-intentrecognizer"></a>初始化 IntentRecognizer

現在，我們將建立一個 `IntentRecognizer`。 這個物件是在 using 陳述式內建立的，可確保能夠適當釋放非受控資源。 將此程式碼插入您的語音設定下方的 `RecognizeIntentAsync()` 方法中。
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=28-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>新增 LanguageUnderstandingModel 和意圖

現在，您必須將 `LanguageUnderstandingModel` 與意圖辨識器建立關聯，並新增您想要辨識的意圖。
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=31-35)]

## <a name="recognize-an-intent"></a>辨識意圖

從 `IntentRecognizer` 物件，您將呼叫 `RecognizeOnceAsync()` 方法。 此方法可讓語音服務知道您要傳送單一片語以進行辨識，且一旦識別出該片語即停止辨識語音。

在 using 陳述式中，新增下列程式碼：[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-the-recognition-results-or-errors"></a>顯示辨識結果 (或錯誤)

當語音服務傳回辨識結果時，您會想要對它執行一些動作。 我們將以簡單為原則，將結果列印到主控台。

在 using 陳述式中的 `RecognizeOnceAsync()` 下方，新增下列程式碼：[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=48-75)]

## <a name="check-your-code"></a>檢查您的程式碼

此時，您的程式碼應會如下所示：(我們已在此版本中新增一些註解) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=5-86)]

## <a name="build-and-run-your-app"></a>建置並執行您的應用程式

現在您已準備好使用語音服務來建立應用程式，並測試我們的語音辨識。

1. **編譯程式碼** - 從 Visual Studio 的功能表列中，選擇 [建置]   > [建置解決方案]  。
2. **啟動應用程式** - 從功能表列中，選擇 [偵錯]   > [開始偵錯]  ，或按 **F5**。
3. **開始辨識** - 它會提示您說英文片語。 您的語音會傳送至語音服務、轉譯為文字，並在主控台中轉譯。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]
