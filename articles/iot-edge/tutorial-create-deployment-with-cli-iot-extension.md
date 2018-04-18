---
title: 使用 Azure CLI 2.0 的 IoT 擴充功能將模組部署到 IoT Edge 裝置 | Microsoft Docs
description: 使用 Azure CLI 2.0 的 IoT 擴充功能將模組部署到 IoT Edge 裝置
services: iot-edge
keywords: ''
author: chrissie926
manager: timlt
ms.author: menchi
ms.date: 03/02/2018
ms.topic: article
ms.service: iot-edge
ms.custom: ''
ms.reviewer: kgremban
ms.openlocfilehash: 1f71fdfb7090dce24ba73f1fa01e287c52b065f8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-modules-to-an-iot-edge-device-using-iot-extension-for-azure-cli-20"></a>使用 Azure CLI 2.0 的 IoT 擴充功能將模組部署到 IoT Edge 裝置

[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure?view=azure-cli-latest) 是一個開放原始碼跨平台命令列工具，用來管理 Azure 資源 (例如 IoT Edge)。 Azure CLI 2.0 適用於 Windows、Linux 和 MacOS。

Azure CLI 2.0 可讓您管理 Azure IoT 中樞資源、裝置佈建服務執行個體，以及現成的連結中樞。 新的 IoT 擴充功能以裝置管理和完整 IoT Edge 功能來擴充 Azure CLI 2.0 的功能。

在本文中，您會設定 Azure CLI 2.0 和 IoT 擴充功能。 然後了解如何使用可用的 CLI 命令，將模組部署到 IoT Edge 裝置。

## <a name="prerequisites"></a>先決條件

* 一個 Azure 帳戶。 如果還沒有帳戶，可以立即[建立免費帳戶](https://azure.microsoft.com/free/?v=17.39a)。 

* [Python 2.7x 或 Python 3.x](https://www.python.org/downloads/)。

* 您環境中的 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 您的 Azure CLI 2.0 版本至少必須是 2.0.24 或更新版本。 使用 `az –-version` 進行驗證。 這個版本支援 az 擴充命令並引進 Knack 命令架構。 在 Windows 上進行安裝的最簡單方式，就是下載並安裝 [MSI](https://aka.ms/InstallAzureCliWindows)。

* [Azure CLI 2.0 的 IoT 擴充功能](https://github.com/Azure/azure-iot-cli-extension)：
   1. 執行 `az extension add --name azure-cli-iot-ext`。 
   2. 安裝之後，請使用 `az extension list` 來驗證目前安裝的擴充功能，或使用 `az extension show --name azure-cli-iot-ext` 來查看有關 IoT 擴充功能的詳細資料。
   3. 若要移除擴充功能，請使用 `az extension remove --name azure-cli-iot-ext`。


## <a name="create-an-iot-edge-device"></a>建立 IoT Edge 裝置
本文提供如何建立 IoT Edge 部署的指示。 此範例會說明如何登入您的 Azure 帳戶、建立 Azure 資源群組 (可保存 Azure 解決方案相關資源的容器)、建立 IoT 中樞、建立三個 IoT Edge 裝置身分識別、設定標記，然後建立以這些裝置為目標的 IoT Edge 部署。 

登入您的 Azure 帳戶。 在您輸入下列登入命令之後，系統會提示您透過網頁瀏覽器使用一次性程式碼進行登入： 

   ```cli
   az login
   ```

在美國東部區域建立名為 **IoTHubCLI** 的新資源群組： 

   ```cli
   az group create -l eastus -n IoTHubCLI
   ```

   ![建立資源群組][2]

在新建立的資源群組中建立名為 **CLIDemoHub** 的 IoT 中樞：

   ```cli
   az iot hub create --name CLIDemoHub --resource-group IoTHubCLI --sku S1
   ```

   >[!TIP]
   >每個訂用帳戶都分配到一個免費的 IoT 中樞。 若要使用 CLI 命令建立免費的中樞，請以 `--sku F1` 取代 SKU 值。 如果您的訂用帳戶中已經有免費的中樞，您會在嘗試建立第二個中樞時收到錯誤訊息。 

建立 IoT Edge 裝置：

   ```cli
   az iot hub device-identity create --device-id edge001 -hub-name CLIDemoHub --edge-enabled
   ```

   ![建立 IoT Edge 裝置][4]

## <a name="configure-the-iot-edge-device"></a>設定 IoT Edge 裝置

建立部署 JSON 範本，並且在本機將它儲存為 txt 檔案。 當您執行 apply-configuration 命令時，您需要此檔案的路徑。

部署 JSON 範本應該一律包含兩個系統模組：edgeAgent 和 edgeHub。 除了這兩者模組以外，您可以使用這個檔案，將其他模組部署到 IoT Edge 裝置。 使用下列範例來設定具有一個 tempSensor 模組的 IoT Edge 裝置：

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
                 "image": "microsoft/azureiotedge-agent:1.0-preview",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "microsoft/azureiotedge-hub:1.0-preview",
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
                 "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
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

將此組態套用到 IoT Edge 裝置：

   ```cli
   az iot hub apply-configuration --device-id edge001 --hub-name CLIDemoHub --content C:\<configuration.txt file path>
   ```

在您的 IoT Edge 裝置上檢視模組：
    
   ```cli
   az iot hub module-identity list --device-id edge001 --hub-name CLIDemoHub
   ```

   ![列出模組][6]

## <a name="next-steps"></a>後續步驟

* 了解如何[使用 IoT Edge 裝置作為閘道](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[2]: ./media/tutorial-create-deployment-with-cli-iot-extension/create-resource-group.png
[4]: ./media/tutorial-create-deployment-with-cli-iot-extension/Create-edge-device.png
[6]: ./media/tutorial-create-deployment-with-cli-iot-extension/list-modules.png

