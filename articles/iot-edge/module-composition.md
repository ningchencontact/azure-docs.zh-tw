---
title: 宣告具有部署資訊清單的模組和路由 - Azure IoT Edge | Microsoft Docs
description: 了解部署資訊清單如何宣告要部署哪些模組、如何加以部署，以及如何在其間建立訊息路由。
author: kgremban
manager: philmea
ms.author: v-yiso
origin.date: 03/28/2019
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f4a562cab445398986c1b8f379f6cb90ca843342
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61363147"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>了解如何在 IoT Edge 中部署模組及建立路由

每個 IoT Edge 裝置會至少執行兩個模組：$edgeAgent 和 $edgeHub；這兩個模組都是 IoT Edge 執行階段的一部分。 此外，任何 IoT Edge 裝置都可以執行多個模組，以執行任意數目的處理程序。 由於您會一次將這些模組全部部署到裝置，因此 IoT Edge 提供一種方式來宣告要安裝哪些模組以及如何將其設定為共同運作。 

部署資訊清單是 JSON 文件，描述：

* **IoT Edge 代理程式**模組對應項，其中包含每個模組的容器映像、用來存取私人容器登錄的認證，以及如何建立和管理每個模組的指示。
* **IoT Edge 中樞**模組對應項，包括訊息如何在模組之間流動，以及最後如何到達 IoT 中樞。
* (選擇性) 任何其他模組對應項的所需屬性。

所有 IoT Edge 裝置都必須使用部署資訊清單來設定。 新安裝的 IoT Edge 執行階段會報告錯誤碼，直到使用有效的資訊清單進行設定。 

在 Azure IoT Edge 教學課程中，您會藉由完成 Azure IoT Edge 入口網站中的精靈，來建置部署資訊清單。 您也可以程式設計方式使用 REST 或 IoT 中樞服務 SDK，套用部署資訊清單。 如需詳細資訊，請參閱[了解 IoT Edge 部署](module-deployment-monitoring.md)。

## <a name="create-a-deployment-manifest"></a>建立部署資訊清單

概括而言，部署資訊清單是以其所需屬性設定的模組對應項清單。 部署資訊清單會告知 IoT Edge 裝置 (或裝置群組) 要安裝哪些模組，以及如何設定它們。 部署資訊清單包含每個模組對應項的「所需屬性」。 IoT Edge 裝置會回報每個模組的「報告屬性」。 

每個部署資訊清單中都必須要有兩個模組：`$edgeAgent` 及 `$edgeHub`。 這些模組是 IoT Edge 執行階段的一部分，負責管理 IoT Edge 裝置與其上執行的模組。 如需這些模組的詳細資訊，請參閱[了解 IoT Edge 執行階段及其架構](iot-edge-runtime.md)。

除了兩個執行階段模組之外，您可以新增最多 20 個您自己的模組，來在 IoT Edge 裝置上執行。 

僅包含 IoT Edge 執行階段 (edgeAgent 與 edgeHub) 的部署資訊清單為有效。

部署資訊清單遵循此結構：

```json
{
    "modulesContent": {
        "$edgeAgent": { // required
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": { //required
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "module1": {  // optional
            "properties.desired": {
                // desired properties of module1
            }
        },
        "module2": {  // optional
            "properties.desired": {
                // desired properties of module2
            }
        },
        ...
    }
}
```

## <a name="configure-modules"></a>設定模組

定義 IoT Edge 執行階段在您部署中安裝模組的方式。 IoT Edge 代理程式是一項執行階段元件，負責管理 IoT Edge 裝置的安裝、更新及狀態回報。 因此，$edgeAgent 模組對應項需要所有模組的設定及管理資訊。 此信息包括 IoT Edge 代理本身的配置参数。 

有关可以或必须包含的属性的完整列表，请参阅 [IoT Edge 代理和 IoT Edge 中心的属性](module-edgeagent-edgehub.md)。

$EdgeAgent 屬性遵循此結構：

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
                }
            }
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "module1": { // optional
                // configuration and management details
            },
            "module2": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>宣告路由

IoT Edge 中樞會管理模組、IoT 中樞和任何分葉裝置間的通訊。 因此，$edgeHub 模組對應項包含稱為 *routes* 的所需屬性，其會宣告訊息在部署中的傳遞方式。 您可以在相同部署內具有多個路由。

路由會以下列語法在 **$edgeHub** 所需屬性中宣告：

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "route1": "FROM <source> WHERE <condition> INTO <sink>",
            "route2": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

每個路由必須有來源和接收，但條件是可用來篩選訊息的選擇性項目。 


### <a name="source"></a>來源

來源會指定訊息來自於何處。 IoT Edge 可以路由來自分葉裝置或模組的訊息。

來源屬性可以是下列其中任何一個值：

| 來源 | 描述 |
| ------ | ----------- |
| `/*` | 所有來自任何模組或分葉裝置的裝置到雲端訊息或對應項變更通知 |
| `/twinChangeNotifications` | 任何來自任何模組或分葉裝置的對應項變更 (報告屬性) |
| `/messages/*` | 任何由模組或分葉裝置透過部分或無輸出傳送的裝置到雲端訊息 |
| `/messages/modules/*` | 由模組透過部分或無輸出傳送的任何裝置到雲端訊息 |
| `/messages/modules/<moduleId>/*` | 任何由特定模組透過部分或無輸出傳送的裝置到雲端訊息 |
| `/messages/modules/<moduleId>/outputs/*` | 任何由特定模組透過部分輸出傳送的裝置到雲端訊息 |
| `/messages/modules/<moduleId>/outputs/<output>` | 任何由特定模組透過特定輸出傳送的裝置到雲端訊息 |

### <a name="condition"></a>條件
條件在路由宣告中是選擇性項目。 如果您想要將所有訊息從接收傳遞至來源，請直接省略整個 **WHERE** 子句。 您可以使用 [IoT 中樞查詢語言](../iot-hub/iot-hub-devguide-routing-query-syntax.md)來篩選特定訊息或符合條件的訊息類型。 IoT Edge 路由不支援根據對應項標籤或屬性來篩選訊息。 

在 IoT Edge 的模組之間傳遞的訊息所用的格式，和您的裝置與 Azure IoT 中樞傳遞訊息時所用的格式相同。 所有訊息均採用 JSON 格式，且含 **systemProperties**、**appProperties** 和 **body** 參數。 

您可以使用下列語法，對三個參數中的任何一個參數建立查詢： 

* 系統屬性：`$<propertyName>` 或 `{$<propertyName>}`
* 應用程式屬性：`<propertyName>`
* 主體屬性：`$body.<propertyName>` 

如需如何針對訊息屬性建立查詢的範例，請參閱[裝置到雲端訊息路由查詢運算式](../iot-hub/iot-hub-devguide-routing-query-syntax.md)。

有個 IoT Edge 特有的範例，是想要篩選從分葉裝置送達閘道裝置的訊息。 來自模組的訊息包含稱為 **connectionModuleId** 的系統屬性。 因此，如果您將訊息直接從分葉裝置路由至 IoT 中樞，請使用下列路由來排除模組訊息：

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>接收
接收會定義要將訊息傳往何處。 只有模組和 IoT 中樞能接收訊息。 訊息無法路由到其他裝置。 接收屬性中沒有任何萬用字元選項。 

接收屬性可以是下列其中任何一個值：

| 接收 | 描述 |
| ---- | ----------- |
| `$upstream` | 將訊息傳送到 IoT 中樞 |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | 將訊息傳送到特定模組的特定輸入 |

IoT Edge 提供至少一次的保證。 IoT Edge 中心在本地存储消息，以防路由无法将消息传送到其接收器。 例如，如果 IoT Edge 中心无法连接到 IoT 中心，或者目标模块未连接。

IoT Edge 中心会一直存储消息，直到达到在 [IoT Edge 中心所需属性](module-edgeagent-edgehub.md)的 `storeAndForwardConfiguration.timeToLiveSecs` 属性中指定的时间。

## <a name="define-or-update-desired-properties"></a>定義或更新所需屬性 

部署資訊清單可為部署到 IoT Edge 裝置的每個模組指定所需屬性。 部署資訊清單中所需屬性會覆寫目前在模組對應項中的任何所需屬性。

如果您未在部署資訊清單中指定模組對應項的所需屬性，IoT 中樞就不會修改模組對應項。 您可以改為以程式設計方式來設定所需屬性。

您可以使用與修改裝置對應項相同的機制來修改模組對應項。 如需詳細資訊，請參閱[模組對應項開發人員指南](../iot-hub/iot-hub-devguide-module-twins.md)。   

## <a name="deployment-manifest-example"></a>部署資訊清單範例

下列範例顯示有效部署資訊清單文件的可能外觀。

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
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
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
              "createOptions": ""
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
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
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
          "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>後續步驟

* 有关在 $edgeAgent 和 $edgeHub 中可以或必须包含的属性的完整列表，请参阅 [IoT Edge 代理和 IoT Edge 中心的属性](module-edgeagent-edgehub.md)。

* 您現在知道如何使用 IoT Edge 模組，[了解開發 IoT Edge 模組的需求和工具](module-development.md)。

[lnk-deploy]: module-deployment-monitoring.md
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-routing-query-syntax.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
