---
title: 開發及偵錯在 Visual Studio-Azure IoT Edge 模組 |Microsoft Docs
description: 使用 Visual Studio 2019 開發和偵錯適用於 Azure IoT Edge 的模組
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/27/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 96a67a9a593655b3b187fe1bb0decfc7252d2d10
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252997"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge-preview"></a>使用 Visual Studio 2019 開發和偵錯模組，適用於 Azure IoT Edge （預覽）

您可以將商務邏輯轉換成 Azure IoT Edge 的模組。 本文說明如何使用 Visual Studio 2019 作為主要工具，開發和偵錯模組。

Azure IoT Edge Tools for Visual Studio 提供下列優點：

- 在本機開發電腦上建立、編輯、建置、執行 Azure IoT Edge 解決方案和模組並針對其進行偵錯。
- 透過 Azure IoT 中樞將您的 Azure IoT Edge 解決方案部署到 Azure IoT Edge 裝置。
- 您的 Azure IoT 模組 C 中的程式碼或C#時將所有的 Visual Studio 開發的優點。
- 使用 UI 管理 Azure IoT Edge 裝置與模組。

本文說明如何使用 Azure IoT Edge Tools for Visual Studio 2019 來開發 IoT Edge 模組。 您也將學習如何將專案部署到 Azure IoT Edge 裝置。

> [!TIP]
> Visual Studio 所建立的 IoT Edge 專案結構與 Visual Studio Code 中的 IoT Edge 專案結構不同。
  
## <a name="prerequisites"></a>必要條件

此文章假設您使用執行 Windows 的電腦或虛擬機器作為開發電腦。 您的 IoT Edge 裝置可以是另一部實體裝置。

因為這篇文章會使用 Visual Studio 2019 作為主要開發工具，請安裝 Visual Studio。 請確定您包含**Azure 開發**並**使用的桌面開發C++**  Visual Studio 2019 安裝中的工作負載。 您可以[修改 Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019)來新增必要的工作負載。

您的 Visual Studio 2019 備妥之後，您還需要下列工具和元件：

- 下載並安裝[Azure IoT Edge 工具 （預覽）](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)從 Visual Studio marketplace 建立 IoT Edge 專案在 Visual Studio 2019。

> [!TIP]
> 如果您使用 Visual Studio 2017，plrease 下載並安裝[Azure IoT Edge 工具 （預覽）](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)適用於 VS 2017 的 Visual Studio marketplace

- 在開發電腦下載並安裝 [Docker Community Edition](https://docs.docker.com/install/)，以建置並執行您的模組映像。 您必須設定 Docker CE 在 Linux 容器模式或 Windows 容器模式中執行。

- 安裝 [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/)，以便設定本機開發環境以偵錯、執行和測試您的 IoT Edge 解決方案。 在您的終端機中執行下列命令，先安裝 [Python (2.7/3.6) 和 Pip](https://www.python.org/)，再安裝 **iotedgehubdev** 封裝。 確定您的 Azure IoT EdgeHub Dev Tool 版本大於 0.3.0。

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- 複製儲存機制和安裝 Vcpkg 程式庫管理員 中，然後再安裝**azure iot sdk-c 封裝**for Windows。

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 或 [Docker 中樞](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。

  > [!TIP]
  > 您可以使用本機 Docker 登錄作為原型並用於測試，而非使用雲端登錄。

- 若要在裝置上測試模組，您需要一個有效的 IoT 中樞，而且該中樞中至少必須有一個 IoT Edge 裝置。 若要使用您的電腦作為 IoT Edge 裝置，請遵循 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 快速入門中的步驟。 如果您在開發電腦上執行 IoT Edge 精靈，您可能必須先停止 EdgeHub 與 EdgeAgent，才能開始在 Visual Studio 中進行開發。

### <a name="check-your-tools-version"></a>檢查您的工具版本

1. 從 [工具]  功能表中，選取 [擴充功能和更新]  。 依序展開 [已安裝] > [工具]  ，您可以找到 **Azure IoT Edge Tools** 和 **Cloud Explorer for Visual Studio**。

1. 記下已安裝的版本。 您可以將此版本與 Visual Studio Marketplace 上的最新版本進行比較 ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019)、[Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. 如果您的版本比 Visual Studio Marketplace 提供的版本舊，請依照下一節中的說明在 Visual Studio 中更新您的工具。

### <a name="update-your-tools"></a>更新您的工具

1. 在 [擴充功能和更新]  對話方塊中，展開 [更新] > [Visual Studio Marketplace]  ，選取 [Azure IoT Edge Tools]  或 [Cloud Explorer for Visual Studio]  ，然後選取 [更新]  。

1. 下載工具更新之後，請關閉 Visual Studio，以使用 VSIX 安裝程式觸發工具更新。

1. 在安裝程式中選取 [確定]  以啟動，然後選取 [修改]  以更新工具。

1. 更新完成後，請選取 [關閉]  並重新啟動 Visual Studio。

### <a name="create-an-azure-iot-edge-project"></a>建立 Azure IoT Edge 專案

Visual Studio 中的 Azure IoT Edge 專案範本可建立可以在 Azure IoT 中樞中部署到 Azure IoT Edge 裝置的專案。 首先您建立 Azure IoT Edge 方案，然後在該解決方案中產生的第一個模組， 每個 IoT Edge 解決方案都可以包含多個模組。

1. 在 Visual Studio 新專案對話方塊中，搜尋並選取**Azure IoT Edge**，輸入您專案的名稱和指定的位置，然後選取**確定**。 預設專案名稱是 **AzureIoTEdgeApp1**。

1. 在 **新增 IoT Edge 應用程式和模組**視窗中，選取**Linux Amd64**， **Windows Amd64**，或兩者皆為應用程式平台。 如果您選取，您會建立具有每個參考的預設程式碼模組的兩個專案的方案。

   > [!TIP]
   > Visual Studio 的 Azure IoT Edge 擴充功能目前不支援建立專案，適用於 ARM 平台。 請參閱此[IoT 開發人員部落格文章](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/)如需使用 Visual Studio Code 的 ARM32v7/armhf 開發解決方案的範例。

1. 選取   **C#模組**或是**C 模組**，然後指定您的模組名稱和模組映像儲存機制。 Visual Studio 會自動以 **localhost:5000/<您的模組名稱\>** 填入模組名稱。 請使用您自己的登錄資訊加以取代。 如果您使用本機 Docker 登錄來進行測試，則可以使用 **localhost**。 如果您使用 Azure Container Registry，則請使用登錄設定中的登入伺服器。 登入伺服器看起來像 * **\<登錄名稱\>*.azurecr.io**。 僅取代字串的 **localhost:5000** 部分即可，因此最後的結果看起來像 * *\<* 登錄名稱 *\>.azurecr.io/* \<您的模組名稱\>***。 預設的模組名稱是 **IoTEdgeModule1**

1. 選取  **確定**來建立 Azure IoT Edge 解決方案使用的模組C#或 c

您現在可以**AzureIoTEdgeApp1.Linux.Amd64**專案或**AzureIoTEdgeApp1.Windows.Amd64**專案，或兩者，以及**IoTEdgeModule1**專案中您解決方案。 每個**AzureIoTEdgeApp1**專案具有`deployment.template.json`檔案，其中定義想要建置及部署您的 IoT Edge 方案的模組，而且也會定義模組之間的路由。 預設解決方案具有 **tempSensor** 模組與 **IoTEdgeModule1** 模組。 **tempSensor** 模組會產生 **IoTEdgeModule1** 模組的模擬資料，而 **IoTEdgeModule1** 模組中的預設程式碼會直接將收到的訊息透過管道傳送到 Azure IoT 中樞。

**IoTEdgeModule1** 專案是 .NET Core 2.1 主控台應用程式。 它包含執行 Windows 容器或 Linux 容器之 IoT Edge 裝置所需的 Dockerfiles。 `module.json`檔案描述模組中繼資料。 實際的模組程式碼，其使用 Azure IoT 裝置 SDK，為相依性： 位於`Program.cs`或`main.c`檔案。

## <a name="develop-your-module"></a>開發您的模組

解決方案提供的預設模組程式碼位於**IoTEdgeModule1** > **Program.cs** (如C#) 或**main.c** (C)。 模組和`deployment.template.json`檔案，讓您可以建置方案、 將它推送到容器登錄，並將它部署到裝置，以開始測試，不會更動任何程式碼會設定。 模組會建置為採用來源的輸入 (在此案例中，是會模擬資料的 **tempSensor** 模組)，並且將其傳送到 Azure IoT 中樞。

當您準備好自訂模組範本，以您自己的程式碼時時，請使用[Azure IoT 中樞 Sdk](../iot-hub/iot-hub-devguide-sdks.md)建置模組的 IoT 解決方案，例如安全性、 裝置管理和可靠性的索引鍵需要該位址。

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>使用 IoT Edge 裝置連接字串來初始化 iotedgehubdev

1. 從 Visual Studio Cloud Explorer 中的**主要連接字串**，複製任何 IoT Edge 裝置的連接字串。 慎勿複製非 Edge 裝置的連接字串，IoT Edge 裝置的圖示與非 Edge 裝置的圖示不同。

   ![複製 Edge 裝置連接字串](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. 移至**工具** > **Azure IoT Edge 工具** > **安裝 IoT Edge 模擬器**，pasten 連接字串，然後按一下  **確定**.

   ![開啟 [設定 Edge 連接字串] 視窗](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. 輸入第一個步驟中的連接字串，然後選取 [確定]  。

> [!NOTE]
> 您只需要在開發電腦上進行這些步驟一次，因為結果會自動套用至全部後續的 Azure IoT Edge 解決方案。 如果您要變更為不同的連接字串，可以再次進行此程序。

## <a name="build-and-debug-single-module"></a>建置和偵錯單一模組

一般而言，您為想要先針對每個模組進行測試和偵錯，然後在使用多個模組的整個解決方案中執行。

1. 以滑鼠右鍵按一下 **IoTEdgeModule1**，然後選取快顯功能表中的 [設定為啟始專案]  。

   ![設定啟始專案](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. 按下 **F5** 或按一下下面的按鈕執行模組，第一次可能需要 10&ndash;20 秒。

   ![執行模組](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. 如果模組已成功初始化，您應該會看到 .NET Core 主控台應用程式已啟動。

   ![執行中的模組](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. 如果在開發C#，在中設定中斷點`PipeMessage()`函式中**Program.cs**; 如果使用 C 中設定中斷點`InputQueue1Callback()`函式中**main.c**。 您可以藉由執行下列命令傳送訊息，然後測試它**Git Bash**或是**WSL Bash**殼層。 (您無法從 PowerShell 或命令提示字元執行 `curl` 命令。)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![針對單一模組進行偵錯](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    中斷點應該已觸發。 您可以在 Visual Studio [區域變數]  視窗中觀察變數。

   > [!TIP]
   > 您也可以使用 [PostMan](https://www.getpostman.com/) 或其他 API 工具來傳送訊息，而不使用 `curl`。

1. 按 **Ctrl + F5** 或按一下 [停止] 按鈕以停止偵錯。

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>建置使用多個模組的 IoT Edge 解決方案以及針對其進行偵錯

您完成開發單一模組之後，您會想要執行使用多個模組的整個解決方案以及針對其進行偵錯。

1. 加入方案中的第二個模組，以滑鼠右鍵按一下**AzureIoTEdgeApp1** ，然後選取**新增** > **新 IoT Edge 模組**。 第二個模組的預設名稱是 **IoTEdgeModule2**，而且仍然是個別的管道模組。

1. 開啟檔案 `deployment.template.json`，您將在 [模組]  區段中看到已加入 **IoTEdgeModule2**。 以下列程式碼取代 **routes** 區段。 如果您已自訂模組名稱，請確定更新名稱以使其相符。

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

1. 以滑鼠右鍵按一下 **AzureIoTEdgeApp1**，然後選取快顯功能表中的 [設定為啟始專案]  。

1. 建立您的中斷點，然後按下 **F5** 同時執行多個模組並針對其進行偵錯。 您應該看到多個.NET Core 主控台應用程式視窗，代表不同的模組的每個視窗。

   ![針對多個模組進行偵錯](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. 按下 **Ctrl + F5** 或選取 [停止] 按鈕以停止偵錯。

## <a name="build-and-push-images"></a>建置及推送映像

1. 確定 **AzureIoTEdgeApp1** 是啟始專案。 選取 [偵錯]  或 [發行]  做為要對於模組映像建置的設定。

    > [!NOTE]
    > 選擇 [偵錯]  時，Visual Studio 將使用 `Dockerfile.(amd64|windows-amd64).debug` 來建置 Docker 映像。 這包含建置它時容器映像中的 .NET Core 命令列偵錯工具 VSDBG。 對於生產就緒 IoT Edge 模組，建議您使用 [發行]  設定，此設定會使用無 VSDBG 的 `Dockerfile.(amd64|windows-amd64)`。

1. 如果您使用私人登錄，例如 Azure Container Registry，請使用下列 Docker 命令來登入。 如果您使用的是本機登錄，您可以[執行本機登錄](https://docs.docker.com/registry/deploying/#run-a-local-registry)。

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. 如果您使用私人登錄，例如 Azure Container Registry，您需要將檔案登入資訊新增到在檔案 `deployment.template.json` 中找到的執行階段設定。 將您的實際 ACR 系統管理員使用者名稱、密碼和登錄名稱填入預留位置。

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. 以滑鼠右鍵按一下 **AzureIoTEdgeApp1**，並選取 [建置並推送 Edge 解決方案]  ，以便建置並推送每個模組的 Docker 映像。

   ![建置並推送映像](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>部署解決方案

在您用來設定 IoT Edge 裝置的快速入門文章中，您使用 Azure 入口網站部署了模組。 您也可以使用適用於 Visual Studio 的 Cloud Explorer 來部署模組。 您已備妥對於您的案例準備的部署資訊清單 (`deployment.json` 檔案)，您只需要選取要接收部署的裝置即可。

1. 依序按一下 [檢視]   > [Cloud Explorer]  以開啟 **Cloud Explorer**。 請確定您已登入 Visual Studio 2019。

1. 在 **Cloud Explorer** 中，展開您的訂用帳戶，尋找您的 Azure IoT 中樞與您要部署的 Azure IoT Edge 裝置。

1. 以滑鼠右鍵按一下 IoT Edge 裝置為其建立部署，您必須選取 `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json` 下的部署資訊清單檔案。

   > [!NOTE]
   > 您不得選取 `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. 按一下 [重新整理] 按鈕，查看新的模組正在與 **TempSensor** 模組以及 **$edgeAgent** 和 **$edgeHub** 一起執行。

## <a name="view-generated-data"></a>檢視產生的資料

1. 若要監視 D2C 訊息特定裝置，選取裝置清單中，然後按一下**開始監視內建事件端點**中**動作**視窗。

1. 若要停止監視資料，在清單中選取的裝置，然後按**停止監視內建事件端點**中**動作**視窗。

## <a name="next-steps"></a>後續步驟

若要為您的 IoT Edge 裝置開發自訂模組，請[了解及使用 Azure IoT 中樞 SDK](../iot-hub/iot-hub-devguide-sdks.md)。
