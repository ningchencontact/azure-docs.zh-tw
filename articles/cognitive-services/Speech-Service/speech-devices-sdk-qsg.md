---
title: 開始使用 Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: 開始使用 Speech Devices SDK 的先決條件和指示。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: erhopf
ms.openlocfilehash: e035e1bdedefc8e327b0179006b45f3bad4c41ee
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2018
ms.locfileid: "49470195"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>開始使用 Speech Devices SDK

本文說明如何設定您的開發電腦和語音裝置開發套件，以使用「語音裝置 SDK」來開發支援語音功能的裝置。 接著，您將建置範例應用程式並將它部署到裝置。 

範例應用程式的原始程式碼包含在「語音裝置 SDK」中。 您也可以[在 GitHub 上取得](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)該原始程式碼。

## <a name="prerequisites"></a>必要條件

在使用「語音裝置 SDK」開始開發之前，請收集您需要的資訊和軟體：

* [從 ROOBO 取得開發套件](http://ddk.roobo.com/)。 套件隨附線性或圓形麥克風陣列設定。 請依據您的需求選擇正確的設定。

    |開發套件設定|喇叭位置|
    |-----------------------------|------------|
    |圓形|來自裝置的任何方向|
    |線性|裝置正面|

* 從[語音裝置 SDK 下載網站](https://shares.datatransfer.microsoft.com/)取得最新版「語音裝置 SDK」，其中包括 Android 範例應用程式。 將 .zip 檔案解壓縮至本機資料夾 (例如 C:\SDSDK)。

* 在您的電腦上安裝 [Android Studio](https://developer.android.com/studio/) 和 [Vysor](http://vysor.io/download/)。

* 取得[語音服務訂用帳戶金鑰](get-started.md)。 您可以從 Azure 儀表板取得 30 天免費試用版或取得金鑰。

* 如果您希望使用語音服務的意圖辨識，請訂閱 [Language Understanding 服務](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LUIS) 並[取得訂用帳戶金鑰](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription)。 

    您可以[建立簡單 LUIS 模型](https://docs.microsoft.com/azure/cognitive-services/luis/)，或使用簡單 LUIS 模型 LUIS-example.json。 您可以從[語音裝置 SDK 下載網站](https://shares.datatransfer.microsoft.com/)取得範例 LUIS 模型。 若要將您模型的 JSON 檔案上傳到 [LUIS 入口網站](https://www.luis.ai/home)，請選取 [Import new app] \(匯入新應用程式\)，然後選取 JSON 檔案。

## <a name="set-up-the-development-kit"></a>設定開發套件

1. 使用 Mini USB 纜線將開發套件連接到電腦或變壓器。 當套件連接之後，上方面板底下應該會亮起綠色電源指示燈。

1. 使用第二條 Mini USB 纜線將開發套件連接到電腦。

    ![連接開發套件](media/speech-devices-sdk/qsg-1.png)

1. 將您的開發套件依圓形或線性設定調整方向。

    |開發套件設定|方向|
    |-----------------------------|------------|
    |圓形|直立，麥克風朝向天花板|
    |線性|側邊，麥克風朝向您 (如下圖所示)|

    ![線性開發套件方向](media/speech-devices-sdk/qsg-2.png)

1. 安裝憑證和喚醒字 (關鍵字) 表檔案，並設定音效裝置的權限。 在命令提示字元視窗中輸入下列命令：

   ```
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/ 
   adb shell
   cd /data/ 
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > 這些命令會使用 Android Debug Bridge `adb.exe`，它是 Android Studio 安裝的一部分。 此工具位於 C:\Users\[使用者名稱]\AppData\Local\Android\Sdk\platform-tools。 您可以將此目錄新增至您的路徑，以便更方便地叫用 `adb`。 否則，您必須在叫用 `adb` 的每個命令中指定 adb.exe 的完整安裝路徑。

    > [!TIP]
    > 請將您電腦的麥克風和喇叭靜音，以確定您使用的是開發套件的麥克風。 如此一來，您就不會不小心讓來自電腦的音訊觸發該裝置。
    
1.  在您的電腦上啟動 Vysor。

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  您的裝置應該會列在 [Choose a device] \(選擇裝置\) 底下。 選取裝置旁邊的 [View] \(檢視\) 按鈕。 
 
1.  選取資料夾圖示，然後選取 [Settings] \(設定\) > [WLAN]，以連線到您的無線網路。

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
    > 
    > 如果您想要將喇叭連結到開發套件，您可以將它連接到音訊線路輸出。您應該選擇一個高品質的 3.5mm 喇叭。
    >
    > ![Vysor 音訊](media/speech-devices-sdk/qsg-14.png)
 
## <a name="run-a-sample-application"></a>執行範例應用程式

若要執行 ROOBO 測試並驗證您的開發套件設定，請建置並安裝範例應用程式：

1.  啟動 Android Studio。

1.  選取 [開啟現有 Android Studio 專案]。

    ![Android Studio - 開啟現有專案](media/speech-devices-sdk/qsg-5.png)
 
1.  前往 C:\SDSDK\Android-Sample-Release\example。 選取 [OK] \(確定\) 以開啟範例專案。
 
1.  將您的語音訂用帳戶金鑰加入至原始程式碼中。 如果您想要試用意圖辨識，請一併新增您的 [Language Understanding 服務](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)訂用帳戶金鑰和應用程式識別碼。 

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

    您也可以[建立自訂的喚醒字](speech-devices-sdk-create-kws.md)。

    安裝您想要使用的喚醒字：
 
    * 藉由在命令提示字元視窗中執行下列命令，在裝置上的 [data] 資料夾中建立 [keyword] 資料夾：

        ```
        adb shell
        cd /data
        mkdir keyword
        exit
        ```

    * 將 kws.table、kws_k.fst 及 words_kw.txt 檔案複製到裝置的 \data\keyword\ 資料夾。 在命令提示字元視窗中執行下列命令。 如果您已建立[自訂喚醒字](speech-devices-sdk-create-kws.md)，則從 Web 產生的 kws.table 檔案會位於與 kws.table、kws_k.fst 及 words_kw.txt 檔案相同的目錄中。 針對自訂喚醒字，請使用 `adb push C:\SDSDK\Android-Sample-Release\keyword\[wake_word_name]\kws.table /data/keyword` 命令將 kws.table 檔案推送到開發套件：

        ```
        adb push C:\SDSDK\Android-Sample-Release\keyword\kws.table /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_k.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\words_kw.txt /data/keyword
        ```
    
    * 在範例應用程式中參考這些檔案。 在 MainActivity.java 中尋找下列幾行。 確定指定的關鍵字是您正在使用的關鍵字，且路徑指向您推送至裝置的 `kws.table` 檔案。
        
        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > 在您自己的程式碼中，您可以使用 kws.table 檔案來建立關鍵字模型執行個體並啟動辨識：
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  更新包含麥克風陣列幾何設定的下列各行：

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```
    下表說明可用的值：
    
    |變數|意義|可用值|
    |--------|-------|----------------|
    |`DeviceGeometry`|實體麥克風設定|針對圓形裝置套件：`Circular6+1` |
    |||針對線性裝置套件：`Linear4`|
    |`SelectedGeometry`|軟體麥克風設定|針對使用所有麥克風的圓形開發套件：`Circular6+1`|
    |||針對使用四個麥克風的圓形開發套件：`Circular3+1`|
    |||針對使用所有麥克風的線性開發套件：`Linear4`|
    |||針對使用兩個麥克風的線性開發套件：`Linear2`|


1.  若要建置應用程式，請在 [Run] \(執行\) 功能表上選擇 [Run 'app'] \(執行「應用程式」\)。 隨即會顯示 [Select Deployment Target] \(選取部署目標\) 對話方塊。 

1. 選取您的裝置，然後選取 [OK] \(確定\)，以將應用程式部署到裝置。

    ![[Select Deployment Target] \(選取部署目標\) 對話方塊](media/speech-devices-sdk/qsg-7.png)
 
1.  「語音裝置 SDK」範例應用程式會啟動，並顯示下列選項：

    ![「語音裝置 SDK」範例應用程式和選項](media/speech-devices-sdk/qsg-8.png)

1. 進行實驗！

## <a name="troubleshooting"></a>疑難排解

### <a name="certificate-failures"></a>憑證失敗

如果您在使用語音服務時發生憑證失敗，請確定裝置的日期與時間正確：

1. 移至 [設定] 。 在 [System] \(系統\) 底下，選取 [Date & time] \(日期與時間\)。

    ![在 [Settings] \(設定\) 底下，選取 [Date & time] \(日期與時間\)](media/speech-devices-sdk/qsg-12.png)

1. 將 [Automatic date & time] \(自動設定日期與時間\) 選項保持選取。 在 [Select time zone] \(選取時區\) 底下，選取您目前的時區。 

    ![選取日期與時區選項](media/speech-devices-sdk/qsg-13.png)

    當您看到開發套件的時間與您電腦的時間相符時，即表示開發套件已連線到網際網路。 
    
    如需更多開發資訊，請參閱 [ROOBO 開發指南](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf) \(英文\)。

### <a name="audio"></a>音訊

ROOBO 提供一種可將所有的音訊擷取到快閃記憶體的工具。 它可以協助您針對音訊問題進行疑難排解。 針對每個開發套件設定提供了工具版本。 在 [ROOBO 網站](http://ddk.roobo.com/)上選取您的裝置，然後選取頁面底部的 [ROOBO Tools] \(ROOBO 工具\) 連結。
