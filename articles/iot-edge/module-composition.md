---
title: Azure IoT Edge 模組撰寫 | Microsoft Docs
description: 了解 Azure IoT Edge 模組有什麼內容以及如何重複使用
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/23/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 58b4c6769f6eb441e9ccaf041f3d56f4af9a0831
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>了解如何使用、設定以及重複使用 IoT Edge 模組 - 預覽

每個 IoT Edge 裝置會至少執行兩個模組：$edgeAgent 和 $edgeHub，由這兩個模組組成 IoT Edge 執行階段。 除了這兩個標準的模組，任何 IoT Edge 裝置都可以執行多個模組，以執行任意數目的處理程序。 當您將所有這些模組一次部署到裝置時，您需要有方法來宣告包含的模組，以及這些模組與彼此互動的方式。 

部署資訊清單是 JSON 文件，描述：

* 必須部署哪些 IoT Edge 模組，以及其建立和管理選項。
* Edge 中樞的組態，包括訊息如何在模組之間流動，及最後如何到達 IoT 中樞。
* (選擇性) 在模組對應項預期屬性中設定的值，以設定個別模組應用程式。

所有 IoT Edge 裝置都需要使用部署資訊清單來設定。 新安裝的 IoT Edge 執行階段會報告錯誤碼，直到使用有效的資訊清單進行設定。 

在 Azure IoT Edge 教學課程中，您會藉由完成 Azure IoT Edge 入口網站中的精靈，來建置部署資訊清單。 您也可以程式設計方式使用 REST 或 IoT 中樞服務 SDK，套用部署資訊清單。 請參閱[部署與監視][lnk-deploy]以取得 IoT Edge 部署的詳細資訊。

## <a name="create-a-deployment-manifest"></a>建立部署資訊清單

概括而言，部署資訊清單會為 IoT Edge 裝置上部署之 IoT Edge 模組設定模組對應項的所需屬性。 這兩個模組永遠存在：Edge 代理程式和 Edge 中樞。

僅包含 IoT Edge 執行階段 (代理程式和中樞) 的部署資訊清單才是有效的。

資訊清單會遵循此結構：

```json
{
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
            }
        },
        "$edgeHub": {
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "{module1}": {  // optional
            "properties.desired": {
                // desired properties of module with id {module1}
            }
        },
        "{module2}": {  // optional
            ...
        },
        ...
    }
}
```

## <a name="configure-modules"></a>設定模組

除了為任何要部署的模組建立所需屬性外，您必須告訴 IoT Edge 執行階段如何安裝這些屬性。 所有模組的組態和管理資訊都會進入 **$edgeAgent** 所需屬性中。 這項資訊包含 Edge 代理程式本身的組態參數。 

如需可包含或必須包含的屬性完整清單，請參閱 [Edge 代理程式和 Edge 中樞的屬性](module-edgeagent-edgehub.md)。

$EdgeAgent 屬性遵循此結構：

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
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
            "{module1}": { //optional
                // configuration and management details
            },
            "{module2}": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>宣告路由

Edge 中樞提供方法以宣告方式在模組之間及模組與 IoT 中樞之間路由訊息。 Edge 中樞會管理所有通訊，因此路由資訊會進入 **$edgeHub** 所需屬性中。 您可以在相同部署內具有多個路由。

路由會以下列語法在 **$edgeHub** 所需屬性中宣告：

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "{route1}": "FROM <source> WHERE <condition> INTO <sink>",
            "{route2}": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

每個路由必須有來源和接收，但條件是可用來篩選訊息的選擇性項目。 


### <a name="source"></a>來源
來源會指定訊息來自於何處。 可以是下列其中任何一個值：

| 來源 | 說明 |
| ------ | ----------- |
| `/*` | 來自任何裝置或模組的所有裝置到雲端訊息 |
| `/messages/*` | 由裝置或模組透過部分或無輸出傳送的任何裝置到雲端訊息 |
| `/messages/modules/*` | 由模組透過部分或無輸出傳送的任何裝置到雲端訊息 |
| `/messages/modules/{moduleId}/*` | 由 {moduleId} 使用無輸出傳送的任何裝置到雲端訊息 |
| `/messages/modules/{moduleId}/outputs/*` | 由 {moduleId} 使用部分輸出傳送的任何裝置到雲端訊息 |
| `/messages/modules/{moduleId}/outputs/{output}` | 由 {moduleId} 使用 {output} 傳送的任何裝置到雲端訊息 |

### <a name="condition"></a>條件
條件在路由宣告中是選擇性項目。 如果您想要將所有訊息從接收傳遞至來源，請直接省略整個 **WHERE** 子句。 您可以使用 [IoT 中樞查詢語言][lnk-iothub-query]來篩選特定訊息或符合條件的訊息類型。

在 IoT Edge 的模組之間傳遞的訊息所用的格式，和您的裝置與 Azure IoT 中樞傳遞訊息時所用的格式相同。 所有訊息均採用 JSON 格式，且含 **systemProperties**、**appProperties** 和 **body** 參數。 

您可以使用下列語法，對所有三個參數建立查詢： 

* 系統屬性：`$<propertyName>` 或 `{$<propertyName>}`
* 應用程式屬性：`<propertyName>`
* 主體屬性：`$body.<propertyName>` 

如需如何針對訊息屬性建立查詢的範例，請參閱[裝置到雲端訊息路由查詢運算式](../iot-hub/iot-hub-devguide-query-language.md#device-to-cloud-message-routes-query-expressions)。

有個 IoT Edge 特有的範例，是想要篩選從分葉裝置送達閘道裝置的訊息。 來自模組的訊息包含稱為 **connectionModuleId** 的系統屬性。 因此，如果您將訊息直接從分葉裝置路由至 IoT 中樞，請使用下列路由來排除模組訊息：

```sql
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>接收
接收會定義要將訊息傳往何處。 可以是下列其中任何一個值：

| 接收 | 說明 |
| ---- | ----------- |
| `$upstream` | 將訊息傳送到 IoT 中樞 |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | 將訊息傳送到模組 `{moduleId}` 的輸入 `{input}` |

務必記住，Edge 中樞提供至少一次保證，這表示訊息會儲存在本機，以免路由無法將訊息傳遞到其接收，例如，Edge 中樞無法連線到 IoT 中樞，或目標模組未連線。

[Edge 中樞所需屬性](module-edgeagent-edgehub.md)的 `storeAndForwardConfiguration.timeToLiveSecs` 屬性會指定訊息能在 Edge 中樞內儲存多久。

## <a name="define-or-update-desired-properties"></a>定義或更新所需屬性 

部署資訊清單可為部署到 IoT Edge 裝置的每個模組，指定模組對應項的所需屬性。 在部署資訊清單中指定預期屬性時，它們會覆寫目前在模組對應項中任何預期屬性。

如果您未在部署資訊清單中指定模組對應項的預期屬性，IoT 中樞就不會修改模組對應項，您將無法以程式設計方式設定預期屬性。

您可以使用與修改裝置對應項相同的機制來修改模組對應項。 如需進一步資訊，請參閱[裝置對應項開發人員指南](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)。   

## <a name="deployment-manifest-example"></a>部署資訊清單範例

這是部署資訊清單 JSON 文件的範例。

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
                    "createOptions": ""
                    }
                },
                "edgeHub": {
                    "type": "docker",
                    "status": "running",
                    "restartPolicy": "always",
                    "settings": {
                    "image": "microsoft/azureiotedge-hub:1.0-preview",
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
                    "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
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

* 如需 $edgeAgent 和 $edgeHub 中可包含或必須包含的屬性完整清單，請參閱 [Edge 代理程式和 Edge 中樞的屬性](module-edgeagent-edgehub.md)。

* 您現在知道如何使用 IoT Edge 模組，[了解開發 IoT Edge 模組的需求和工具][lnk-module-dev]。

[lnk-deploy]: module-deployment-monitoring.md
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
