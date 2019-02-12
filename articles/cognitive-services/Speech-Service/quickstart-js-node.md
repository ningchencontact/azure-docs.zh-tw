---
title: 快速入門：辨識語音，Node.js - 語音服務
titleSuffix: Azure Cognitive Services
description: 您可以使用本指南，透過適用於 Node.js 的語音 SDK 建立語音轉換文字的主控台應用程式。 完成之後，您可以使用電腦的麥克風將語音即時轉譯為文字。
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 1/16/2019
ms.author: fmegen
ms.openlocfilehash: 99938f8b64339556c0ee4b9caa9124c609159af6
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747266"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-nodejs"></a>快速入門：使用適用於 Node.js 的語音 SDK 來辨識語音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

本文說明如何使用 Azure 認知服務語音 SDK 的 JavaScript 繫結來建立 Node.js 專案，以將語音轉譯為文字。
此應用程式是以[適用於 JavaScript 的語音 SDK](https://aka.ms/csspeech/npmpackage) 作為基礎。

## <a name="prerequisites"></a>必要條件

* 適用於語音服務的 Azure 訂用帳戶金鑰。 [免費取得一個金鑰](get-started.md)。
* 目前版本的 [Node.js](https://nodejs.org) \(英文\)。

## <a name="create-a-new-project"></a>建立新專案

建立新的資料夾及初始化專案：

```sh
npm init -f
```

這個命令會使用預設值來初始化 **package.json** 檔案。 您後續應該會再度編輯此檔案。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

將語音 SDK 新增到您的 Node.js 專案：

```
npm install microsoft-cognitiveservices-speech-sdk
```

這個命令會從 **npmjs** 下載並安裝最新版本的語音 SDK，以及任何所需的必要條件。 SDK 會安裝到您專案資料夾內的 `node_modules` 目錄中。

## <a name="use-the-speech-sdk"></a>使用語音 SDK

在資料夾中建立名為 `index.js` 的新檔案，並使用文字編輯器開啟此檔案。

> [!NOTE]
> 在 Node.js 中，語音 SDK 並不支援麥克風或 **File** 資料類型。 只有在瀏覽器上才支援這兩者。 因此，請改為使用針對語音 SDK 的 **Stream** 介面，這可透過 `AudioInputStream.createPushStream()` 或 `AudioInputStream.createPullStream()` 來達成。

在此範例中，我們會使用 `PushAudioInputStream` 介面。

新增這個 JavaScript 程式碼：

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="run-the-sample"></a>執行範例

若要開啟應用程式，請將 `YourSubscriptionKey`、`YourServiceRegion` 及 `YourAudioFile.wav` 納入您的設定。 然後藉由呼叫此命令來加以執行：

```sh
node index.js
```

其會使用所提供的檔名來觸發辨識。 然後會在主控台上顯示輸出。

此範例是您在更新訂用帳戶金鑰並使用檔案 `whatstheweatherlike.wav` 之後，執行 `index.js` 時所產生的輸出：

```json
SpeechRecognitionResult {
  "privResultId": "9E30EEBD41AC4571BB77CF9164441F46",
  "privReason": 3,
  "privText": "What's the weather like?",
  "privDuration": 15900000,
  "privOffset": 300000,
  "privErrorDetails": null,
  "privJson": {
    "RecognitionStatus": "Success",
    "DisplayText": "What's the weather like?",
    "Offset": 300000,
    "Duration": 15900000
  },
  "privProperties": null
}
```

## <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>透過 Visual Studio Code 安裝及使用語音 SDK

您也可以從 Visual Studio Code 執行範例。 請遵循這些步驟來安裝、開啟及執行快速入門：

1. 啟動 Visual Studio Code。 選取 [開啟資料夾]。 然後瀏覽至 quickstart 資料夾。

   ![開啟資料夾](media/sdk/qs-js-node-01-open_project.png)

1. 在 Visual Studio Code 中開啟終端機。

   ![終端機視窗](media/sdk/qs-js-node-02_open_terminal.png)

1. 執行 `npm` 以安裝相依性。

   ![npm 安裝](media/sdk/qs-js-node-03-npm_install.png)

1. 現在您已準備好開啟 `index.js` 並設定中斷點。

   ![在行 16 上具有中斷點的 index.js](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. 若要開始偵錯，請選取 F5 或從功能表選取 [偵錯/開始偵錯]。

   ![[偵錯] 功能表](media/sdk/qs-js-node-05-start_debugging.png)

1. 到達中斷點時，您便可以檢查呼叫堆疊和變數。

   ![偵錯工具](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. 所有輸出都會顯示於 [偵錯主控台] 視窗中。

   ![偵錯主控台](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索 GitHub 上的 Node.js 範例](https://aka.ms/csspeech/samples) \(英文\)
