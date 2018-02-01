---
title: "使用 Azure CLI 2.0 的 IoT 擴充功能將模組部署到 IoT Edge 裝置 | Microsoft Docs"
description: "使用 Azure CLI 2.0 的 IoT 擴充功能將模組部署到 IoT Edge 裝置"
services: iot-edge
keywords: 
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 01/11/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 26067187864f9a2a4c85c953ae8aca888458d245
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>使用 Azure CLI 2.0 的 IoT 擴充功能將模組部署到 IoT Edge 裝置

[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) 是一個開放原始碼跨平台命令列工具，用來管理 Azure 資源 (例如 IoT Edge)。 Azure CLI 2.0 適用於 Windows、Linux 和 MacOS。

Azure CLI 2.0 可讓您管理 Azure IoT 中樞資源、裝置佈建服務執行個體，以及現成的連結中樞。 新的 IoT 擴充功能以裝置管理和完整 IoT Edge 功能來擴充 Azure CLI 2.0 的功能。

在本教學課程中，您會先完成設定 Azure CLI 2.0 和 IoT 擴充功能的步驟。 然後了解如何使用可用的 CLI 命令，將模組部署到 IoT Edge 裝置。

## <a name="installation"></a>安裝 

### <a name="step-1---install-python"></a>步驟 1 - 安裝 Python

需要 [Python 2.7x 或 Python 3.x](https://www.python.org/downloads/)。

### <a name="step-2---install-azure-cli-20"></a>步驟 2 - 安裝 Azure CLI 2.0

請遵循[安裝指示](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)在您的環境中設定 Azure CLI 2.0。 您的 Azure CLI 2.0 版本至少必須是 2.0.24 或更新版本。 使用 `az –version` 進行驗證。 這個版本支援 az 擴充命令並引進 Knack 命令架構。 在 Windows 上進行安裝的最簡單方式就是下載並安裝 [MSI](https://aka.ms/InstallAzureCliWindows)。

### <a name="step-3---install-iot-extension"></a>步驟 3 - 安裝 IoT 擴充功能

[IoT 擴充功能讀我檔案](https://github.com/Azure/azure-iot-cli-extension)說明安裝此擴充功能的數種方式。 最簡單的方式就是執行 `az extension add --name azure-cli-iot-ext`。 安裝之後，您可以使用 `az extension list` 來驗證目前安裝的擴充功能，或使用 `az extension show --name azure-cli-iot-ext` 來查看有關 IoT 擴充功能的詳細資料。 若要移除此擴充功能，您可以使用 `az extension remove --name azure-cli-iot-ext`。


## <a name="deploy-modules-to-an-iot-edge-device"></a>將模組部署到 IoT Edge 裝置
在本教學課程中，您將了解如何建立 IoT Edge 部署。 此範例會說明如何登入您的 Azure 帳戶、建立 Azure 資源群組 (可保存 Azure 解決方案相關資源的容器)、建立 IoT 中樞、建立三個 IoT Edge 裝置身分識別、設定標記，然後建立以這些裝置為目標的 IoT Edge 部署。 開始之前，請先完成先前所述的安裝步驟。 如果您沒有 Azure 帳戶，可以立即[建立一個免費帳戶](https://azure.microsoft.com/free/?v=17.39a)。 


### <a name="1-login-to-the-azure-account"></a>1.登入 Azure 帳戶
  
    az login

![登入][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2.在 eastus 中建立資源群組 IoTHubBlogDemo

    az group create -l eastus -n IoTHubBlogDemo

![建立資源群組][2]


### <a name="3-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>3.在新建立的資源群組之下建立 IoT 中樞 blogDemoHub

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![建立 IoT 中樞][3]


### <a name="4-create-an-iot-edge-device"></a>4.建立 IoT Edge 裝置

    az iot hub device-identity create -d edge001 -n blogDemoHub --edge-enabled

![建立 IoT Edge 裝置][4]

### <a name="5-apply-configuration-to-the-iot-edge-device"></a>5.將組態套用到 IoT Edge 裝置

在本機將部署 JSON 範本儲存為 txt 檔案。 當您執行 apply-configuration 命令時，您需要此檔案的路徑。

以下是包含一個 tempSensor 模組的範例部署 JSON 範本：

```json
{
  "moduleContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": ""
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-agent:1.0-preview",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/edge-hub:1.0-preview",
              "createOptions": "{}"
            }
          }
        },
        "modules": {
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "edgepreview.azurecr.io/azureiotedge/simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "tempSensor": {
      "properties.desired": {}
    }
  }
}
```

    az iot hub apply-configuration --device-id edge001 --hub-name blogDemoHub --content C:\<yourLocation>\edgeconfig.txt

![套用組態][5]

### <a name="6-list-modules"></a>6.列出模組
    
    az iot hub module-identity list --device-id edge001 --hub-name blogDemoHub

![列出模組][6]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立包含程式碼的 Azure Function，可篩選您 IoT Edge 裝置所產生的原始資料。 若要保持瀏覽 Azure IoT Edge，請了解如何使用 IoT Edge 裝置作為閘道。 

> [!div class="nextstepaction"]
> [建立 IoT Edge 閘道裝置](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-create-deployment-with-cli-iot-extension/login.jpg
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.jpg
[3]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-hub.jpg
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[5]: ./media/tutorial-create-deployment-with-cli-iot-extension/apply-configuration.PNG
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.PNG

