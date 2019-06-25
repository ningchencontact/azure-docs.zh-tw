---
title: 從命令列建立自動部署 - Azure IoT Edge | Microsoft Docs
description: 使用適用於 Azure CLI 的 IoT 延伸模組為 IoT Edge 裝置群組建立自動部署
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 55b18246861e452a4ac170094ee902bd6954fe89
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190414"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>使用 Azure CLI 大規模部署與監視 IoT Edge 模組

建立**IoT Edge 的自動部署**來同時管理許多裝置的進行中的部署使用 Azure 命令列介面。 自動部署 IoT Edge 是的一部分[自動的裝置管理](/iot-hub/iot-hub-automatic-device-management.md)IoT 中樞的功能。 部署是動態的程序，可讓您將多個模組部署到多個裝置，追蹤的狀態和健全狀況模組，並進行變更時所需。 

如需詳細資訊，請參閱 <<c0> [ 自動了解 IoT Edge 部署適用於單一裝置或大規模](module-deployment-monitoring.md)。

在本文中，您會設定 Azure CLI 和 IoT 擴充功能。 然後，您會學習如何將模組部署到一組 IoT Edge 裝置，以及監視使用可用的 CLI 命令的進度。

## <a name="cli-prerequisites"></a>CLI 先決條件

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-using-cli.md)。 
* 已安裝 IoT Edge 執行階段的 [IoT Edge 裝置](how-to-register-device-cli.md)。
* 您環境中的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 您的 Azure CLI 版本至少必須是 2.0.24 或更新版本。 使用 `az –-version` 進行驗證。 這個版本支援 az 擴充命令並引進 Knack 命令架構。 
* [適用於 Azure CLI 的 IoT 擴充功能](https://github.com/Azure/azure-iot-cli-extension) \(英文\)。

## <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署資訊清單為 JSON 文件，說明應部署的模組、資料如何在模組之間流動，以及想要的模組對應項需要的屬性。 如需詳細資訊，請參閱 <<c0> [ 了解如何將模組部署，並在 IoT Edge 建立路由](module-composition.md)。

若要使用 Azure CLI 部署模組，請在本機上將部署資訊清單儲存成 .txt 檔案。 當您執行命令，以將組態套用到您的裝置時，您可以使用下一節中的檔案路徑。 

下面以具有一個模組的基本部署資訊清單為例：

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {
                "registryName": {
                  "username": "",
                  "password": "",
                  "address": ""
                }
              }
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
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
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "route": "FROM /* INTO $upstream"
          },
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
}
```

## <a name="identify-devices-using-tags"></a>使用標記識別裝置

在您可建立部署之前，必須能夠指定您要影響的裝置。 Azure IoT Edge 會使用裝置對應項中的 **tags** 來識別裝置。 每個裝置可以有多個您在任何對您的方案有意義的方式中定義的標記。 例如，如果您管理智慧建築的校園，便可以將下列標記新增至裝置：

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

如需裝置對應項和標記的詳細資訊，請參閱[了解和使用 IoT 中樞的裝置對應項](../iot-hub/iot-hub-devguide-device-twins.md)。

## <a name="create-a-deployment"></a>建立部署

透過建立一個包含部署資訊清單及其他參數的部署，您就可以將模組部署至您的目標裝置。 

使用[az iot edge 部署建立](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create)命令來建立部署：

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

部署會建立命令將會需要下列參數： 

* **--deployment-id** - 將建立在 IoT 中樞內的部署名稱。 為部署指定唯一的名稱，最長為 128 個小寫字母。 避免空格和下列無效字元：`& ^ [ ] { } \ | " < > /`。
* **--hub-name** - 將在其中建立部署的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 變更與您目前訂用帳戶`az account set -s [subscription name]`命令。
* **--content** - 部署資訊清單 JSON 的檔案名稱。 
* **--labels** - 新增標籤，以協助追蹤您的部署。 標籤是成對的「名稱, 值」組合，可描述您的部署。 標籤會採用 JSON 格式的名稱和值。 例如： `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition** - 輸入目標條件來判斷這個部署會將哪些裝置設為目標。 條件會以裝置對應項標籤或裝置對應項報告屬性為基礎，且應符合運算式格式。 例如： `tags.environment='test' and properties.reported.devicemodel='4000x'`。 
* **--priority** - 正整數。 如果兩個以上部署的目標為相同的裝置，則將會套用 [優先順序] 數值最高的部署。

## <a name="monitor-a-deployment"></a>監視部署

使用[az iot edge 部署節目](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show)命令，以顯示單一部署的詳細資料：

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

部署會顯示命令將會需要下列參數：
* **--deployment-id** - 存在於 IoT 中樞的部署名稱。
* **--hub-name** - 部署存在於其中的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶

在命令視窗中檢查部署。 **metrics** 屬性會列出每個中樞所評估每個計量的計數：

* **targetedCount** - 系統計量，指定 IoT 中樞內符合目標條件的裝置對應項數目。
* **appliedCount** - 系統計量，指定已在 IoT 中樞內將部署內容套用到其模組對應項的裝置數目。
* **reportedSuccessfulCount** -裝置度量資訊會指定部署報告成功從 IoT Edge 用戶端執行階段中的 IoT Edge 裝置的數目。
* **reportedFailedCount** -裝置度量資訊會報告從 IoT Edge 用戶端執行階段失敗的部署中指定的 IoT Edge 裝置數目。

您也可以使用針對每個度量的顯示裝置識別碼或物件的清單[az iot edge 部署顯示度量](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric)命令：

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
```

部署顯示度量命令採用下列參數： 
* **--deployment-id** - 存在於 IoT 中樞的部署名稱。
* **--metric-id** - 您想要在其中查看裝置識別碼清單的計量名稱，例如 `reportedFailedCount`
* **--hub-name** - 部署存在於其中的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶

## <a name="modify-a-deployment"></a>修改部署

當您修改部署時，所做的變更會立即複寫到所有目標裝置。 

如果您更新目標條件，就會發生下列更新：

* 如果裝置不符合舊的目標條件，但符合新的目標條件，而且此部署為該裝置的最高優先順序，則會將此部署套用到裝置。 
* 如果目前執行此部署的裝置不再符合目標條件，它會解除安裝此部署，並採用下一個最高優先順序的部署。 
* 如果目前執行此部署的裝置不再符合目標條件，且不符合任何其他部署的目標條件，則不會在裝置上發生任何變更。 裝置會以模組目前的狀態繼續執行它目前的模組，但不再將其當成此部署的一部分來管理。 一旦它符合任何其他部署的目標條件之後，就會解除安裝此部署，並採用新的部署。 

使用[az iot edge 部署更新](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update)命令可更新的部署：

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

部署更新命令會使用下列參數：
* **--deployment-id** - 存在於 IoT 中樞的部署名稱。
* **--hub-name** - 部署存在於其中的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶
* **--set** - 更新部署中的屬性。 您可以更新下列屬性：
  * targetCondition - 例如 `targetCondition=tags.location.state='Oregon'`
  * 標籤 
  * 優先順序


## <a name="delete-a-deployment"></a>刪除部署

當您刪除部署時，所有裝置均會採用其下一個最高優先順序的部署。 如果您的裝置不符合任何其他部署的目標條件，則在刪除部署時不會移除模組。 

使用[az iot edge 部署刪除](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete)命令來刪除部署：

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
```

部署的 [刪除] 命令會使用下列參數： 
* **--deployment-id** - 存在於 IoT 中樞的部署名稱。
* **--hub-name** - 部署存在於其中的 IoT 中樞名稱。 中樞必須在目前訂用帳戶中。 使用 `az account set -s [subscription name]` 命令切換到所需的訂用帳戶

## <a name="next-steps"></a>後續步驟

深入了解[將模組部署到 IoT Edge 裝置](module-deployment-monitoring.md)。
