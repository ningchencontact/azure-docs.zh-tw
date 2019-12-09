---
title: 快速入門：辨識語音、意圖和實體，Python - 語音服務
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
ms.openlocfilehash: 9a4c7f24a2e28743679e312e3dce0bc605db6749
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816126"
---
## <a name="prerequisites"></a>必要條件

開始之前，請務必：

> [!div class="checklist"]
>
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [建立 Language Understanding (LUIS) 應用程式並取得端點金鑰](../../../../quickstarts/create-luis.md)
> * [設定開發環境](../../../../quickstarts/setup-platform.md)
> * [建立空的範例專案](../../../../quickstarts/create-project.md)

## <a name="open-your-project"></a>開啟您的專案

在您的 Python 編輯器中開啟 Quickstart.py。

## <a name="start-with-some-boilerplate-code"></a>從重複使用程式碼開始著手

我們將新增程式碼，作為專案的基本架構。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>建立語音設定

您必須先建立使用 LUIS 端點金鑰和區域的設定，才能夠初始化 `IntentRecognizer` 物件。 接下來插入此程式碼。

此範例使用 LUIS 金鑰和區域建構 `SpeechConfig` 物件。 如需可用方法的完整清單，請參閱 [SpeechConfig 類別](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) \(英文\)。
語音 SDK 會預設為使用 en-us 來辨識語言，如需選擇來源語言的詳細資訊，請參閱[指定語音轉換文字的來源語言](../../../../how-to-specify-source-language.md)。

> [!NOTE]
> 請務必使用 LUIS 端點金鑰，而不是 Starter 或 Authoring 金鑰，因為只有端點金鑰才適用於辨識語音到意圖的轉換。 如需如何取得正確金鑰的指示，請參閱[建立 LUIS 應用程式並取得端點金鑰](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md)。

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

## <a name="initialize-an-intentrecognizer"></a>初始化 IntentRecognizer

現在，我們將建立一個 `IntentRecognizer`。 將此程式碼插入您的語音設定的下方。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>新增 LanguageUnderstandingModel 和意圖

現在，您必須將 `LanguageUnderstandingModel` 與意圖辨識器建立關聯，並新增您想要辨識的意圖。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>辨識意圖

從 `IntentRecognizer` 物件，您將呼叫 `recognize_once()` 方法。 此方法可讓語音服務知道您要傳送單一片語以進行辨識，且一旦識別出該片語即停止辨識語音。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>顯示辨識結果 (或錯誤)

當語音服務傳回辨識結果時，建議您對其執行一些動作。 為了簡單起見，我們將結果列印到主控台。

在 using 陳述式中您對 `recognize_once()` 的呼叫下方，新增此程式碼：[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>檢查您的程式碼

此時，您的程式碼應會如下所示：  
(我們已在此版本中新增一些註解) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>建置並執行您的應用程式

從主控台或在您的 IDE 中執行範例：

    ````
    python quickstart.py
    ````

系統將會辨識接下來 15 秒來自您麥克風的語音輸入，並記錄在主控台視窗中。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]
