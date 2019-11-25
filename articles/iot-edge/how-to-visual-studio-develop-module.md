---
title: Develop and debug modules in Visual Studio - Azure IoT Edge | Microsoft Docs
description: Use Visual Studio 2019 to develop and debug modules for Azure IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 2f8b0fe83e10beb3b65dca08e18b03f4fc11947e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457104"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Use Visual Studio 2019 to develop and debug modules for Azure IoT Edge

您可以將商務邏輯轉換成 Azure IoT Edge 的模組。 This article shows you how to use Visual Studio 2019 as the main tool to develop and debug modules.

Azure IoT Edge Tools for Visual Studio 提供下列優點：

- 在本機開發電腦上建立、編輯、建置、執行 Azure IoT Edge 解決方案和模組並針對其進行偵錯。
- 透過 Azure IoT 中樞將您的 Azure IoT Edge 解決方案部署到 Azure IoT Edge 裝置。
- Code your Azure IoT modules in C or C# while having all of the benefits of Visual Studio development.
- 使用 UI 管理 Azure IoT Edge 裝置與模組。

This article shows you how to use the Azure IoT Edge Tools for Visual Studio 2019 to develop your IoT Edge modules. 您也將學習如何將專案部署到 Azure IoT Edge 裝置。 Currently, Visual Studio 2019 provides support for modules written in C and C#. The supported device architectures are Windows X64 and Linux X64 or ARM32. For more information about supported operating systems, languages, and architectures, see [Language and architecture support](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>必要條件

此文章假設您使用執行 Windows 的電腦或虛擬機器作為開發電腦。 On Windows computers you can develop either Windows or Linux modules. To develop Windows modules, use a Windows computer running version 1809/build 17763 or newer. To develop Linux modules, use a Windows computer that meets the [requirements for Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install). 

Because this article uses Visual Studio 2019 as the main development tool, install Visual Studio. Make sure you include the **Azure development** and **Desktop development with C++** workloads in your Visual Studio 2019 installation. You can [Modify Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) to add the required workloads.

After your Visual Studio 2019 is ready, you also need the following tools and components:

- Download and install [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) from the Visual Studio marketplace to create an IoT Edge project in Visual Studio 2019.

> [!TIP]
> If you are using Visual Studio 2017, please download and install [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) for VS 2017 from the Visual Studio marketplace

- 在開發電腦下載並安裝 [Docker Community Edition](https://docs.docker.com/install/)，以建置並執行您的模組映像。 您必須設定 Docker CE 在 Linux 容器模式或 Windows 容器模式中執行。

- 安裝 [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/)，以便設定本機開發環境以偵錯、執行和測試您的 IoT Edge 解決方案。 Install [Python (2.7/3.6+) and Pip](https://www.python.org/) and then install the **iotedgehubdev** package by running the following command in your terminal. 確定您的 Azure IoT EdgeHub Dev Tool 版本大於 0.3.0。

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Clone the repository and install the Vcpkg library manager, and then install the **azure-iot-sdk-c package** for Windows.

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

1. 記下已安裝的版本。 您可以將此版本與 Visual Studio Marketplace 上的最新版本進行比較 ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019)、[Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. 如果您的版本比 Visual Studio Marketplace 提供的版本舊，請依照下一節中的說明在 Visual Studio 中更新您的工具。

### <a name="update-your-tools"></a>更新您的工具

1. 在 [擴充功能和更新] 對話方塊中，展開 [更新] > [Visual Studio Marketplace]，選取 [Azure IoT Edge Tools] 或 [Cloud Explorer for Visual Studio]，然後選取 [更新]。

1. 下載工具更新之後，請關閉 Visual Studio，以使用 VSIX 安裝程式觸發工具更新。

1. 在安裝程式中選取 [確定] 以啟動，然後選取 [修改] 以更新工具。

1. 更新完成後，請選取 [關閉] 並重新啟動 Visual Studio。

### <a name="create-an-azure-iot-edge-project"></a>建立 Azure IoT Edge 專案

Visual Studio 中的 Azure IoT Edge 專案範本可建立可以在 Azure IoT 中樞中部署到 Azure IoT Edge 裝置的專案。 First you create an Azure IoT Edge solution, and then you generate the first module in that solution. 每個 IoT Edge 解決方案都可以包含多個模組。

> [!TIP]
> Visual Studio 所建立的 IoT Edge 專案結構與 Visual Studio Code 中的 IoT Edge 專案結構不同。

1. In Visual Studio new project dialog, search and select **Azure IoT Edge** project and click **Next**. In project configuration window, enter a name for your project and specify the location, and then select **Create**. 預設專案名稱是 **AzureIoTEdgeApp1**。

   ![建立新專案](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. In the **Add IoT Edge Application and Module** window, select either **C# Module** or **C Module** and then specify your module name and module image repository. Visual Studio 會自動以 **localhost:5000/<您的模組名稱\>** 填入模組名稱。 請使用您自己的登錄資訊加以取代。 如果您使用本機 Docker 登錄來進行測試，則可以使用 **localhost**。 如果您使用 Azure Container Registry，則請使用登錄設定中的登入伺服器。 The login server looks like **_\<registry name\>_ .azurecr.io**. Only replace the **localhost:5000** part of the string so that the final result looks like **\<*registry name*\>.azurecr.io/ _\<your module name\>_** . The default module name is **IotEdgeModule1**

   ![Add Application and Module](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Select **OK** to create the Azure IoT Edge solution with a module that uses either C# or C.

Now you have an **AzureIoTEdgeApp1.Linux.Amd64** project or an **AzureIoTEdgeApp1.Windows.Amd64** project, and also an **IotEdgeModule1** project in your solution. Each **AzureIoTEdgeApp1** project has a `deployment.template.json` file, which defines the modules you want to build and deploy for your IoT Edge solution, and also defines the routes between modules. The default solution has a **SimulatedTemperatureSensor** module and a **IotEdgeModule1** module. The **SimulatedTemperatureSensor** module generates simulated data to the **IotEdgeModule1** module, while the default code in the **IotEdgeModule1** module directly pipes received messages to Azure IoT Hub.

The **IotEdgeModule1** project is a .NET Core 2.1 console application if it's a C# module. 它包含執行 Windows 容器或 Linux 容器之 IoT Edge 裝置所需的 Dockerfiles。 The `module.json` file describes the metadata of a module. The actual module code, which takes Azure IoT Device SDK as a dependency, is found in the `Program.cs` or `main.c` file.

## <a name="develop-your-module"></a>開發您的模組

The default module code that comes with the solution is located at **IotEdgeModule1** > **Program.cs** (for C#) or **main.c** (C). The module and the `deployment.template.json` file are set up so that you can build the solution, push it to your container registry, and deploy it to a device to start testing without touching any code. The module is built to take input from a source (in this case, the **SimulatedTemperatureSensor** module that simulates data) and pipe it to Azure IoT Hub.

When you're ready to customize the module template with your own code, use the [Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md) to build modules that address the key needs for IoT solutions such as security, device management, and reliability.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>使用 IoT Edge 裝置連接字串來初始化 iotedgehubdev

1. 從 Visual Studio Cloud Explorer 中的**主要連接字串**，複製任何 IoT Edge 裝置的連接字串。 慎勿複製非 Edge 裝置的連接字串，IoT Edge 裝置的圖示與非 Edge 裝置的圖示不同。

   ![複製 Edge 裝置連接字串](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Go to **Tools** > **Azure IoT Edge Tools** > **Setup IoT Edge Simulator**, paste the connection string and click **OK**.

   ![開啟 [設定 Edge 連接字串] 視窗](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. 輸入第一個步驟中的連接字串，然後選取 [確定]。

> [!NOTE]
> 您只需要在開發電腦上進行這些步驟一次，因為結果會自動套用至全部後續的 Azure IoT Edge 解決方案。 如果您要變更為不同的連接字串，可以再次進行此程序。

## <a name="build-and-debug-single-module"></a>Build and debug single module

一般而言，您為想要先針對每個模組進行測試和偵錯，然後在使用多個模組的整個解決方案中執行。

1. Right-click **IotEdgeModule1** and select **Set as StartUp Project** from the context menu.

   ![設定啟始專案](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. 按下 **F5** 或按一下下面的按鈕執行模組，第一次可能需要 10&ndash;20 秒。

   ![執行模組](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. 如果模組已成功初始化，您應該會看到 .NET Core 主控台應用程式已啟動。

   ![執行中的模組](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. If developing in C#, set a breakpoint in the `PipeMessage()` function in **Program.cs**; if using C, set a breakpoint in the `InputQueue1Callback()` function in **main.c**. You can then test it by sending a message by running the following command in a **Git Bash** or **WSL Bash** shell. (您無法從 PowerShell 或命令提示字元執行 `curl` 命令。)

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

1. Add a second module to the solution by right-clicking **AzureIoTEdgeApp1** and selecting **Add** > **New IoT Edge Module**. The default name of the second module is **IotEdgeModule2** and will act as another pipe module.

1. Open the file `deployment.template.json` and you'll see **IotEdgeModule2** has been added in the **modules** section. 以下列程式碼取代 **routes** 區段。 如果您已自訂模組名稱，請確定更新名稱以使其相符。

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. 以滑鼠右鍵按一下 **AzureIoTEdgeApp1**，然後選取快顯功能表中的 [設定為啟始專案]。

1. 建立您的中斷點，然後按下 **F5** 同時執行多個模組並針對其進行偵錯。 You should see multiple .NET Core console app windows, which each window representing a different module.

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

1. 依序按一下 [檢視] > [Cloud Explorer] 以開啟 **Cloud Explorer**。 Make sure you've logged in to Visual Studio 2019.

1. 在 **Cloud Explorer** 中，展開您的訂用帳戶，尋找您的 Azure IoT 中樞與您要部署的 Azure IoT Edge 裝置。

1. 以滑鼠右鍵按一下 IoT Edge 裝置為其建立部署，您必須選取 `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json` 下的部署資訊清單檔案。

   > [!NOTE]
   > 您不得選取 `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. Click the refresh button to see the new modules running along with the **SimulatedTemperatureSensor** module and **$edgeAgent** and **$edgeHub**.

## <a name="view-generated-data"></a>檢視產生的資料

1. To monitor the D2C message for a specific device, select the device in the list and then click **Start Monitoring Built-in Event Endpoint** in the **Action** window.

1. To stop monitoring data, select the device in the list and then select **Stop Monitoring Built-in Event Endpoint** in the **Action** window.

## <a name="next-steps"></a>後續步驟

若要為您的 IoT Edge 裝置開發自訂模組，請[了解及使用 Azure IoT 中樞 SDK](../iot-hub/iot-hub-devguide-sdks.md)。
