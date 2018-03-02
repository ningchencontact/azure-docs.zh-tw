---
title: "遠端監視解決方案中的裝置模擬 - Azure | Microsoft Docs"
description: "本教學課程會示範如何使用裝置模擬器搭配遠端監視預先設定的解決方案。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/15/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 9f51c35be09af6f3a8dde7061dcf57a9c4cc9fdb
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-new-simulated-device"></a>建立新的模擬裝置

本教學課程會示範如何自訂遠端監視預先設定解決方案中的裝置模擬器微服務。 為說明裝置模擬器的功能，本教學課程會使用 Contoso IoT 應用程式中的兩個情節。

在第一個情節中，Contoso 需要測試新的智慧型燈泡裝置。 若要執行測試，您可以建立具有下列特性的新模擬裝置：

*屬性*

| Name                     | 值                      |
| ------------------------ | --------------------------- |
| 色彩                    | 白色、紅色、藍色            |
| 亮度               | 0 到 100                    |
| 估計的剩餘時間 | 10,000 個小時倒數計時 |

*遙測*

下表顯示燈泡 (lightbulb) 以資料流向雲端報告的資料：

| Name   | 值      |
| ------ | ----------- |
| 狀態 | 「開啟」、「關閉」 |
| 溫度 | 華氏度數 |
| 線上 | true、false |

> [!NOTE]
> **線上**遙測值會強制用於所有模擬類型。

*方法*

下表會顯示新裝置所支援的動作：

| Name        |
| ----------- |
| 開啟   |
| 關閉  |

*初始狀態*

下表會顯示裝置的初始狀態：

| Name                     | 值 |
| ------------------------ | -------|
| 初始色彩            | 白色  |
| 初始亮度       | 75     |
| 初始剩餘時間   | 10,000 |
| 初始遙測狀態 | 「開啟」   |
| 初始遙測溫度 | 200   |

在第二個情節中，您要將新的遙測類型新增至 Contoso 的現有 **Chiller** 裝置。

本教學課程會示範如何使用裝置模擬器搭配遠端監視預先設定的解決方案：

在本教學課程中，您了解如何：

>[!div class="checklist"]
> * 建立新的裝置類型
> * 模擬自訂裝置行為
> * 將新的裝置類型新增至儀表板
> * 從現有的裝置類型傳送自訂遙測

以下影片會逐步解說如何將模擬裝置和真實裝置連線到遠端監視解決方案：

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-38-Customizing-Azure-IoT-Suite-solution-and-connect-a-real-device/Player]

## <a name="prerequisites"></a>先決條件

為了依循本教學課程進行操作，您需要：

* 在 Azure 訂用帳戶中有一個已部署的遠端監視解決方案執行個體。 如果您尚未部署遠端監視解決方案，應該先完成[部署遠端監視預先設定的解決方案](iot-suite-remote-monitoring-deploy.md)教學課程。

* Visual Studio 2017。 如果尚未安裝 Visual Studio 2017，可以下載免費的 [Visual Studio Community](https://www.visualstudio.com/free-developer-offers/) 版本。

* [適用 Visual Studio 2017 的 Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVS15Preview) Visual Studio 擴充。

* [Docker Hub](https://hub.docker.com/) 帳戶。 您可以免費註冊並開始使用。

* 在您的桌面機器上安裝 [Git](https://git-scm.com/downloads)。

## <a name="prepare-your-development-environment"></a>準備您的開發環境

完成下列工作將開發環境準備就緒，以便將新的模擬裝置新增至您的遠端監視解決方案：

### <a name="configure-ssh-access-to-the-solution-virtual-machine-in-azure"></a>設定 Azure 中解決方案虛擬機器的 SSH 存取

當您在 [www.azureiotsuite.com](https://www.azureiotsuite.com) 建立遠端監視解決方案時，要選擇解決方案的名稱。 解決方案名稱會變成 Azure 資源群組的名稱，此資源群組中包含解決方案會使用的各種已部署資源。 下列命令使用 **Contoso-01** 資源群組名稱，您應該將 **Contoso-01** 取代為您的資源群組名稱。

以下命令使用 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest) 中的 `az` 命令。 您可以在您的部署機器上安裝 Azure CLI 2.0，或在 [Azure 入口網站](http://portal.azure.com)中使用 [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)。 Cloud Shell 中會預先安裝 Azure CLI 2.0。

1. 若要確認指定名稱的資源群組中包含您的遠端監視資源，請執行下列命令：

    ```sh
    az group list | grep "name"
    ```

    此命令會列出訂用帳戶中的所有資源群組。 此清單會包含與您的遠端監視解決方案同名的資源群組。

1. 若要將您的資源群組設為後續命令的預設群組，請執行下列命令，並將 **Contoso-01** 取代為您的資源群組名稱：

    ```sh
    az configure --defaults group=Contoso-01
    ```

1. 若要列出資源群組中的資源，請執行下列命令：

    ```sh
    az resource list -o table
    ```

    記下虛擬機器與網路安全性群組的名稱。 您在稍後的步驟會使用這些值。

1. 若要為您的虛擬機器啟用 SSH 存取，請使用上一個步驟中的網路安全性群組名稱執行下列命令：

    ```sh
    az network nsg rule create --name SSH --nsg-name YOUR-NETWORK-SECURITY-GROUP --priority 101 --destination-port-ranges 22 --access Allow --protocol TCP
    ```

    若要檢視網路的輸入規則，請執行下列命令：

    ```sh
    az network nsg rule list --nsg-name YOUR-NETWORK-SECURITY-GROUP -o table
    ```

1. 若要將虛擬機器的密碼變更為您知道的密碼，請執行下列命令。 使用您先前記下的虛擬機器名稱和您選擇的密碼：

    ```sh
    az vm user update --name YOUR-VM-NAME --username azureuser --password YOUR-PASSWORD
    ```
1. 使用下列命令找出虛擬網路的 IP 位址，然後記下公用 IP 位址：

    ```sh
    az vm list-ip-addresses --name YOUR-VM-NAME
    ```

1. 現在您可以使用 SSH 連線到您的虛擬機器。 Cloud Shell 中會預先安裝 `ssh` 命令。 使用先前步驟中的公用 IP 位址，並在出現提示時，使用您為虛擬機器設定的密碼：

    ```sh
    ssh azureuser@public-ip-address
    ```

    現在，您可以在遠端監視解決方案中，存取執行 Docker 容器之虛擬機器的殼層。 若要檢視執行中的容器，請使用下列命令：

    ```sh
    docker ps
    ```

### <a name="find-the-service-connection-strings"></a>找出服務連接字串

在本教學課程中，您使用 Visual Studio 解決方案，它會連線至解決方案的 Cosmos DB 與 IoT 中樞服務。 下列步驟顯示如何找出您需要的連接字串值：

1. 若要找出 Cosmos DB 連接字串，在連線至虛擬機器的 SSH 工作階段中執行下列命令：

    ```sh
    sudo grep STORAGEADAPTER_DOCUMENTDB /app/env-vars
    ```

    記下連接字串的名稱。 您稍後會在本教學課程中使用此值。

1. 若要找出 IoT 中樞連接字串，在連線至虛擬機器的 SSH 工作階段中執行下列命令：

    ```sh
    sudo grep IOTHUB_CONNSTRING /app/env-vars
    ```

    記下連接字串的名稱。 您稍後會在本教學課程中使用此值。

> [!NOTE]
> 您也可以使用 `az` 命令或在 Azure 入口網站中找到這些連接字串。

### <a name="stop-the-device-simulation-service-in-the-virtual-machine"></a>停止虛擬機器上的裝置模擬服務

當您修改裝置模擬服務 (device-simulation) 時，可以在本機執行它以測試您的變更。 本機執行裝置模擬服務之前，必須先停止虛擬機器上的執行中執行個體，做法如下：

1. 若要找出 **device-simulation** 服務的**容器識別碼**，在連線至虛擬機器的 SSH 工作階段中執行下列命令：

    ```sh
    docker ps
    ```

    記下 **device-simulation** 服務的容器識別碼。

1. 若要停止 **device-simulation** 容器，請執行下列命令：

    ```sh
    docker stop container-id-from-previous-step
    ```

### <a name="clone-the-github-repositories"></a>複製 GitHub 存放庫

在本教學課程中，您使用 **device-simulation** 和 **storage-adapter** 這兩個 Visual Studio 專案。 您可以從 GitHub 複製來源程式碼存放庫。 在已安裝 Visual Studio 的本機開發機器上執行此步驟：

1. 開啟命令提示字元，瀏覽至要儲存 **device-simulation** 和 **storage-adapter** GitHub 存放庫副本的資料夾。

1. 若要複製 .NET 版本的 **device-simulation** 存放庫，請執行下列命令：

    ```cmd
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

    遠端監視解決方案中的裝置模擬服務，可讓您變更內建的模擬裝置類型，以及建立新的模擬裝置類型。 在與實體裝置連線之前，您可以使用自訂的裝置類型來測試遠端監視解決方案的行為。

1. 若要複製 .NET 版本的 **storage-adapter** 存放庫，請執行下列命令：

    ```cmd
    git clone https://github.com/Azure/storage-adapter.git
    ```

    裝置模擬服務會使用儲存體配接器 (storage adapter) 服務連線到 Azure 中的 Cosmos DB 服務。 遠端監視解決方案會將模擬裝置的設定資料儲存在 Cosmos DB 資料庫中。

### <a name="run-the-storage-adapter-service-locally"></a>本機執行儲存體配接器服務

裝置模擬服務會使用儲存體配接器 (storage adapter) 服務連線到解決方案的 Cosmos DB 服務。 如果您在本機執行裝置模擬服務，也必須在本機執行儲存體配接器服務。 下列步驟示範如何從 Visual Studio 執行儲存體配接器服務：

1. 在 Visual Studio 中，開啟您的 **storage-adapter** 存放庫本機副本中的 **pcs-storage-adapter.sln** 解決方案檔案。

1. 在 [方案總管] 中，以滑鼠右鍵按一下 [WebService] 專案，選擇 [屬性]，然後選擇 [偵錯]。

1. 在 [環境變數] 區段中，編輯 **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** 變數的值，改為您先前記下的 Cosmos DB 連接字串。 然後儲存您的變更。

1. 在 [方案總管] 中，以滑鼠右鍵按一下 [WebService] 專案，選擇 [偵錯]，然後選擇 [開始新執行個體]。

1. 服務會開始在本機執行，並在預設瀏覽器中開啟 `http://localhost:9022/v1/status`。 確認 [狀態] 值是「正確: 運作良好」。

1. 繼續讓儲存體配接器服務在本機執行，直到完成本教學課程。

現在一切已準備就緒，您可以開始將新的模擬裝置類型新增至您的遠端監視解決方案。

## <a name="create-a-simulated-device-type"></a>建立模擬裝置類型

在裝置模擬服務中建立新裝置類型最簡單的方式，是複製現有的類型並加以修改。 下列步驟會示範如何複製內建 **Chiller** 裝置來建立新的 **Lightbulb** 裝置：

1. 在 Visual Studio 中，開啟您的 **device-simulation** 存放庫本機副本中的 **device-simulation.sln** 解決方案檔案。

1. 在 [方案總管] 中，以滑鼠右鍵按一下 [SimulationAgent] 專案，選擇 [屬性]，然後選擇 [偵錯]。

1. 在 [環境變數] 區段中，編輯 **PCS\_IOTHUB\_CONNSTRING** 變數的值，改為您先前記下的 IoT 中樞連接字串。 然後儲存您的變更。

1. 在 [方案總管] 中，以滑鼠右鍵按一下 [device-simulation] 解決方案，然後選擇 [設定啟始專案]。 選擇 [單一啟始專案]，然後選取 [SimulationAgent]。 然後按一下 [確定] 。

1. 在 **Services/data/devicemodels** 資料夾中，每種裝置類型都有 JSON 模型檔案和相關聯的指令碼。 在 [方案總管] 中，複製 **Chiller** 檔案，以建立 **Lightbulb** 檔案，如下表所示：

    | 來源                      | 目的地                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>定義新裝置類型的特性

**lightbulb-01.json** 檔案會定義類型的特性，例如它所產生的遙測和它所支援的方法。 下列步驟會更新 **lightbulb-01.json** 檔案以定義 **Lightbulb** 裝置：

1. 在 **lightbulb-01.json** 檔案中，更新裝置中繼資料，如下列程式碼片段所示：

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. 在 **lightbulb-01.json** 檔案中，更新模擬定義，如下列程式碼片段所示：

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "temperature": 200.0,
        "temperature_unit": "F",
        "status": "on"
      },
      "Script": {
        "Type": "javascript",
        "Path": "lightbulb-01-state.js",
        "Interval": "00:00:20"
      }
    },
    ```

1. 在 **lightbulb-01.json** 檔案中，更新裝置類型屬性，如下列程式碼片段所示：

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. 在 **lightbulb-01.json** 檔案中，更新裝置類型遙測定義，如下列程式碼片段所示：

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "temperature": "double",
            "temperature_unit": "text",
            "status": "text"
          }
        }
      }
    ],
    ```

1. 在 **lightbulb-01.json** 檔案中，更新裝置類型方法，如下列程式碼片段所示：

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      }
    }
    ```

1. 儲存 **lightbulb-01.json** 檔案。

### <a name="simulate-custom-device-behavior"></a>模擬自訂裝置行為

**scripts/lightbulb-01-state.js** 檔案會定義 **Lightbulb** 類型的模擬行為。 下列步驟會更新 **scripts/lightbulb-01-state.js** 檔案來定義 **Lightbulb** 裝置的行為：

1. 編輯 **scripts/lightbulb-01-state.js** 檔案中的狀態定義，如下列程式碼片段所示：

    ```js
    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };
    ```

1. 在 **vary** 函式之後新增 **flip** 函式，並定義如下：

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. 編輯 **main** 函式來實作行為，如下列程式碼片段所示：

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. 儲存 **scripts/lightbulb-01-state.js** 檔案。

**scripts/SwitchOn-method.js** 檔案會在 **Lightbulb** (燈泡) 裝置中實作 **Switch On** (開啟) 方法。 下列步驟會更新 **scripts/SwitchOn-method.js** 檔案：

1. 編輯 **scripts/SwitchOn-method.js** 檔案中的狀態定義，如下列程式碼片段所示：

    ```js
    var state = {
       status: "on"
    };
    ```

1. 為了開啟燈泡，編輯 **main** 函式，如下所示：

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. 儲存 **scripts/SwitchOn-method.js** 檔案。

1. 複製 **scripts/SwitchOn-method.js** 檔案，並命名為 **scripts/SwitchOff-method.js**。

1. 為了關閉 (Switch Off) 燈泡，編輯 **scripts/SwitchOff-method.js** 檔案中的 **main** 函式，如下所示：

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. 儲存 **scripts/SwitchOff-method.js** 檔案。

1. 在 [方案總管] 中，逐一選取您的四個新檔案。 在每個檔案的 [屬性] 視窗中，確定已將 [複製到輸出目錄] 設為 [有更新時才複製]。

### <a name="configure-the-device-simulation-service"></a>設定裝置模擬服務

為了限制測試期間可連線到解決方案的模擬裝置數目，將服務設定為執行單一 chiller 和單一 lightbulb 裝置。 設定資料是儲存在解決方案的資源群組中的 Cosmos DB 執行個體。 若要編輯設定資料，請使用 Visual Studio 中的 [Cloud Explorer] 檢視：

1. 若要在 Visual Studio 中開啟 [Cloud Explorer]，依序選擇 [檢視] 和 [Cloud Explorer]。

1. 若要尋找模擬設定文件，在 [搜尋資源] 中輸入 **simualtions.1**。

1. 按兩下 **simulations.1** 文件即可開啟並編輯它。

1. 在 **Data** 的值之中，找到看起來像下列程式碼片段的 **DeviceModels** 陣列：

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"chiller-02\",\"Count\":1},{\"Id\":\"elevator-01\",\"Count\":1},{\"Id\":\"elevator-02\",\"Count\":1},{\"Id\":\"engine-01\",\"Count\":1},{\"Id\":\"engine-02\",\"Count\":1},{\"Id\":\"prototype-01\",\"Count\":1},{\"Id\":\"prototype-02\",\"Count\":1},{\"Id\":\"truck-01\",\"Count\":1},{\"Id\":\"truck-02\",\"Count\":1}]
    ```

1. 為了定義單一 chiller 和單一 lightbulb 模擬裝置，將**DeviceModels** 陣列取代為下列程式碼：

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"lightbulb-01\",\"Count\":1}]
    ```

    儲存對 **simulations.1** 文件所做的變更。

> [!NOTE]
> 您也可以在 Azure 入口網站中使用 Cosmos DB 資料總管，來編輯 **simulations.1** 文件。

### <a name="test-the-lightbulb-device-type-locally"></a>本機測試燈泡裝置類型

您現在可以開始在本機執行裝置模擬專案，測試新的模擬燈泡類型。

1. 在 [方案總管] 中，以滑鼠右鍵按一下 [SimulationAgent]，選擇 [偵錯]，然後選擇 [開始新執行個體]。

1. 若要檢查兩個模擬裝置是否已連線到 IoT 中樞，在您的瀏覽器中開啟 Azure 入口網站。

1. 瀏覽找到資源群組之中包含遠端監視解決方案的 IoT 中樞。

1. 在 [監視] 區段中選取 [計量]。 然後確認 [連線的裝置] 的數目是兩個：

    ![連線的裝置數目](media/iot-suite-remote-monitoring-test/connecteddevices.png)

1. 在您的瀏覽器中，瀏覽至遠端監視解決方案的 [儀表板]。 在 [儀表板] 的遙測面板上，選取 [溫度]。 兩個模擬裝置的溫度會顯示在圖表上：

    ![溫度遙測](media/iot-suite-remote-monitoring-test/telemetry.png)

現在燈泡模擬裝置已在本機上執行。 下一步是將更新的模擬器程式碼部署至在 Azure 中執行遠端監視微服務的虛擬機器。

在繼續之前，您可以停止在 Visual Studio 中偵錯裝置模擬和儲存體配接器專案。

### <a name="deploy-the-updated-simulator-to-the-cloud"></a>將更新的模擬器部署到雲端

遠端監視解決方案中的微服務是在 docker 容器中執行。 容器裝載在 Azure 中該解決方案的虛擬機器上。 在本節中，您可：

* 建立新的裝置模擬 docker 映像。
* 將映像上傳到 Docker 中樞存放庫。
* 將映像匯入解決方案的虛擬機器。

下列步驟假設您的 Docker Hub 帳戶中已有名為 **lightbulb** 的存放庫。

1. 在 Visual Studio 中的 **device-simulation** 專案中，開啟 **solution\scripts\docker\build.cmd** 檔案。

1. 編輯 **DOCKER_IMAGE** 環境變數，設定為您的 Docker Hub 存放庫名稱：

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    儲存變更。

1. 在 Visual Studio 中的 **device-simulation** 專案中，開啟 **solution\scripts\docker\publish.cmd** 檔案。

1. 編輯 **DOCKER_IMAGE** 環境變數，設定為您的 Docker Hub 存放庫名稱：

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    儲存變更。

1. 以系統管理員身分開啟命令提示字元。 然後導覽至您的 **device-simulation** GitHub 存放庫副本中的 **scripts\docker** 資料夾。

1. 若要建置 Docker 映像，請執行下列命令：

    ```cmd
    build.cmd
    ```

1. 若要登入您的 Docker Hub 帳戶，請執行下列命令：

    ```cmd
    docker login
    ```

1. 若要將新映像上傳至您的 Docker Hub 帳戶，請執行下列命令：

    ```cmd
    publish.cmd
    ```

1. 若要確認上傳，瀏覽至 [https://hub.docker.com/](https://hub.docker.com/)。 找到您的 **lightbulb** 存放庫，選擇 [詳細資料]。 然後選擇 [標記]：

    ![Docker Hub](media/iot-suite-remote-monitoring-test/dockerhub.png)

    指令碼已在映像中新增 **testing** 標記。

1. 使用 SSH 連線到 Azure 中您的解決方案虛擬機器。 然後瀏覽至 **App** 資料夾，編輯 **docker compose.yaml** 檔案：

    ```sh
    cd /app
    sudo nano docker-compose.yaml
    ```

1. 編輯裝置模擬服務的項目，設定為使用您的 docker 映像：

    ```yaml
    devicesimulation:
      image: {your docker ID}/lightbulb:testing
    ```

    儲存您的變更。

1. 若要以新的設定重新啟動所有服務，請執行下列命令︰

    ```sh
    sudo ./start.sh
    ```

1. 若要查看您的新裝置模擬容器的記錄檔，請執行下列命令找出容器識別碼：

    ```sh
    docker ps
    ```

    接著，使用此容器識別碼執行下列命令：

    ```sh
    docker logs {container ID}
    ```

您現在已完成將更新版本的裝置模擬服務部署到遠端監視解決方案的步驟。

在您的瀏覽器中，瀏覽至遠端監視解決方案的 [儀表板]。 在 [儀表板] 的遙測面板上，選取 [溫度]。 兩個模擬裝置的溫度會顯示在圖表上：

![溫度遙測](media/iot-suite-remote-monitoring-test/telemetry.png)

在 [裝置] 頁面上，您可以佈建您新類型的執行個體：

![檢視可用模擬的清單](media/iot-suite-remote-monitoring-test/devicesmodellist.png)

您可以從模擬裝置檢視遙測：

![檢視燈泡遙測](media/iot-suite-remote-monitoring-test/devicestelemetry.png)

您可以在裝置上呼叫 **SwitchOn** 和 **SwitchOff** 方法：

![呼叫燈泡方法](media/iot-suite-remote-monitoring-test/devicesmethods.png)

## <a name="add-a-new-telemetry-type"></a>新增遙測類型

本章節會說明如何修改現有的模擬裝置類型，以支援新的遙測類型。

### <a name="locate-the-chiller-device-type-files"></a>找出 Chiller 裝置類型檔案

下列步驟會示範如何找出定義內建 **Chiller** 裝置的檔案：

1. 如果您尚未這樣做，使用下列命令，將 **device-simulation** GitHub 存放庫複製到本機電腦：

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. 每種裝置類型在 `data/devicemodels` 資料夾中都具有 JSON 模型檔和相關聯的指令碼。 定義模擬 **Chiller** 裝置類型的檔案為：

    * **data/devicemodels/chiller-01.json**
    * **data/devicemodels/scripts/chiller-01-state.js**

### <a name="specify-the-new-telemetry-type"></a>指定新的遙測類型

下列步驟會示範如何將新的**內部溫度**類型新增至 **Chiller** 裝置類型：

1. 開啟 **chiller 01.json** 檔案。

1. 更新 **SchemaVersion** 值，如下所示：

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. 在 **InitialState** 區段中，新增下列兩個定義：

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. 在**遙測** 陣列中，新增下列定義：

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. 儲存 **chiller 01.json** 檔案。

1. 開啟 **scripts/chiller-01-state.js** 檔案。

1. 將下列欄位新增至 **state** 變數：

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. 將下列字行新增至 **main** 函式：

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. 儲存 **scripts/chiller-01-state.js** 檔案。

### <a name="test-the-chiller-device-type"></a>測試 Chiller 裝置類型

若要測試更新後的 **Chiller** 裝置類型，請先執行 **device-simulation** 服務的本機副本，測試您的裝置類型是否行為如預期。 當您已在本機上測試並偵錯您更新的裝置類型時，可以重建容器，並將 **device-simulation** 服務重新部署至 Azure。

當您在本機執行 **device-simulation** 服務時，它會將遙測傳送至遠端監視解決方案。 在 [裝置] 頁面上，您可以佈建您更新類型的執行個體。

若要本機測試及偵錯您的變更，請參閱之前的[本機測試燈泡裝置類型](#test-the-lightbulb-device-type-locally)小節。

若要將更新的裝置模擬服務部署至 Azure 中解決方案的虛擬機器，請參閱先前的[將更新的模擬器部署到雲端](#deploy-the-updated-simulator-to-the-cloud)小節。

在 [裝置] 頁面上，您可以佈建您更新類型的執行個體：

![新增更新的 Chiller](media/iot-suite-remote-monitoring-test/devicesupdatedchiller.png)

您可以從模擬裝置檢視新的**內部溫度**遙測。

## <a name="next-steps"></a>後續步驟

本教學課程已示範如何：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 建立新的裝置類型
> * 模擬自訂裝置行為
> * 將新的裝置類型新增至儀表板
> * 從現有的裝置類型傳送自訂遙測

現在您已學會如何自訂裝置模擬服務。 建議的下一步是了解如何[將實體裝置連線到遠端監視解決方案](iot-suite-connecting-devices-node.md)。

如需關於遠端監視解決方案的開發人員詳細資訊，請參閱：

* [開發人員參考指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [開發人員疑難排解指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->