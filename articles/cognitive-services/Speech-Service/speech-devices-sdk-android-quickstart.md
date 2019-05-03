---
title: 快速入門：Android-語音服務上執行的語音裝置 SDK
titleSuffix: Azure Cognitive Services
description: 先決條件與指示開始使用 Android 的語音裝置 SDK。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: d5af2bb61eeb986f02a31d45ff9236ecc0c8427e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026770"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>快速入門：在 Android 上執行語音裝置 SDK 範例應用程式

在本快速入門中，您將了解如何使用適用於 Android 的語音裝置 SDK 來建置具備語音功能的產品。

本指南會要求[Azure 認知服務](get-started.md)與語音服務資源的帳戶。 如果您還沒有帳戶，可以使用[免費試用](https://azure.microsoft.com/try/cognitive-services/)來取得訂用帳戶金鑰。

範例應用程式的原始程式碼包含在「語音裝置 SDK」中。 您也可以[在 GitHub 上取得](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)該原始程式碼。

## <a name="prerequisites"></a>必要條件

開始使用語音裝置 SDK 之前，您將需要：

* 請遵循所提供的指示您[開發套件](get-speech-devices-sdk.md)裝置上的乘冪。

* 下載最新版[語音裝置 SDK](https://aka.ms/sdsdk-download)，並解壓縮至您的工作目錄.zip。
   > [!NOTE]
   > .Zip 檔案包含 Android 範例應用程式。

* 若要取得[語音服務的 Azure 訂用帳戶金鑰](get-started.md)

* 如果您打算使用語音服務，以從使用者談話中識別出意圖 （或動作） 時，您將需要[Language Understanding Service (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription)訂用帳戶。 若要深入了解 LUIS 和意圖辨識，請參閱[辨識語音意圖，有了 LUIS， C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)。

    您可以[建立簡單 LUIS 模型](https://docs.microsoft.com/azure/cognitive-services/luis/)，或使用簡單 LUIS 模型 LUIS-example.json。 您可以從[語音裝置 SDK 下載網站](https://aka.ms/sdsdk-luis)取得範例 LUIS 模型。 若要將您模型的 JSON 檔案上傳到 [LUIS 入口網站](https://www.luis.ai/home)，請選取 [Import new app] \(匯入新應用程式\)，然後選取 JSON 檔案。

* 在您的電腦上安裝 [Android Studio](https://developer.android.com/studio/) 和 [Vysor](https://vysor.io/download/)。

## <a name="set-up-the-device"></a>將裝置設定

1. 在您的電腦上啟動 Vysor。

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1. 您的裝置應該會列在 [Choose a device] \(選擇裝置\) 底下。 選取裝置旁邊的 [View] \(檢視\) 按鈕。

1. 選取資料夾圖示，然後選取 [Settings] \(設定\) > [WLAN]，以連線到您的無線網路。

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)

    > [!NOTE]
    > 如果貴公司有關於將裝置連線到 Wi-Fi 系統的政策，您就必須取得 Mac 位址並連絡 IT 部門，以了解如何將其連線到公司的 Wi-Fi 系統。
    >
    > 若要尋找開發套件的 MAC 位址，請選取開發套件桌面上的檔案資料夾圖示。
    >
    >  ![Vysor 檔案資料夾](media/speech-devices-sdk/qsg-10.png)
    >
    > 選取 [Settings] \(設定) 。 搜尋 "mac address"，然後選取 [Mac address] \(Mac 位址\) > [Advanced WLAN] \(進階 WLAN\)。 記下出現在靠近對話方塊底部的 MAC 位址。
    >
    > ![Vysor MAC 位址](media/speech-devices-sdk/qsg-11.png)
    >
    > 有些公司針對裝置可連線到其 Wi-Fi 系統的時間長度可能有限制。 您可能需要在特定天數之後，將開發套件的 Wi-Fi 系統註冊延長。

## <a name="run-the-sample-application"></a>執行範例應用程式

若要驗證您的開發套件安裝程式，建置並安裝範例應用程式：

1. 啟動 Android Studio。

1. 選取 [開啟現有 Android Studio 專案]。

   ![Android Studio - 開啟現有專案](media/speech-devices-sdk/qsg-5.png)

1. 前往 C:\SDSDK\Android-Sample-Release\example。 選取 [OK] \(確定\) 以開啟範例專案。

1. 將您的語音訂用帳戶金鑰加入至原始程式碼中。 如果您想要試用意圖辨識，請一併新增您的 [Language Understanding 服務](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)訂用帳戶金鑰和應用程式識別碼。

   您的金鑰和應用程式資訊會在 MainActivity.java 原始程式檔的下列幾行中：

   ```java
   // Subscription
   private static final String SpeechSubscriptionKey = "[your speech key]";
   private static final String SpeechRegion = "westus";
   private static final String LuisSubscriptionKey = "[your LUIS key]";
   private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
   private static final String LuisAppId = "[your LUIS app ID]"
   ```

1. 預設喚醒字 (關鍵字) 是 "Computer"。 您也可以嘗試其中一個其他提供的喚醒字，例如 "Machine" 或 "Assistant"。 這些替代喚醒字的資源檔在「語音裝置 SDK」的 [keyword] 資料夾中。 例如 C:\SDSDK\Android-Sample-Release\keyword\Computer 便包含用於 "Computer" 喚醒字的檔案。

   > [!TIP]
   > 您也可以[建立自訂的喚醒字](speech-devices-sdk-create-kws.md)。

    若要使用新的線上醒機字，更新中的下列兩行`MainActivity.java`，並將網路喚醒 word 封裝複製到您的應用程式。 例如，若要使用網路喚醒 word 封裝 kws 喚醒詞彙 'Machine'-machine.zip:

   * 網路喚醒 word 將套件複製到資料夾"C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\ 」。
   * 更新`MainActivity.java`關鍵字和封裝名稱：

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. 更新包含麥克風陣列幾何設定的下列各行：

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   下表列出支援的值：

   |變數|意義|可用值|
   |--------|-------|----------------|
   |`DeviceGeometry`|實體麥克風設定|針對圓形裝置套件：`Circular6+1` |
   |||針對線性裝置套件：`Linear4`|
   |`SelectedGeometry`|軟體麥克風設定|針對使用所有麥克風的圓形開發套件：`Circular6+1`|
   |||針對使用四個麥克風的圓形開發套件：`Circular3+1`|
   |||針對使用所有麥克風的線性開發套件：`Linear4`|
   |||針對使用兩個麥克風的線性開發套件：`Linear2`|

1. 若要建置應用程式，請在 [Run] \(執行\) 功能表上選擇 [Run 'app'] \(執行「應用程式」\)。 隨即會顯示 [Select Deployment Target] \(選取部署目標\) 對話方塊。

1. 選取您的裝置，然後選取 [OK] \(確定\)，以將應用程式部署到裝置。

    ![[Select Deployment Target] \(選取部署目標\) 對話方塊](media/speech-devices-sdk/qsg-7.png)

1. 「語音裝置 SDK」範例應用程式會啟動，並顯示下列選項：

   ![「語音裝置 SDK」範例應用程式和選項](media/speech-devices-sdk/qsg-8.png)

1. 進行實驗！

## <a name="troubleshooting"></a>疑難排解

   如果您無法連線到語音裝置。 命令提示字元 視窗中，輸入下列命令。 它會傳回裝置的清單：

   ```powershell
    adb devices
   ```

   > [!NOTE]
   > 此命令會使用 Android Debug Bridge， `adb.exe`，這是 Android Studio 中安裝的一部分。 此工具位於 C:\Users\[使用者名稱]\AppData\Local\Android\Sdk\platform-tools。 您可以將此目錄新增至您的路徑，以便更方便地叫用 `adb`。 否則，您必須在叫用 `adb` 的每個命令中指定 adb.exe 的完整安裝路徑。
   >
   > 如果您看到錯誤`no devices/emulators found`然後檢查您的 USB 纜線已連接，並確定使用的是高品質的纜線。
   >

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [檢閱版本資訊](devices-sdk-release-notes.md)
