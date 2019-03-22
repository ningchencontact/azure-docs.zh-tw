---
title: 在本機部署遠端監視解決方案 (透過 IntelliJ IDE) - Azure | Microsoft Docs
description: 本操作指南示範如何使用 IntelliJ 將遠端監視解決方案加速器部署到本機電腦，以進行測試和開發。
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: ccdb3e16ea60cf85ae28e533e3b2d9f473cc90c8
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316383"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>在本機部署遠端監視解決方案加速器 - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

本文會示範如何將遠端監視解決方案加速器部署到本機電腦，以進行測試和開發。 您會了解如何在 IntelliJ 中執行微服務。 本機微服務部署會使用下列雲端服務：IoT 中樞、Cosmos DB、Azure 串流分析，以及雲端中的 Azure 時間序列深入解析服務。

如果您想要在本機電腦上執行 Docker 中的遠端監視解決方案加速器，請參閱[本機部署遠端監視解決方案加速器 - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md)。

## <a name="prerequisites"></a>必要條件

若要部署遠端監視解決方案加速器所使用的 Azure 服務，您需要一個有效的 Azure 訂用帳戶。

如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

### <a name="machine-setup"></a>電腦設定

若要完成本機部署，您必須在本機開發機器上安裝下列工具：

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ 社群版](https://www.jetbrains.com/idea/download/)
* [IntelliJ 外掛程式 Scala](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ 外掛程式 SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ 外掛程式 SBT Executor](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) - 此軟體是指令碼用來建立 Azure 資源之 PCS CLI 的先決條件。 不要使用 Node.js v10。

> [!NOTE]
> IntelliJ IDE 適用於 Windows 和 Mac。

[!INCLUDE [iot-accelerators-local-setup-java](../../includes/iot-accelerators-local-setup-java.md)]

## <a name="run-the-microservices"></a>執行微服務

在本節中，您會執行遠端監視微服務。 您會以原生方式執行 Web UI、在 Docker 中執行「裝置模擬」、「驗證」和「ASA 管理員」，以及在 IntelliJ 中執行微服務。

### <a name="run-the-device-simulation-service"></a>執行裝置模擬服務

開啟新的命令提示字元視窗，以確保能夠存取上一節中 **start.cmd** 指令碼設定的環境變數。

執行下列命令以啟動裝置模擬服務的 Docker 容器。 該服務會模擬遠端監視解決方案的裝置。

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>執行驗證服務

請開啟一個新的命令提示字元視窗，然後執行下列命令來啟動「驗證」服務的 Docker 容器。 此服務可讓您管理獲授權存取「Azure IoT 解決方案」的使用者。

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-asa-manager-service"></a>執行 ASA 管理員服務

請開啟一個新的命令提示字元視窗，然後執行下列命令來啟動「ASA 管理員」服務的 Docker 容器。 此服務可讓您管理「Azure 串流分析」(ASA) 作業，包括設定組態，以及啟動、停止和監視其狀態。

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>在本機電腦上部署所有其他微服務

下列步驟示範如何在 IntelliJ 中執行「遠端監視」微服務：

#### <a name="import-project"></a>匯入專案

1. 啟動 IntelliJ IDE
1. 選取 [Import Project] \(匯入專案\)，然後選擇 **azure-iot-pcs-remote-monitoring-java\services\build.sbt**

#### <a name="create-run-configurations"></a>建立執行設定

1. 選取 [Run] \(執行\) > [Edit Configurations] \(編輯設定\)
1. 選取 [Add New Configuration] \(新增設定\) > [sbt task] \(SBT 工作\) 
1. 輸入 [Name] \(名稱\)，然後在 [Tasks] \(工作\) 中輸入 run 
1. 根據您想要執行的服務，選取 [Working Directory] \(工作目錄\)
1. 按一下 [Apply] \(套用\) > [Ok] \(確定\) 以儲存您的選擇。
1. 為下列服務建立執行設定：
    * WebService (services\config)
    * WebService (services\device-telemetry)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

舉例來說，下圖顯示為某個服務新增設定：

[![新增設定](./media/deploy-locally-intelliJ/run-configurations.png)](./media/deploy-locally-intelliJ/run-configurations.png#lightbox)


#### <a name="create-compound-configuration"></a>建立複合設定

1. 若要將所有服務一起執行，請選取 [Add new Configuration] \(新增設定\) > [Compound] \(複合\)
1. 輸入 [Name] \(名稱\)，然後**新增 SBT 工作**
1. 按一下 [Apply] \(套用\) > [Ok] \(確定\) 以儲存您的選擇。

舉例來說，下圖顯示將所有 SBT 工作新增至單一設定：


[![新增所有服務](./media/deploy-locally-intelliJ/all-services.png)](./media/deploy-locally-intelliJ/all-services.png#lightbox)



1. 按一下 [Run] \(執行\)以在本機電腦上建置並執行 Web 服務。

每個 Web 服務都會開啟一個命令提示字元和 Web 瀏覽器視窗。 您可以在命令提示字元中看到執行中服務的輸出，還有瀏覽器視窗可讓您監視狀態。 請不要關閉命令提示字元或網頁，這個動作會停止 Web 服務。


若要存取服務的狀態，您可以瀏覽至下列 URL：
* IoT 中樞管理員 [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* 裝置遙測 [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* 設定 [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* 儲存裝置介面卡 [http://localhost:9022/v1/status](http://localhost:9022/v1/status)


### <a name="start-the-stream-analytics-job"></a>啟動串流分析工作

請遵循下列步驟以啟動串流分析作業：

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至為解決方案建立的**資源群組**。 當您執行 **start.cmd** 指令碼**時為解決方案選擇的名稱，就是資源群組的名稱。
1. 在資源清單中，按一下 [串流分析作業]。
1. 在串流分析作業 [概觀] 頁面上，按一下 [啟動] 按鈕。 接著，按一下 [啟動] 以立即啟動作業。

### <a name="run-the-web-ui"></a>執行 Web UI

在此步驟中，您會啟動 Web UI。 開啟新的命令提示字元視窗，以確保能夠存取 **start.cmd** 指令碼設定的環境變數。 瀏覽到存放庫本機複本中的 **webui** 資料夾，然後執行下列命令：

```cmd
npm install
npm start
```

啟動完成時，您的瀏覽器會顯示頁面**http:\//localhost:3000 / 儀表板**。 此頁面上的錯誤一如預期。 若要檢視沒有錯誤的應用程式，請完成下列步驟。

### <a name="configure-and-run-nginx"></a>設定和執行 NGINX

設定反向 Proxy 伺服器，以連結 Web 應用程式與在本機電腦上執行的微服務：

* 將 **nginx.conf** 檔案從存放庫本機複本中的 **webui\scripts\localhost** 資料夾複製到 **nginx\conf** 安裝目錄。
* 執行 **nginx**。

如需執行 **nginx** 的詳細資訊，請參閱[適用於 Windows 的 nginx](https://nginx.org/en/docs/windows.html) \(英文\)。

### <a name="connect-to-the-dashboard"></a>連線至儀表板

若要存取遠端監視解決方案儀表板，請瀏覽至 http:\//localhost:9000 瀏覽器中的。

## <a name="clean-up"></a>清除

為避免不必要的費用，當您完成測試後，請從您的 Azure 訂用帳戶中移除雲端服務。 若要移除服務，請瀏覽到 [Azure 入口網站](https://ms.portal.azure.com)，然後刪除 **start.cmd** 指令碼建立的資源群組。

您也可以將您從 GitHub 複製原始程式碼時，所建立的遠端監視存放庫本機副本刪除。

## <a name="next-steps"></a>後續步驟

既然您已部署遠端監視解決方案，下一步便是[探索解決方案儀表板的功能](quickstart-remote-monitoring-deploy.md)。
