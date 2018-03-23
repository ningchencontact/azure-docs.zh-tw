---
title: 使用 Azure Functions 取出 Twitter 訊息 | Microsoft Docs
description: 使用動作感應器來偵測搖動，並使用 Azure Functions 來尋找包含您指定之主題標籤的隨機推文。
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: d9d03d35aa5d78d83e0f195c804cfe09fece3c07
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>搖一搖以獲取推文 -- 使用 Azure Functions 來取出 Twitter 訊息！

在此專案中，您會了解如何使用動作感應器，利用 Azure Functions 來觸發事件。 應用程式會取出具有您在 Arduino 草圖中所設定之 #主題標籤的隨機推文。 此推文會顯示在 DevKit 畫面上。

## <a name="what-you-need"></a>您需要什麼

完成[使用者入門指南](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)以便：

* 讓 DevKit 連線至 Wi-Fi。
* 準備開發環境。

有效的 Azure 訂用帳戶。 如果沒有，您可以透過下列這些方法之一來註冊：

* 啟動 [30 天免費試用 Microsoft Azure 帳戶](https://azureinfo.microsoft.com/us-freetrial.html)
* 如果您是 MSDN 或 Visual Studio 訂閱者，請認領您的 [Azure 點數](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)

## <a name="open-the-project-folder"></a>開啟專案資料夾

### <a name="start-vs-code"></a>啟動 VS Code

- 確定 DevKit **未**連線至您的電腦。
- 啟動 VS Code。
- 將 DevKit 連接到您的電腦。

VS Code 會自動尋找您的 DevKit，並顯示簡介頁面：

![mini-solution-vscode](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_start.png)

> [!NOTE]
> 當您啟動 VS Code 時，您可能會收到錯誤訊息，指出找不到 Arduino IDE 或相關的面板套件。 如果發生這個錯誤，請關閉 VS Code，然後再次啟動 Arduino IDE。 VS Code 現在應該就會正確找到 Arduino IDE 路徑。

### <a name="open-arduino-examples-folder"></a>開啟 Arduino 範例資料夾

展開左邊的 [ARDUINO 範例] 區段，瀏覽至 [MXCHIP AZ3166 的範例] > [AzureIoT]，然後選取 [ShakeShake]。 隨即開啟一個內含專案資料夾的新 VS Code 視窗。

![mini-solution-examples](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

如果您不小心關閉該窗格，可予以重新開啟。 請使用 `Ctrl+Shift+P` (macOS：`Cmd+Shift+P`) 來開啟命令選擇區，輸入 **Arduino**，然後尋找並選取 [Arduino: Examples] \(Arduino: 範例\)。

## <a name="provision-azure-services"></a>佈建 Azure 服務

在解決方案視窗中，輸入 `task cloud-provision` 以 `Ctrl+P` (macOS：`Cmd+P`) 執行您的工作。

VS Code 終端機中會有互動式命令列來引導您佈建所需的 Azure 服務：

![cloud-provision](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> 如果頁面在嘗試登入 Azure 時於載入中狀態停止回應，請參閱此 [常見問題集步驟]({{"/docs/faq/#page-hangs-when-log-in-azure" | 
 
## <a name="modify-the-hashtag"></a>修改 #主題標籤

開啟 `ShakeShake.ino` 並尋找此程式碼：

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

將大括號內的字串 `iot` 取代為您想要的主題標籤。 DevKit 稍後會取出隨機推文，當中包含您在此步驟中指定的主題標籤。

## <a name="deploy-azure-functions"></a>部署 Azure Functions

使用 `Ctrl+P` (macOS：`Cmd+P`) 來執行 `task cloud-deploy`，以便開始部署 Azure Functions 程式碼：

![cloud-deploy](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> 有時候，Azure Functions 可能無法正常運作。 若要在發生此問題時加以解決，請查看這個[常見問題集步驟](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function)。

## <a name="build-and-upload-the-device-code"></a>組建並上傳裝置程式碼

### <a name="windows"></a>Windows

1. 使用 `Ctrl+P` 來執行 `task device-upload`。
2. 終端機會提示您進入設定模式。 若要這樣做：

   * 按住 A 按鈕
   * 按下再放開 [重設] 按鈕。

3. 畫面會顯示 DevKit 識別碼和 [設定]。
4. 這會設定從 `task cloud-provision` 步驟中取出的連接字串。
5. 然後，VS Code 會開始驗證 Arduino 草圖並將其上傳至您的 DevKit：![device-upload](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)
6. DevKit 會重新開機，然後開始執行程式碼。

> [!NOTE]
> 您可能會收到「錯誤：AZ3166：未知的套件」錯誤訊息。 未正確重新整理面板套件索引時，就會發生此錯誤。 若要解決此問題，請查看這個[常見問題集步驟](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)。

### <a name="macos"></a>macOS

1. 讓 DevKit 進入設定模式：按住 A 按鈕，然後按下再放開 [重設] 按鈕。 畫面會顯示 [Configuraiton] \(設定\)。
2. 使用 `Cmd+P` 來執行 `task device-upload`，以便設定從 `task cloud-provision` 步驟中取出的連接字串。
3. 然後，VS Code 會開始驗證 Arduino 草圖並將其上傳至您的 DevKit：![device-upload](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)
4. DevKit 會重新開機，然後開始執行程式碼。

> [!NOTE]
> 您可能會收到「錯誤：AZ3166：未知的套件」錯誤訊息。 未正確重新整理面板套件索引時，就會發生此錯誤。 若要解決此問題，請查看這個[常見問題集步驟](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)。

## <a name="test-the-project"></a>測試專案

應用程式初始化之後，按一下再放開 A 按鈕，然後輕輕搖動 DevKit 面板。 這個動作會取出隨機推文，其中包含您稍早指定的主題標籤。 在幾秒內，DevKit 畫面上便會顯示推文：

### <a name="arduino-application-initializing"></a>Arduino 應用程式正在初始化...
![Arduino-application-initializing](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>按 A 以搖動...
![Press-A-to-shake](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>準備好搖動...
![Ready-to-shake](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>處理中...
![Processing](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>按 B 以讀取...
![Press-B-to-read](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>顯示隨機推文...
![Display-a-random-tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- 再按一次 A 按鈕，然後搖動以取得新推文。
- 按 B 按鈕以捲動瀏覽推文的其餘部分。

## <a name="how-it-works"></a>運作方式

![圖表](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Arduino 草圖會將事件傳送至 Azure IoT 中樞。 此事件會觸發 Azure Functions 應用程式。 Azure Functions 應用程式所包含的邏輯可用來連線到 Twitter 的 API 並取出推文。 接著，它會將推文的文字包裝成 C2D (雲端到裝置) 訊息，並將該訊息傳回給裝置。

## <a name="optional-use-your-own-twitter-bearer-token"></a>選擇性：使用您自己的 Twitter 持有人權杖

為了進行測試，此專案範例會使用預先設定的 Twitter 持有人權杖。 不過，每個 Twitter 帳戶都有[速率限制](https://dev.twitter.com/rest/reference/get/search/tweets)。 如果您要考慮使用您自己的權杖，請遵循下列步驟：

1. 移至 [Twitter 開發人員入口網站](https://dev.twitter.com/)以註冊新的 Twitter 應用程式。

2. [取得您應用程式的取用者金鑰和取用者祕密](https://support.yapsody.com/hc/en-us/articles/203068116-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-)。

3. 使用[某些公用程式](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/)從這兩個金鑰產生 Twitter 持有人權杖。

4. 在 [Azure 入口網站](https://portal.azure.com/){:target="_blank"} 中，進入 [資源群組] 並找出您 "Shake, Shake" 專案的 Azure Functions (類型：App Service)。 此名稱一律會包含 'shake...' 字串。
  ![azure-function](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. 使用您自己的權杖在 [Functions > shakeshake-cs] 內更新 `run.csx` 的程式碼：
  ```csharp
  ...
  string authHeader = "Bearer " + "[your own token]";
  ...
  ```
  ![twitter-token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. 儲存檔案，然後按一下 [執行]。


## <a name="problems-and-feedback"></a>問題與意見反應

### <a name="the-screen-displays-no-tweets-while-every-step-has-run-successfully"></a>每個步驟都已順利執行，畫面卻顯示「沒有推文」

在您第一次部署及執行範例時常會發生這種狀況，其原因是函式應用程式需要數秒鐘到一分鐘之久的時間來將應用程式冷啟動。 或者，在執行程式碼時，發生了一些問題而導致應用程式重新啟動。 發生這種狀況時，裝置的應用程式會在擷取推文時逾時。 在此情況下，您可以嘗試一或兩種下列方法來解決此問題：

1. 按一下 DevKit 上的 [重設] 按鈕，以便再次執行裝置的應用程式。

2. 在 [Azure 入口網站](https://portal.azure.com/)中，尋找您所建立的 Azure Functions 應用程式，然後將它重新啟動：![azure-function-restart](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>意見反應

如果您遇到其他問題，請參閱[常見問題集](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)或透過下列管道與我們連絡：

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>後續步驟

現在您已了解如何將 DevKit 裝置連線到 Azure IoT 套件並取出推文，以下是建議的後續步驟：

* [Azure IoT 套件概觀](https://docs.microsoft.com/azure/iot-suite/)