---
title: 快速入門：辨識語音 (Objective-C) - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 SDK 在 iOS 上以 Objective-C 辨識語音
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: chlandsi
ms.openlocfilehash: ee0be31b10c86651fa36577ad0dffccc3dc7bf59
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59009266"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>快速入門：使用語音 SDK 在 iOS 上以 Objective-C 辨識語音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

在本文中，您將了解如何使用認知服務語音 SDK 以 Objective-C 建立 iOS 應用程式，以將從麥克風或含有錄製音訊的檔案輸出的語音轉譯成文字。

## <a name="prerequisites"></a>必要條件

開始之前，以下為必要條件清單：

* 語音服務的[訂用帳戶金鑰](get-started.md)
* 具有 [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) \(英文\) 或更新版本的 macOS 機器
* 將目標設定為 iOS 9.3 版或更新版本

## <a name="get-the-speech-sdk-for-ios"></a>取得適用於 iOS 的語音 SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

認知服務語音 SDK 目前的版本為 `1.4.0`。

適用於 iOS 的認知服務語音 SDK 目前以 Cocoa Framework 的形式散發。
您可以從[這裡](https://aka.ms/csspeech/iosbinary)加以下載。 請將檔案下載到您的主目錄。

## <a name="create-an-xcode-project"></a>建立 Xcode 專案

啟動 Xcode，然後按一下 [檔案] > [新增] > [專案] 以啟動新專案。
在範本選取對話方塊中，選擇「iOS 單一檢視應用程式」範本。

在後續的對話方塊中，進行下列選取：

1. 專案選項對話方塊
    1. 輸入快速入門應用程式的名稱，例如 `helloworld`。
    1. 如果您已有 Apple 開發人員帳戶，請輸入適當的組織名稱和組織識別碼。 基於測試用途，您可以直接挑選類似於 `testorg` 的任何名稱。 若要簽署應用程式，您需要適當的佈建設定檔。 如需詳細資訊，請參閱 [Apple 開發人員網站](https://developer.apple.com/)。
    1. 確定已選擇 Objective-C 作為專案的語言。
    1. 停用測試和核心資料的所有核取方塊。
    ![專案設定](media/sdk/qs-objectivec-project-settings.png)
1. 選取專案目錄
    1. 選擇用來放置專案的主目錄。 這會在您的主目錄中建立 `helloworld` 目錄，其中包含 Xcode 專案檔的所有檔案。
    1. 停用為此範例專案建立 Git 存放庫的功能。
    1. 在 [專案設定] 中調整 SDK 的路徑。
        1. 在 [內嵌的二進位檔] 標頭下方的 [一般] 索引標籤中，新增 SDK 程式庫作為架構：[新增內嵌的二進位檔] > [新增其他...] > 瀏覽至您的主目錄，然後選擇檔案 `MicrosoftCognitiveServicesSpeech.framework`。 這會將 SDK 程式庫自動加入**連結的架構和程式庫**標頭。
        ![新增的架構](media/sdk/qs-objectivec-framework.png)
        1. 移至 [組建設定] 索引標籤，然後啟動 [所有] 設定。
        1. 將目錄 `$(SRCROOT)/..` 新增至 [搜尋路徑] 標題下方的 [架構搜尋路徑]。
        ![架構搜尋路徑設定](media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>設定 UI

範例應用程式將提供一個非常簡單的 UI：以檔案或是麥克風輸入啟動語音辨識的兩個按鈕，以及顯示結果的文字標籤。
此 UI 可在專案的 `Main.storyboard` 部分中設定。
以滑鼠右鍵按一下專案樹狀結構的 `Main.storyboard` 項目，然後選取 [開啟形式...] > [原始程式碼]，以開啟分鏡腳本的 XML 檢視。
請將自動產生的 XML 取代為此程式碼：

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>新增範例程式碼

1. 以滑鼠右鍵按一下連結，然後選擇 [另存目標...]，以下載[範例 wav 檔案](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav)。將 wav 檔案從 [搜尋工具] 視窗拖曳到 [專案] 檢視的根層級中，以將其新增為專案的資源。
   在下列對話方塊中按一下 [完成]，而不變更設定。
1. 將自動產生的 `ViewController.m` 檔案內容取代為：

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. 將字串 `YourSubscriptionKey` 取代為您的訂用帳戶金鑰。
1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](regions.md)取代 `YourServiceRegion` 字串。
1. 將麥克風存取的要求。 以滑鼠右鍵按一下專案樹狀結構的 `Info.plist` 項目，然後選取 [Open As...] \(開啟為\) > [Source Code] \(原始程式碼\)。 將以下幾行新增至 `<dict>` 區段中，然後儲存檔案。
    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="building-and-running-the-sample"></a>建置並執行範例

1. 顯示偵錯輸出 ([檢視] > [偵錯區域] > [啟動主控台])。
1. 在 [產品] -> [目的地] 功能表中的清單中，選擇 iOS 模擬器或是連接到您開發電腦的 iOS 裝置，當做應用程式的目的地。
1. 建置範例程式碼，然後從功能表中選取 [產品] -> [執行] 或按一下 [播放] 按鈕，在 iOS 模擬器中加以執行。
   語音 SDK 目前僅支援 64 位元 iOS 平台。
1. 按一下應用程式中的 [辨識 (檔案)] 按鈕後，應該會在螢幕的下半部看到音訊檔案的內容 「天氣如何？」。

   ![模擬的 iOS 應用程式](media/sdk/qs-objectivec-simulated-app.png)

1. 按一下應用程式中的 [辨識 (麥克風)] 按鈕，並說出幾個字後，應該會在螢幕的下半部看到說出的文字。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瀏覽 GitHub 上的 Objective-C 範例](https://aka.ms/csspeech/samples)

