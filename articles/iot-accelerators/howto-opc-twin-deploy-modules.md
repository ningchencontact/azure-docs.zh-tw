---
title: 如何部署適用於 Azure 的 OPC 對應項的模組，從頭 |Microsoft Docs
description: 如何從零開始部署 OPC 對應項。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f470beb79e69b5a4a3febeb6a433c48490b96cf7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61451067"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>部署 OPC 對應項的模組，並從可用的相依性

OPC 對應項的模組會在 IoT Edge 上執行，並提供數個邊緣服務以 OPC 裝置對應項，登錄服務。 

有數個選項可將模組來部署您[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/)閘道，在它們之間

- [從 Azure 入口網站的 IoT Edge 刀鋒視窗中進行部署](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [使用 AZ CLI 部署](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> 如需有關部署詳細資料和指示的詳細資訊，請參閱 GitHub[存放庫](https://github.com/Azure/azure-iiot-components)。

## <a name="deployment-manifest"></a>部署資訊清單

使用部署資訊清單所部署的所有模組。  若要部署的範例資訊清單[OPC 發行者](https://github.com/Azure/iot-edge-opc-publisher)並[OPC 對應項](https://github.com/Azure/azure-iiot-opc-twin-module)如下所示。

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
            }
          }
        },
        "modules": {
          "opctwin": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
              "createOptions": "{\"HostConfig\": { \"NetworkMode\": \"host\", \"CapAdd\": [\"NET_ADMIN\"] } }"
            }
          },
          "opcpublisher": {
            "version": "2.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
              "createOptions": "{\"Hostname\": \"publisher\", \"Cmd\": [ \"publisher\", \"--pf=./pn.json\", \"--di=60\", \"--to\", \"--aa\", \"--si=0\", \"--ms=0\" ], \"ExposedPorts\": { \"62222/tcp\": {} }, \"HostConfig\": { \"PortBindings\": { \"62222/tcp\": [{ \"HostPort\": \"62222\" }] } } }"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
          "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>從 Azure 入口網站部署

若要將模組部署到 Azure IoT Edge 閘道裝置的最簡單方式是透過 Azure 入口網站。  

### <a name="prerequisites"></a>必要條件

1. 部署 OPC 對應項[相依性](howto-opc-twin-deploy-dependencies.md)並取得所產生的`.env`檔案。 請注意已部署`hub name`的`PCS_IOTHUBREACT_HUB_NAME`中所產生的使用者定義變數`.env`檔案。

2. 註冊並啟動[Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)或是[Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IoT Edge 閘道，並記下其`device id`。

### <a name="deploy-to-an-edge-device"></a>部署到 edge 裝置

1. 登入 [Azure 入口網站](https://portal.azure.com/)，然後瀏覽至 IoT 中樞。

2. 選取  **IoT Edge**從左側的功能表。

3. 按一下裝置清單中目標裝置的識別碼。

4. 選取 [設定模組]。

5. 在 **部署模組**頁面上，選取一節**新增**和**IoT Edge 模組。**

6. 在  **IoT Edge 自訂模組**對話方塊使用`opctwin`做為模組名稱，然後指定容器*映像 URI*為

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   作為*建立選項*使用下列 JSON:

   ```json
   {"HostConfig":{"NetworkMode":"host","CapAdd":["NET_ADMIN"]}}
   ```

   視需要填寫選擇性欄位。 如需容器建立選項、重新啟動原則和所需狀態的詳細資訊，請參閱 [EdgeAgent 所需屬性](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties)。 如需模組對應項的詳細資訊，請參閱[定義或更新所需屬性](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)。

7. 選取 **儲存**並重複步驟**5**。  

8. 在 [IoT Edge 自訂模組] 對話方塊中，使用`opcpublisher`做為模組和容器名稱*映像 URI*為 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   作為*建立選項*使用下列 JSON:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. 選取 [**儲存**，然後**下一步]** 繼續 [路由] 區段。

10. 在 [路由] 索引標籤中，貼上下列 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
      }
    }
    ```

    選取**下一步**

11. 檢閱您的部署資訊和資訊清單。  它看起來應該像上述的部署資訊清單。  選取 [提交]。

12. 一旦您將模組部署到您的裝置，就可以在入口網站的 [裝置詳細資料] 頁面中檢視所有項目。 此頁面會顯示每個已部署模組的名稱，以及像是部署狀態和結束代碼的實用資訊。

## <a name="deploying-using-azure-cli"></a>使用 Azure CLI 部署

### <a name="prerequisites"></a>必要條件

1. 安裝最新版[Azure 命令列介面 (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)從[這裡](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

### <a name="quickstart"></a>快速入門

1. 儲存到上述的部署資訊清單`deployment.json`檔案。  

2. 使用下列命令，將組態套用至 IoT Edge 裝置：

   ```bash
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   `device id`參數會區分大小寫。 內容參數會指向您已儲存的部署資訊清單檔案。 
    ![az IoT Edge 集模組輸出](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. 將模組部署至裝置後，您可以使用下列命令檢視所有模組：

   ```bash
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   裝置識別碼參數會區分大小寫。 ![az iot hub module-identity list 輸出](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="run-and-debug-locally"></a>執行和本機偵錯

問題的疑難排解和偵錯相當有用來執行使用在本機的 Edge 模組[IoT Edge 開發模擬器](https://github.com/Azure/iotedgehubdev)。  它提供與模擬器的本機開發體驗的建立、 開發、 測試、 執行和偵錯 Azure IoT Edge 模組並使用相同的位元/程式碼的解決方案，可在生產環境中。

### <a name="prerequisites"></a>必要條件

1. 部署 OPC 對應項[相依性](howto-opc-twin-deploy-dependencies.md)。

2. 安裝[Docker CE (18.02.0+)](https://www.docker.com/community-edition)上[Windows](https://docs.docker.com/docker-for-windows/install/)， [macOS](https://docs.docker.com/docker-for-mac/install/)或是[Linux](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce)。

3. 安裝[Docker Compose (1.20.0+)](https://docs.docker.com/compose/install/#install-compose) (才需要**Linux**。 撰寫已包含在 Windows/macOS Docker CE 安裝)

4. 安裝[Python (2.7 / 3.5+) 與 Pip](https://www.python.org/)

5. 執行下列命令，在終端機安裝 iotedgehubdev

   ```bash
   pip install --upgrade iotedgehubdev
   ```

> [!NOTE]
> 安裝`iotedgehubdev`要**根**在 Linux/macOS 上 (*請勿使用 '-使用者 ' 中 'pip install' 命令的選項*)。
> 請確定沒有任何 iotedgehubdev 在同一部電腦上執行，因為它們需要相同的連接埠的 Azure IoT Edge 執行階段。

### <a name="quickstart"></a>快速入門

1. 請依照下列指示來[在 Azure 入口網站中建立 Edge 裝置](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)。  將複製的 edge 裝置連接字串。

2. 設定模擬器使用邊緣的連接字串。

    ```bash
    iotedgehubdev setup -c <edge-device-connection-string>
    ```

3. 複製到的資訊清單上方`deployment.json`相同的資料夾中的檔案。  開始在模擬器中使用的部署

    ```bash
    iotedgehubdev start -d deployment.json
    ```

4. 停止模擬器使用

   ```bash
   iotedgehubdev stop
   ```

## <a name="next-steps"></a>後續步驟

既然您已了解如何從頭部署 OPC 對應項，以下是建議的下一個步驟：

> [!div class="nextstepaction"]
> [部署至現有的專案的 OPC 對應項](howto-opc-twin-deploy-existing.md)