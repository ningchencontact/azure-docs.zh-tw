---
title: 從 web 應用程式中的 Azure IoT 中樞的感應器資料的即時資料視覺效果 |Microsoft Docs
description: 使用 web 應用程式來視覺化收集自感應器並傳送至 Iot 中樞的溫度和溼度資料。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 22b15a95e529d4f09560e9b7e59d9f78f70651bc
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476020"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>從 web 應用程式中的 Azure IoT 中樞的即時感應器資料視覺化

![端對端圖表](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>您學到什麼

在本教學課程中，您將了解如何將 IoT 中樞收到與您本機電腦上執行的 node.js web 應用程式的即時感應器資料視覺化。 之後在本機執行 web 應用程式，您可以選擇性地遵循步驟來裝載在 Azure App Service web 應用程式。 如果您想嘗試使用 Power BI 將 IoT 中樞的資料視覺化，請參閱[使用 Power BI 將 Azure IoT 中樞的即時感應器資料視覺化](iot-hub-live-data-visualization-in-power-bi.md)。

## <a name="what-you-do"></a>您要做什麼

* 將取用者群組新增至您的 web 應用程式將用來讀取感應器資料的 IoT 中樞
* 從 GitHub 下載 web 應用程式程式碼
* 檢查 web 應用程式程式碼
* 設定環境變數來保存您的 web 應用程式所需的成品的 IoT 中樞
* 在您的開發電腦上執行的 web 應用程式
* 開啟網頁，以查看您的 IoT 中樞的即時溫度和溼度資料
* （選擇性）使用 Azure CLI 來裝載在 Azure App Service web 應用程式

## <a name="what-you-need"></a>您需要什麼

* 完成[Raspberry Pi 線上模擬器](iot-hub-raspberry-pi-web-simulator-get-started.md)教學課程中，或其中一個裝置教學課程中，例如[搭配 node.js 的 Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md)。 這些會涵蓋下列需求：

  * 作用中的 Azure 訂用帳戶
  * 您訂用帳戶之下的 IoT 中樞
  * 將訊息傳送到 IoT 中樞的用戶端應用程式

* [下載 Git](https://www.git-scm.com/downloads)

* 這篇文章中的步驟假設 Windows 開發電腦;不過，您可以輕鬆地執行這些步驟在 Linux 系統上在您慣用的殼層。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

執行下列命令，將適用於 Azure CLI 的 Microsoft Azure IoT 擴充功能新增至您的 Cloud Shell 執行個體。 IoT 擴充功能可將 IoT 中樞、IoT Edge 和 IoT 裝置佈建服務的特定命令新增至 Azure CLI。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>將取用者群組新增至 IoT 中樞

[取用者群組](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers)提供獨立的檢視事件資料流可讓應用程式與 Azure 服務來單獨使用相同的事件中樞端點的資料。 在本節中，您可以新增取用者群組至 web 應用程式將用來讀取資料的 IoT 中樞的內建端點。

執行下列命令以將取用者群組新增至您的 IoT 中樞的內建端點：

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

請注意下您所選擇的名稱，您將需要稍後在本教學課程。

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>取得您的 IoT 中樞服務的連接字串

IoT 中樞會建立數個預設的存取原則。 其中一種原則是**服務**原則，可提供足夠的權限來讀取和寫入 IoT 中樞端點的服務。 執行下列命令，以取得服務的原則會遵守您 IoT 中樞連接字串：

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

連接字串看起來應該如下所示：

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

記下服務連接字串中，您將本教學課程稍後需要它。

## <a name="download-the-web-app-from-github"></a>從 GitHub 下載 web 應用程式

開啟命令視窗，並輸入下列命令，以從 GitHub 下載範例，並將 範例目錄：

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>檢查 web 應用程式程式碼

從 web-apps-node-iot-hub-data-visualization 目錄中，開啟您最愛的編輯器中的 web 應用程式。 下圖顯示在 VS Code 中檢視的檔案結構：

![Web 應用程式檔案結構](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

請花一點時間來檢查下列檔案：

* **Server.js**初始化 web 通訊端以及事件中樞的包裝函式類別的服務端指令碼。 它提供的類別會使用廣播 web 通訊端的內送訊息的事件中樞包裝函式類別的回呼。

* **事件-中樞-reader.js**是連線至 IoT 中樞的內建的端點，使用指定的連接字串和取用者群組的伺服器端指令碼。 它會從傳入訊息上的中繼資料中擷取的 DeviceId 和 EnqueuedTimeUtc 然後轉送使用 server.js 所註冊的回呼方法的訊息。

* **圖表-裝置-data.js** web 通訊端上接聽，追蹤的每個的 DeviceId，並將每個裝置的內送資料的最後一個 50 點的用戶端指令碼。 它然後將選取的裝置資料圖表物件繫結。

* **Index.html**處理網頁 UI 配置，並參考必要的指令碼，用戶端端邏輯。

## <a name="configure-environment-variables-for-the-web-app"></a>設定 web 應用程式的環境變數

若要從您的 IoT 中樞讀取資料，web 應用程式需要您的 IoT 中樞連接字串和它應該閱讀的取用者群組的名稱。 它會從處理序環境，在 server.js 中的下列行中取得這些字串：

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

在命令視窗中，使用下列命令設定環境變數。 將預留位置值取代為您的 IoT 中樞和您先前建立的取用者群組名稱的服務連接字串。 不加上引號的字串。

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>執行 Web 應用程式

1. 請確定您的裝置是執行，並將資料傳送。

2. 在 [命令] 視窗中，執行下列行以下載及安裝參考的套件，然後啟動網站：

   ```cmd
   npm install
   npm start
   ```

3. 您應該會看到在主控台中，指出 web 應用程式已成功連線到您的 IoT 中樞並接聽連接埠 3000 的輸出：

   ![在主控台上啟動的 web 應用程式](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>開啟網頁，以查看您的 IoT 中樞的資料

開啟瀏覽器並前往`http://localhost:3000`。

在 **選取裝置**清單中，選取您的裝置，若要查看執行繪圖，最後一個包含 50 個裝置所傳送至 IoT 中樞的溫度和溼度資料點。

![顯示即時溫度和溼度的 Web 應用程式 (web app) 頁面](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

您也應該會看到在主控台中，瀏覽器用戶端會顯示您的 web 應用程式正在廣播訊息的輸出：  

![Web 應用程式廣播主控台上的輸出](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>裝載在 App Service 中的 web 應用程式

[Azure App Service Web Apps 功能](https://docs.microsoft.com/azure/app-service/overview)裝載 web 應用程式提供的平台即服務 (PAAS)。 Azure App Service 中裝載的 web 應用程式可以受益於功能強大的 Azure 功能，例如額外的安全性，負載平衡和延展性以及 Azure 和協力廠商 DevOps 持續部署、 套件管理等解決方案，並依此類推。 Azure App Service 支援許多受歡迎的語言開發和部署 Windows 或 Linux 基礎結構上的 web 應用程式。

在本節中，您可以佈建 App Service 中的 web 應用程式，並藉由使用 Azure CLI 命令，將您的程式碼部署到它。 您可以找到所使用的命令的詳細資料[az webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest)文件。 開始之前，請確定您已完成的步驟[新增至 IoT 中樞的資源群組](#add-a-consumer-group-to-your-iot-hub)，[取得您的 IoT 中樞服務的連接字串](#get-a-service-connection-string-for-your-iot-hub)，和[從 GitHub下載web應用程式](#download-the-web-app-from-github).

1. [App Service 方案](https://docs.microsoft.com/azure/app-service/overview-hosting-plans)定義一組来執行的 App Service 中裝載的應用程式的計算資源。 本教學課程中，我們會使用開發人員/免費層來裝載 web 應用程式。 在免費層，web 應用程式會與其他 App Service 應用程式，包括應用程式的其他客戶共用的 Windows 資源上執行。 Azure 也部署在 Linux 上的 web 應用程式的 App Service 方案的供應項目計算資源。 如果您已經有您想要使用 App Service 方案，您可以略過此步驟。

   若要建立使用 Windows 的免費層的 App Service 方案，請執行下列命令。 使用您的 IoT 中樞位於相同的資源群組。 您的服務方案名稱可以包含小寫字母、 數字及連字號。

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. 現在，佈建您的 App Service 方案中的 web 應用程式。 `--deployment-local-git`參數可讓 web 應用程式的上傳並從本機電腦上的 Git 存放庫部署程式碼。 您的 web 應用程式名稱必須是全域唯一的而且可包含小寫字母、 數字及連字號。

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --deployment-local-git
   ```

3. 現在加入應用程式設定環境變數會指定 IoT 中樞連接字串和事件中樞取用者群組。 個別設定會以空格分隔在`-settings`參數。 您的 IoT 中樞和取用者群組，您先前在本教學課程中建立使用服務連接字串。 不加上引號的值。

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. 啟用 Web 通訊端通訊協定的 web 應用程式，並設定 web 應用程式接收 HTTPS 只要求 （HTTP 要求會重新導向至 HTTPS）。

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. 若要將程式碼部署至 App Service 中，您將使用您[使用者層級部署認證](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials)。 您的使用者層級部署認證不同於您的 Azure 認證，並用於本機 Git 和 FTP 部署至 web 應用程式。 設定之後，它們是有效的 App Service 應用程式在您的 Azure 帳戶中的所有訂用帳戶中所有。 如果您先前已經設定使用者層級部署認證，您可以使用它們。

   如果您先前尚未設定使用者層級部署認證，或您不記得密碼，請執行下列命令。 您部署的使用者名稱必須是唯一在 Azure 內，而且不能包含 ' @' 符號的本機 Git 推送。 當系統提示您輸入並確認新密碼。 密碼長度必須至少為 8 個字元，包含下列三個元素其中兩個：字母、數字及符號。

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. 取得用來推送您的程式碼，App Service 到 Git URL。

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. 將遠端新增至您參考 App Service 中 web 應用程式的 Git 存放庫的複本中。 針對\<Git 複製 URL\>，使用上一個步驟中所傳回的 URL。 在命令視窗中執行下列命令。

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. 若要將程式碼部署至 App Service，請在命令視窗中輸入下列命令。 如果系統提示您輸入認證，請輸入您在步驟 5 中建立的使用者層級部署認證。 請確定您將推送至 App Service 遠端的主要分支。

    ```cmd
    git push webapp master:master
    ```

9. 在命令視窗中，將更新部署的進度。 成功的部署會結束，類似下列輸出：

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. 執行下列命令來查詢您的 web 應用程式的狀態，並確定它正在執行：

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. 在瀏覽器中，瀏覽至 `https://<your web app name>.azurewebsites.net` 。 網頁上類似於您所見，當您執行 web 應用程式在本機顯示。 假設您的裝置是執行，且傳送資料，您應該會看到執行 50 最新溫度和溼度的數據的裝置所傳送的繪圖。

## <a name="troubleshooting"></a>疑難排解

如果您遇到任何問題，此範例，請嘗試下列各節中的步驟。 如果您仍有問題時，傳送給我們在本主題底部提供意見反應。

### <a name="client-issues"></a>用戶端問題

* 如果裝置未顯示在清單中，或任何圖表所繪製，請確定您的裝置上執行裝置程式碼。

* 在瀏覽器中，開啟 開發人員工具 （在許多瀏覽器中按 F12 鍵會開啟它），並尋找  主控台。 尋找任何警告或錯誤那里列印。

* 您可以偵錯 /js/chat-device-data.js 中的用戶端指令碼。

### <a name="local-website-issues"></a>本機網站問題

* 觀看您用來啟動節點提供主控台輸出視窗中的輸出。

* 偵錯伺服器程式碼，尤其是 server.js 和 /scripts/event-hub-reader.js。

### <a name="azure-app-service-issues"></a>Azure App Service 的問題

* 在 Azure 入口網站中，移至您的 web 應用程式。 底下**監視**的左窗格中，選取**App Service 記錄檔**。 開啟**的應用程式記錄 （檔案系統）** 上，設定**層級**到錯誤，然後再選取**儲存**。 然後開啟**記錄檔資料流**(底下**監視**)。

* 從 Azure 入口網站中您 web 應用程式底下**開發工具**選取**主控台**和驗證使用的節點與 npm 版本`node -v`和`npm -v`。

* 如果您看到找不到封裝的相關錯誤，您可以執行的步驟順序。 站台部署時 (以`git push`) 執行應用程式服務`npm install`，它已設定執行節點的目前版本為基礎。 如果，在更新版本的組態變更，您必須對程式碼中的無意義的變更並再次推送。

## <a name="next-steps"></a>後續步驟

您已成功使用 Web 應用程式 (web app) 將 IoT 中樞的即時感應器資料視覺化。

另一種方式，以視覺化方式檢視來自 Azure IoT 中樞的資料，請參閱 <<c0> [ 使用 Power BI 將來自 IoT 中樞的即時感應器資料視覺化](iot-hub-live-data-visualization-in-power-bi.md)。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
