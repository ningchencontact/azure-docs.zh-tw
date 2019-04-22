---
title: 在 Visual Studio 中开发和调试模块 - Azure IoT Edge | Microsoft Docs
description: 使用 Visual Studio 2017 开发和调试适用于 Azure IoT Edge 的模块
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 04/03/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f2228726d4edc25efe46a660d25d398959c3ea59
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58851734"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-modules-for-azure-iot-edge-preview"></a>使用 Visual Studio 2017 开发和调试适用于 Azure IoT Edge 的模块（预览）

您可以將商務邏輯轉換成 Azure IoT Edge 的模組。 本文介绍如何使用 Visual Studio 2017 作为主要工具来开发和调试模块。

Azure IoT Edge Tools for Visual Studio 提供下列優點：

- 在本機開發電腦上建立、編輯、建置、執行 Azure IoT Edge 解決方案和模組並針對其進行偵錯。
- 透過 Azure IoT 中樞將您的 Azure IoT Edge 解決方案部署到 Azure IoT Edge 裝置。
- 在 C 或 C# 中编写 Azure IoT 模块的代码，同时兼具 Visual Studio 开发的所有优势。
- 使用 UI 管理 Azure IoT Edge 裝置與模組。

本文介绍如何使用适用于 Visual Studio 2017 的 Azure IoT Edge 工具开发 IoT Edge 模块。 您也將學習如何將專案部署到 Azure IoT Edge 裝置。

> [!TIP]
> Visual Studio 所建立的 IoT Edge 專案結構與 Visual Studio Code 中的 IoT Edge 專案結構不同。
  
## <a name="prerequisites"></a>必要條件

此文章假設您使用執行 Windows 的電腦或虛擬機器作為開發電腦。 您的 IoT Edge 裝置可以是另一部實體裝置。

因為此文章使用 Visual Studio 2017 作為主要開發工具，所以請安裝 Visual Studio。 确保在 Visual Studio 2017 安装中包含“Azure 开发”和“使用 C++ 的桌面开发”工作负荷。 可以[修改 Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) 以添加所需的工作负荷。

Visual Studio 2017 備妥之後，您也需要下列工具和元件：

- 從 Visual Studio Marketplace 下載並安裝 [Azure IoT Edge 延伸模組 (預覽)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)，以在 Visual Studio 2017 中建立 IoT Edge 專案。

- 在開發電腦下載並安裝 [Docker Community Edition](https://docs.docker.com/install/)，以建置並執行您的模組映像。 您必須設定 Docker CE 在 Linux 容器模式或 Windows 容器模式中執行。

- 安裝 [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/)，以便設定本機開發環境以偵錯、執行和測試您的 IoT Edge 解決方案。 在您的終端機中執行下列命令，先安裝 [Python (2.7/3.6) 和 Pip](https://www.python.org/)，再安裝 **iotedgehubdev** 封裝。 確定您的 Azure IoT EdgeHub Dev Tool 版本大於 0.3.0。

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- 克隆存储库并安装 Vcpkg 库管理器，然后安装适用于 Windows 的 **azure-iot-sdk-c 包**。

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

1. 從 [工具] 功能表中，選取 [擴充功能和更新]。 依序展開 [已安裝] > [工具]，您可以找到 **Azure IoT Edge Tools** 和 **Cloud Explorer for Visual Studio**。

1. 記下已安裝的版本。 您可以將此版本與 Visual Studio Marketplace 上的最新版本進行比較 ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)、[Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools))

1. 如果您的版本比 Visual Studio Marketplace 提供的版本舊，請依照下一節中的說明在 Visual Studio 中更新您的工具。

### <a name="update-your-tools"></a>更新您的工具

1. 在 [擴充功能和更新] 對話方塊中，展開 [更新] > [Visual Studio Marketplace]，選取 [Azure IoT Edge Tools] 或 [Cloud Explorer for Visual Studio]，然後選取 [更新]。

1. 下載工具更新之後，請關閉 Visual Studio，以使用 VSIX 安裝程式觸發工具更新。

1. 在安裝程式中選取 [確定] 以啟動，然後選取 [修改] 以更新工具。

1. 更新完成後，請選取 [關閉] 並重新啟動 Visual Studio。

### <a name="create-an-azure-iot-edge-project"></a>建立 Azure IoT Edge 專案

Visual Studio 中的 Azure IoT Edge 專案範本可建立可以在 Azure IoT 中樞中部署到 Azure IoT Edge 裝置的專案。 首先创建一个 Azure IoT Edge 解决方案，并在该解决方案中生成第一个模块。 每個 IoT Edge 解決方案都可以包含多個模組。

1. 在 Visual Studio 中，從 [檔案] 功能表中選取 [新增]  >  [專案]。

1. 在“新建项目”对话框中，依次选择“已安装”、“Azure IoT”、“Azure IoT Edge”，输入项目的名称并指定位置，然后选择“确定”。 預設專案名稱是 **AzureIoTEdgeApp1**。

   ![New Project](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

1. 在“添加 IoT Edge 应用程序和模块”窗口中，选择“Linux Amd64”和/或“Windows Amd64”作为应用程序平台。 如果选择两者，请创建包含两个项目的解决方案，其中的每个项目引用默认代码模块。

   > [!TIP]
   > 适用于 Visual Studio 的 Azure IoT Edge 扩展目前不支持创建适用于 ARM 平台的项目。 有关使用 Visual Studio Code 开发适用于 ARM32v7/armhf 的解决方案的示例，请参阅此 [IoT 开发人员博客文章](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/)。

1. 选择“C# 模块”或“C 模块”，然后指定模块名称和模块映像存储库。 Visual Studio 會自動以 **localhost:5000/<您的模組名稱\>** 填入模組名稱。 請使用您自己的登錄資訊加以取代。 如果您使用本機 Docker 登錄來進行測試，則可以使用 **localhost**。 如果您使用 Azure Container Registry，則請使用登錄設定中的登入伺服器。 登入伺服器看起來像 ***\<登錄名稱\>*.azurecr.io**。 僅取代字串的 **localhost:5000** 部分即可，因此最後的結果看起來像 **\<* 登錄名稱*\>.azurecr.io/*\<您的模組名稱\>***。 預設的模組名稱是 **IoTEdgeModule1**

1. 选择“确定”创建包含使用 C# 或 C 的模块的 Azure IoT Edge 解决方案。

现已在解决方案中创建 **AzureIoTEdgeApp1.Linux.Amd64** 项目和/或 **AzureIoTEdgeApp1.Windows.Amd64** 项目，以及一个 **IoTEdgeModule1** 项目。 每个 **AzureIoTEdgeApp1** 项目包含一个 `deployment.template.json` 文件，此文件定义了要为 IoT Edge 解决方案生成和部署的模块，并定义了模块之间的路由。 預設解決方案具有 **tempSensor** 模組與 **IoTEdgeModule1** 模組。 **tempSensor** 模組會產生 **IoTEdgeModule1** 模組的模擬資料，而 **IoTEdgeModule1** 模組中的預設程式碼會直接將收到的訊息透過管道傳送到 Azure IoT 中樞。

**IoTEdgeModule1** 專案是 .NET Core 2.1 主控台應用程式。 它包含執行 Windows 容器或 Linux 容器之 IoT Edge 裝置所需的 Dockerfiles。 `module.json` 文件描述模块的元数据。 在 `Program.cs` 或 `main.c` 文件中可以找到将 Azure IoT 设备 SDK 用作依赖项的实际模块代码。

## <a name="develop-your-module"></a>開發您的模組

解决方案附带的默认模块代码位于 **IoTEdgeModule1** > **Program.cs** (C#) 或 **main.c** (C) 中。 将设置模块和 `deployment.template.json` 文件，使你能够生成解决方案，将其推送到容器注册表，然后部署到设备以开始测试，而无需修改任何代码。 模組會建置為採用來源的輸入 (在此案例中，是會模擬資料的 **tempSensor** 模組)，並且將其傳送到 Azure IoT 中樞。

准备好使用自己的代码自定义模块模板时，请使用 [Azure IoT 中心 SDK](../iot-hub/iot-hub-devguide-sdks.md) 生成模块，以解决 IoT 解决方案的关键需求，例如安全性、设备管理和可靠性。

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>使用 IoT Edge 裝置連接字串來初始化 iotedgehubdev

1. 從 Visual Studio Cloud Explorer 中的**主要連接字串**，複製任何 IoT Edge 裝置的連接字串。 慎勿複製非 Edge 裝置的連接字串，IoT Edge 裝置的圖示與非 Edge 裝置的圖示不同。

   ![複製 Edge 裝置連接字串](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. 以滑鼠右鍵按一下 [AzureIoTEdgeApp1] 專案，然後按一下 [設定 Edge 裝置連接字串] 開啟 Azure IoT Edge 設定視窗。

   ![開啟 [設定 Edge 連接字串] 視窗](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. 輸入第一個步驟中的連接字串，然後選取 [確定]。

> [!NOTE]
> 您只需要在開發電腦上進行這些步驟一次，因為結果會自動套用至全部後續的 Azure IoT Edge 解決方案。 如果您要變更為不同的連接字串，可以再次進行此程序。

## <a name="build-and-debug-single-module"></a>生成和调试单个模块

一般而言，您為想要先針對每個模組進行測試和偵錯，然後在使用多個模組的整個解決方案中執行。

1. 以滑鼠右鍵按一下 **IoTEdgeModule1**，然後選取快顯功能表中的 [設定為啟始專案]。

   ![設定啟始專案](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. 按下 **F5** 或按一下下面的按鈕執行模組，第一次可能需要 10&ndash;20 秒。

   ![執行模組](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. 如果模組已成功初始化，您應該會看到 .NET Core 主控台應用程式已啟動。

   ![執行中的模組](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. 如果以 C# 进行开发，请在 **Program.cs** 中的 `PipeMessage()` 函数内设置一个断点；如果使用 C，请在 **main.c** 中的 `InputQueue1Callback()` 函数内设置断点。 然后，可以通过在 **Git Bash** 或 **WSL Bash** shell 中运行以下命令发送消息，来测试断点。 (您無法從 PowerShell 或命令提示字元執行 `curl` 命令。)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![針對單一模組進行偵錯](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    中斷點應該已觸發。 您可以在 Visual Studio [區域變數] 視窗中觀察變數。

   > [!TIP]
   > 您也可以使用 [PostMan](https://www.getpostman.com/) 或其他 API 工具來傳送訊息，而不使用 `curl`。

1. 按 **Ctrl + F5** 或按一下 [停止] 按鈕以停止偵錯。

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>建置使用多個模組的 IoT Edge 解決方案以及針對其進行偵錯

您完成開發單一模組之後，您會想要執行使用多個模組的整個解決方案以及針對其進行偵錯。

1. 右键单击“AzureIoTEdgeApp1”并选择“添加” > “新 IoT Edge 模块”，向解决方案添加另一个模块。 第二個模組的預設名稱是 **IoTEdgeModule2**，而且仍然是個別的管道模組。

1. 開啟檔案 `deployment.template.json`，您將在 [模組] 區段中看到已加入 **IoTEdgeModule2**。 以下列程式碼取代 **routes** 區段。 如果您已自訂模組名稱，請確定更新名稱以使其相符。

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

1. 以滑鼠右鍵按一下 **AzureIoTEdgeApp1**，然後選取快顯功能表中的 [設定為啟始專案]。

1. 建立您的中斷點，然後按下 **F5** 同時執行多個模組並針對其進行偵錯。 此时应会看到多个 .NET Core 控制台应用窗口，每个窗口表示一个不同的模块。

   ![針對多個模組進行偵錯](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. 按下 **Ctrl + F5** 或選取 [停止] 按鈕以停止偵錯。

## <a name="build-and-push-images"></a>建置及推送映像

1. 確定 **AzureIoTEdgeApp1** 是啟始專案。 選取 [偵錯] 或 [發行] 做為要對於模組映像建置的設定。

    > [!NOTE]
    > 選擇 [偵錯] 時，Visual Studio 將使用 `Dockerfile.(amd64|windows-amd64).debug` 來建置 Docker 映像。 這包含建置它時容器映像中的 .NET Core 命令列偵錯工具 VSDBG。 對於生產就緒 IoT Edge 模組，建議您使用 [發行] 設定，此設定會使用無 VSDBG 的 `Dockerfile.(amd64|windows-amd64)`。

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

1. 以滑鼠右鍵按一下 **AzureIoTEdgeApp1**，並選取 [建置並推送 Edge 解決方案]，以便建置並推送每個模組的 Docker 映像。

   ![建置並推送映像](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>部署解決方案

在您用來設定 IoT Edge 裝置的快速入門文章中，您使用 Azure 入口網站部署了模組。 您也可以使用適用於 Visual Studio 的 Cloud Explorer 來部署模組。 您已備妥對於您的案例準備的部署資訊清單 (`deployment.json` 檔案)，您只需要選取要接收部署的裝置即可。

1. 依序按一下 [檢視] > [Cloud Explorer] 以開啟 **Cloud Explorer**。 請確定您已登入 Visual Studio 2017。

1. 在 **Cloud Explorer** 中，展開您的訂用帳戶，尋找您的 Azure IoT 中樞與您要部署的 Azure IoT Edge 裝置。

1. 以滑鼠右鍵按一下 IoT Edge 裝置為其建立部署，您必須選取 `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json` 下的部署資訊清單檔案。

   > [!NOTE]
   > 您不得選取 `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. 按一下 [重新整理] 按鈕，查看新的模組正在與 **TempSensor** 模組以及 **$edgeAgent** 和 **$edgeHub** 一起執行。

## <a name="view-generated-data"></a>檢視產生的資料

1. 若要監視特定裝置的 D2C 訊息，請選取清單中的裝置，然後按一下 [動作] 視窗中的 [開始監視 D2C 訊息]。

1. 若要停止監視資料，請選取清單中的裝置，然後在 [動作] 視窗中選取 [停止監視 D2C 訊息]。

## <a name="next-steps"></a>後續步驟

若要為您的 IoT Edge 裝置開發自訂模組，請[了解及使用 Azure IoT 中樞 SDK](../iot-hub/iot-hub-devguide-sdks.md)。
