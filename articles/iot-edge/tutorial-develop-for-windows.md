---
title: 開發適用於 Windows 裝置的模組 - Azure IoT Edge | Microsoft Docs
description: 本教學課程會逐步引導您設定開發機器和雲端資源，以使用 Windows 容器開發適用於 Windows 裝置的 IoT Edge 模組
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/20/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 631338c0217eb61f4f98cd06ffa16cb2500f246b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146740"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>教學課程：開發適用於 Windows 裝置的 IoT Edge 模組

使用 Visual Studio 2017 開發程式碼並將其部署至執行 IoT Edge 的 Windows 裝置。

在快速入門中，您已使用 Windows 虛擬機器建立 IoT Edge 裝置，並部署了來自 Azure Marketplace 的預先建置模組。 本教學課程會逐步引導您了解要如何做才能開發您自己的程式碼並將其部署至 IoT Edge 裝置。 本教學課程是其他所有教學課程的實用先決條件，其內容會深入探討特定的程式設計語言或 Azure 服務。 

本教學課程所使用的範例是部署 **C 模組至 Windows 裝置**。 選擇此範例是因為其最為簡單，您可以了解開發工具，而不必擔心是否有安裝正確的程式庫。 了解開發概念後，您就可以選擇您偏好的語言或 Azure 服務來對細節進行深入了解。 

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 設定開發機器。
> * 使用 IoT Edge Tools for Visual Studio 2017 來建立新專案。
> * 將專案建置為容器，並儲存在 Azure Container Registry。
> * 將程式碼部署至 IoT Edge 裝置。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>重要概念

本教學課程會逐步引導您開發 IoT Edge 模組。 「IoT Edge 模組」  (有時簡稱「模組」)  是含有可執行程式碼的容器。 您可以在 IoT Edge 裝置中部署一或多個模組。 模組會執行特定工作，例如從感應器擷取資料、執行資料分析或資料清除作業，或對 IoT 中樞傳送訊息。 如需詳細資訊，請參閱[了解 Azure IoT Edge 模組](iot-edge-modules.md)。

在開發 IoT Edge 模組時，請務必了解開發機器和作為模組最終部署位置的目標 IoT Edge 裝置之間有何差異。 為了保存模組程式碼所建置的容器必須符合「目標裝置」  的作業系統 (OS)。 在開發 Windows 容器時，這個概念會比較簡單，因為 Windows 容器只會在 Windows 作業系統上執行。 但是，您也可以 (舉例來說) 使用 Windows 開發機器來建置適用於 Linux IoT Edge 裝置的模組。 在該情況下，您必須確定開發機器執行的是 Linux 容器。 當您進行本教學課程時，請牢記「開發機器 OS」  和「容器 OS」  之間的差異。

本教學課程是以執行 IoT Edge 的 Windows 裝置作為適用對象。 Windows IoT Edge 裝置會使用 Windows 容器。 建議您使用 Visual Studio 2017 來針對 Windows 裝置進行開發，本教學課程也是這麼做的。 您也可以使用 Visual Studio Code，但這兩種工具的支援有所差異。

下表列出在 Visual Studio Code 和 Visual Studio 2017 中的 **Windows 容器**所支援的開發案例。

|   | Visual Studio Code | Visual Studio 2017 |
| - | ------------------ | ------------------ |
| **Azure 服務** | Azure Functions <br> Azure 串流分析 |   |
| **語言** | C# (不支援偵錯) | C <br> C# |
| **詳細資訊** | [Azure IoT Edge for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) |

本教學課程會教授 Visual Studio 2017 的開發步驟。 如果您比較想要使用 Visual Studio Code，請參閱[使用 Visual Studio Code 來開發適用於 Azure IoT Edge 的模組並針對其進行偵錯](how-to-vs-code-develop-module.md)中的指示。

## <a name="prerequisites"></a>必要條件

開發機器：

* Windows 10 (含 1809 更新或更新版本)。
* 您可以使用自己的電腦或虛擬機器 (視您偏好的開發方式而定)。
* 安裝 [Git](https://git-scm.com/)。 
* 透過 vcpkg 安裝適用於 Windows x64 的 Azure IoT C SDK：

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

<!--vcpkg only required for C development-->

位於 Windows 上的 Azure IoT Edge 裝置：

* 我們不建議您在開發機器上執行 IoT Edge，請改為使用另外的裝置。 讓開發機器和 IoT Edge 裝置有所區分可以更加精確地反映真正的部署情況，而有助於讓不同的概念變得簡單明瞭。
* 如果您沒有第二個裝置可供使用，請使用快速入門文章在具有 [Windows 虛擬機器](quickstart.md)的 Azure 中建立 IoT Edge 裝置。

雲端資源：

* 在 Azure 中擁有免費或標準層的 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。 

## <a name="install-container-engine"></a>安裝容器引擎

IoT Edge 模組會封裝為容器，因此開發機器上必須有容器引擎才能建置和管理容器。 我們建議使用 Docker Desktop 來進行開發，因為其具有許多功能並且是熱門的容器引擎。 若您在 Windows 電腦上使用 Docker Desktop，便可以在 Linux 容器和 Windows 容器之間進行切換，從而能夠輕鬆地針對不同類型的 IoT Edge 裝置來開發模組。 

使用 Docker 文件以便安裝在開發機器上： 

* [安裝適用於 Windows 的 Docker Desktop](https://docs.docker.com/docker-for-windows/install/)

  * 當您安裝適用於 Windows 的 Docker Desktop 時，系統會詢問您是要使用 Linux 還是 Windows 容器。 本教學課程使用 **Windows 容器**。 如需詳細資訊，請參閱[在 Windows 與 Linux 容器之間切換](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。


## <a name="set-up-visual-studio-and-tools"></a>設定 Visual Studio 和工具

使用適用於 Visual Studio 2017 的 IoT 擴充功能來開發 IoT Edge 模組。 這些擴充功能會提供專案範本、自動執行部署資訊清單的建立程序，並可讓您監視和管理 IoT Edge 裝置。 在本節中，您會安裝 Visual Studio 和 IoT Edge 擴充功能，然後設定 Azure 帳戶以便從 Visual Studio 內部管理 IoT 中樞資源。 

1. 如果開發機器上還沒有 Visual Studio，請使用下列工作負載來[安裝 Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio?view=vs-2017)： 

   * Azure 開發
   * 使用 C++ 的桌面開發
   * .NET Core 跨平台開發

1. 如果開發機器上已有 Visual Studio 2017，請確定其版本是 15.7 或更高版本。 請遵循[修改 Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) 中的步驟來新增所需的工作負載 (如果您還沒有這些工作負載)。

2. 下載並安裝適用於 Visual Studio 2017 的 [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) 擴充功能。 

3. 安裝完成時，請開啟 Visual Studio。

4. 選取 [檢視]   > [Cloud Explorer]  。 

5. 在 Cloud Explorer 中選取 [設定檔] 圖示，並登入 Azure 帳戶 (如果您尚未登入)。 

6. 登入後，系統便會列出您的 Azure 訂用帳戶。 選取您想要透過 Cloud Explorer 存取的訂用帳戶，然後選取 [套用]  。 

7. 依序展開您的訂用帳戶、[IoT 中樞]  和您的 IoT 中樞。 您應該會看到 IoT 裝置清單，並可使用此瀏覽器來管理這些裝置。 

   ![在 Cloud Explorer 中存取 IoT 中樞資源](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>建立新的模組專案

Azure IoT Edge Tools 擴充功能會針對 Visual Studio 2017 中所有支援的 IoT Edge 模組語言，提供專案範本。 這些範本具有您部署運作中模組以測試 IoT Edge 時需要的所有檔案和程式碼，或可成為您利用自己的商務邏輯自訂此範本的起點。 

1. 以系統管理員身分執行 Visual Studio。

2. 選取 [檔案]   > [新增]   > [專案]  。 

3. 在新的專案視窗中，選取 [Azure IoT]  專案類型，然後選擇 [Azure IoT Edge]  專案。 為專案和解決方案重新命名，或接受預設值 **AzureIoTEdgeApp1**。 選取 [確定]  以建立專案。 

   ![建立新的 Azure IoT Edge 專案](./media/tutorial-develop-for-windows/new-project.png)

4. 在 IoT Edge 應用程式和模組視窗中，使用下列值設定您的專案： 

   | 欄位 | 值 |
   | ----- | ----- |
   | 應用程式平台 | 取消核取 [Linux Amd64]  ，然後核取 [WindowsAmd64]  。 |
   | 選取範本 | 選取 [C 模組]  。 | 
   | 模組專案名稱 | 接受預設值 **IoTEdgeModule1**。 | 
   | Docker 映像存放庫 | 映像存放庫包含容器登錄名稱和容器映像名稱。 系統會從模組專案名稱值預先填入容器映像。 將 **localhost:5000** 取代為 Azure Container Registry 的登入伺服器值。 您可以在 Azure 入口網站中，從容器登錄的 [概觀] 頁面擷取登入伺服器。 <br><br> 最終的映像存放庫看起來類似於：\<登錄名稱\>.azurecr.io/iotedgemodule1。 |

   ![針對目標裝置、模組類型和容器登錄設定您的專案](./media/tutorial-develop-for-windows/add-application-and-module.png)

5. 選取 [確定]  以套用變更。 

Visual Studio 視窗中載入您的新專案後，請花點時間熟悉其所建立的檔案： 

* 名為 **AzureIoTEdgeApp1.Windows.Amd64** 的 IoT Edge 專案。
    * **Modules** 資料夾包含專案中所納入模組的指標。 在此案例中，應該就是 IoTEdgeModule1。 
    * **deployment.template.json** 檔案是可協助您建立部署資訊清單的範本。 「部署資訊清單」  檔案會確切定義您想要在裝置上部署的模組、模組的設定方式，以及模組要如何彼此通訊以及與雲端通訊。 
* 名為 **IoTEdgeModule1** 的 IoT Edge 模組專案。
    * **main.c** 檔案包含預設的 C 模組程式碼，此程式碼隨附於專案範本中。 預設模組會從來源取得輸入，再將其傳遞至 IoT 中樞。 
    * **module.json** 檔案會保存模組的相關詳細資料，包括完整的映像存放庫、映像版本，以及要對每個支援的平台使用哪一個 Dockerfile。

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>對 IoT Edge 代理程式提供登錄認證

IoT Edge 執行階段需要登錄認證才能將容器映像提取到 IoT Edge 裝置。 請將這些認證新增至部署範本。 

1. 開啟 **deployment.template.json** 檔案。

2. 在 $edgeAgent 所需的屬性中尋找 **registryCredentials** 屬性。 

3. 使用您的認證更新此屬性，並遵循以下格式： 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }

4. Save the deployment.template.json file. 

### Review the sample code

The solution template that you created includes sample code for an IoT Edge module. This sample module simply receives messages and then passes them on. The pipeline functionality demonstrates an important concept in IoT Edge, which is how modules communicate with each other.

Each module can have multiple *input* and *output* queues declared in their code. The IoT Edge hub running on the device routes messages from the output of one module into the input of one or more modules. The specific language for declaring inputs and outputs varies between languages, but the concept is the same across all modules. For more information about routing between modules, see [Declare routes](module-composition.md#declare-routes).

1. In the **main.c** file, find the **SetupCallbacksForModule** function.

2. This function sets up an input queue to receive incoming messages. It calls the C SDK module client function [SetInputMessageCallback](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-setinputmessagecallback). Review this function and see that it initializes an input queue called **input1**. 

   ![Find the input name in the SetInputMessageCallback constructor](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Next, find the **InputQueue1Callback** function.

4. This function processes received messages and sets up an output queue to pass them along. It calls the C SDK module client function [SendEventToOutputAsync](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-sendeventtooutputasync). Review this function and see that it initializes an output queue called **output1**. 

   ![Find the output name in the SendEventToOutputAsync constructor](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Open the **deployment.template.json** file.

6. Find the **modules** property of the $edgeAgent desired properties. 

   There should be two modules listed here. The first is **tempSensor**, which is included in all the templates by default to provide simulated temperature data that you can use to test your modules. The second is the **IotEdgeModule1** module that you created as part of this project.

   This modules property declares which modules should be included in the deployment to your device or devices. 

7. Find the **routes** property of the $edgeHub desired properties. 

   One of the functions if the IoT Edge hub module is to route messages between all the modules in a deployment. Review the values in the routes property. The first route, **IotEdgeModule1ToIoTHub**, uses a wildcard character (**\***) to include any message coming from any output queue in the IoTEdgeModule1 module. These messages go into *$upstream*, which is a reserved name that indicates IoT Hub. The second route, **sensorToIotEdgeModule1**, takes messages coming from the tempSensor module and routes them to the *input1* input queue of the IotEdgeModule1 module. 

   ![Review routes in deployment.template.json](./media/tutorial-develop-for-windows/deployment-routes.png)


## Build and push your solution

You've reviewed the module code and the deployment template to understand some key deployment concepts. Now, you're ready to build the IotEdgeModule1 container image and push it to your container registry. With the IoT tools extension for Visual Studio, this step also generates the deployment manifest based on the information in the template file and the module information from the solution files. 

### Sign in to Docker

Provide your container registry credentials to Docker on your development machine so that it can push your container image to be stored in the registry. 

1. Open PowerShell or a command prompt.

2. Sign in to Docker with the Azure container registry credentials that you saved after creating the registry. 

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   您可能會收到安全性警告，建議您使用 `--password-stdin`。 雖然建議生產案例使用該最佳做法，但是不在本教學課程的討論範圍內。 如需詳細資訊，請參閱 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 參考。

### <a name="build-and-push"></a>建置與推送

開發機器現在已可存取容器登錄，且 IoT Edge 裝置也會存取。 您可以開始將專案程式碼轉換成容器映像。 

1. 以滑鼠右鍵按一下 **AzureIotEdgeApp1.Windows.Amd64** 專案資料夾，然後選取 [建置和推送 IoT Edge 模組]  。 

   ![建置和推送 IoT Edge 模組](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   建置和推送命令會啟動三項作業。 首先，它會在解決方案中建立名為 **config** 的新資料夾，以保存完整部署資訊清單 (根據部署範本中的資訊建立)，以及其他解決方案檔案。 接著，它會執行 `docker build`，以根據目標架構的適當 dockerfile 建置容器映像。 然後，它會執行 `docker push` 以將映像存放庫推送至您的容器登錄。 

   此程序第一次進行時可能需要幾分鐘的時間，但下一次執行命令時速度就會變快。 

2. 在新建立的 config 資料夾中開啟 **deployment.windows-amd64.json** 檔案。 (config 資料夾可能不會出現在 Visual Studio 中的方案總管內。 如果遇到這種情況，請選取方案總管工作列中的 [顯示所有檔案]  圖示)。

3. 尋找 IotEdgeModule1 區段的 **image** 參數。 請注意，此映像包含完整的映像存放庫，內含來自 module.json 檔案的名稱、版本和架構標記。

4. 開啟 IotEdgeModule1 資料夾中的 **module.json** 檔案。 

5. 變更模組映像的版本號碼。 (版本，而非 $schema-version)。例如，將修補程式版本號碼遞增 **0.0.2**，就好像我們在模組程式碼中進行了些許修正。 

   >[!TIP]
   >模組版本可實現版本控制功能，並讓您先對一小組裝置測試變更，再將更新部署到生產環境。 如果您沒有先遞增模組版本再進行建置和推送，則會覆寫容器登錄中的存放庫。 

6. 將變更儲存至 module.json 檔案。

7. 再次以滑鼠右鍵按一下 **AzureIotEdgeApp1.Windows.Amd64** 專案資料夾，然後再次選取 [建置和推送 IoT Edge 模組]  。 

8. 再次開啟 **deployment.windows-amd64.json** 檔案。 請注意，當您再次執行建置和推送命令時，系統並未建立新的檔案。 反過來，系統會更新同一個檔案以反映變更。 IotEdgeModule1 映像現在會指向容器的 0.0.2 版本。 在部署資訊清單中進行這項變更可讓 IoT Edge 裝置知道有新的模組版本需要提取。 

9. 若要進一步確認建置和推送命令會做些什麼，請移至 [Azure 入口網站](https://portal.azure.com)並瀏覽至容器登錄。 

10. 在容器登錄中，依序選取 [存放庫]  和 [iotedgemodule1]  。 確認映像的這兩個版本皆已推送至登錄。

    ![在容器登錄中檢視這兩個映像版本](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>疑難排解

如果您在建置和推送模組映像時遇到錯誤，原因往往與開發機器上的 Docker 設定有關。 請使用下列檢查來檢閱您的設定： 

* 您是否使用從容器登錄複製的認證來執行 `docker login` 命令？ 這些認證與您用來登入 Azure 的認證不同。 
* 您的容器存放庫是否正確？ 其是否有正確的容器登錄名稱和正確的模組名稱？ 開啟 IotEdgeModule1 資料夾中的 **module.json** 檔案來進行檢查。 存放庫值看起來類似於： **\<登錄名稱\>.azurecr.io/iotedgemodule1**。 
* 如果您為模組使用不同於 **IotEdgeModule1** 的名稱，該名稱在整個解決方案中是否保持一致？
* 機器所執行的容器類型是否與所建置的容器類型相同？ 本教學課程適用於 Windows 的 IoT Edge 裝置，因此 Visual Studio 檔案應該要有 **windows-amd64** 擴充功能，且 Docker Desktop 應該執行 Windows 容器。 

## <a name="deploy-modules-to-device"></a>將模組部署到裝置

您已確認所建置的容器映像儲存在容器登錄中，因此可以開始將這些映像部署到裝置。 請確定您的 IoT Edge 裝置已啟動並執行。 

1. 在 Visual Studio 中開啟 [Cloud Explorer]，並展開 IoT 中樞的詳細資料。 

2. 選取要作為部署目的地裝置的名稱。 在 [動作]  清單中，選取 [建立部署]  。

   ![建立單一裝置的部署](./media/tutorial-develop-for-windows/create-deployment.png)


3. 在檔案總管中瀏覽至專案的 config 資料夾，然後選取 **deployment.windows-amd64.json** 檔案。 此檔案通常位於 `C:\Users\<username>\source\repos\AzureIotEdgeApp1\AzureIotEdgeApp1.Windows.Amd64\config\deployment.windows-amd64.json`

   請勿使用 deployment.template.json 檔案，其並未內含完整的模組映像值。 

4. 在 Cloud Explorer 中展開 IoT Edge 裝置的詳細資料，以查看裝置上的模組。

5. 使用 [重新整理]  按鈕來更新裝置狀態，以查看 tempSensor 和 IotEdgeModule1 模組是否已部署至裝置。 


   ![檢視在 IoT Edge 裝置上執行的模組](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>檢視裝置的訊息

IotEdgeModule1 程式碼會透過其輸入佇列接收訊息，並透過其輸出佇列傳遞。 部署資訊清單會宣告將訊息從 tempSensor 傳遞至 IotEdgeModule1，再將訊息從 IotEdgeModule1 轉送到 IoT 中樞的路由。 Azure IoT Edge Tools for Visual Studio 可讓您查看從個別裝置抵達 IoT 中樞的訊息。 

1. 在 Visual Studio Cloud Explorer 中，選取已作為您部署目的地 IoT Edge 裝置的名稱。 

2. 在 [動作]  功能表中，選取 [開始監視 D2C 訊息]  。

3. 觀看 Visual Studio 中的 [輸出]  區段，以查看抵達 IoT 中樞的訊息。 

   這兩個模組可能需要幾分鐘才會啟動。 IoT Edge 執行階段需要接收其新的部署資訊清單、從容器執行階段提取模組映像，然後啟動每個新的模組。 如果您 

   ![檢視傳入的裝置到雲端訊息](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>檢視裝置上的變更

如果您要查看裝置本身發生了什麼事情，請使用本節中的命令來檢查正在裝置上執行的 IoT Edge 執行階段和模組。 

本節中的命令適用於 IoT Edge 裝置而非開發機器。 如果您對 IoT Edge 裝置使用虛擬機器，請立即與其連線。 在 Azure 中，移至虛擬機器的 [概觀] 頁面，然後選取 [連線]  以存取遠端桌面連線。 在裝置上，開啟命令或 PowerShell 視窗以執行 `iotedge` 命令。

* 檢視所有部署到裝置的模組，並檢查其狀態：

   ```cmd
   iotedge list
   ```

   您應該會看到四個模組：兩個 IoT Edge 執行階段模組、tempSensor 和 IotEdgeModule1。 這四個模組應該都會列示為執行中狀態。

* 檢查特定模組的記錄：

   ```cmd
   iotedge logs <module name>
   ```

   IoT Edge 模組會區分大小寫。 

   tempSensor 和 IotEdgeModule1 記錄應該會顯示其正在處理的訊息。 edgeAgent 模組負責啟動其他模組，因此其記錄會有關於實做部署資訊清單的資訊。 如果有任何模組並未列出或未執行，edgeAgent 記錄可能會有錯誤。 edgeHub 模組負責模組與 IoT 中樞之間的通訊。 如果模組已啟動並執行，但訊息並未抵達 IoT 中樞，則 edgeHub 記錄可能會有錯誤。 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已在開發機器上設定 Visual Studio 2017，並從中部署第一個 IoT Edge 模組。 您已知道基本概念，接下來請嘗試對模組新增功能，使其可以分析其中通過的資料。 請選擇您慣用的語言： 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)