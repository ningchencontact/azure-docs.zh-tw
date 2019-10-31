---
title: 匯出您的 Azure IoT Central 資料 |Microsoft Docs
description: 如何將資料從您的 Azure IoT Central 應用程式匯出至 Azure 事件中樞、Azure 服務匯流排和 Azure Blob 儲存體
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/15/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 118d2b42bc14a943aa3fa60b34aa1c151d5dea4c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176296"
---
# <a name="export-your-azure-iot-central-datapreview-features"></a>匯出您的 Azure IoT Central 資料（預覽功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*本主題適用於系統管理員。*

本文說明如何使用 Azure IoT Central 中的連續資料匯出功能，將您的資料匯出至 Azure 事件中樞、Azure 服務匯流排或 Azure Blob 儲存體實例。 資料會以 JSON 格式匯出，而且可以包含遙測、裝置資訊和裝置範本資訊。 將匯出的資料用於：

- 暖路徑深入解析與分析。 此選項包括觸發 Azure 串流分析中的自訂規則、在 Azure Logic Apps 中觸發自訂工作流程，或透過要轉換的 Azure Functions 進行傳遞。
- 冷路徑分析，例如在 Microsoft Power BI 中 Azure Machine Learning 或長期趨勢分析的定型模型。

> [!Note]
> 當您開啟「連續資料匯出」時，只會取得從該時刻的資料。 目前，無法擷取「連續資料匯出」時的資料。 若要保留更多歷史資料，請儘早開啟「連續資料匯出」。

## <a name="prerequisites"></a>必要條件

您必須是 IoT Central 應用程式中的系統管理員

## <a name="set-up-export-destination"></a>設定匯出目的地

在設定連續資料匯出之前，您的匯出目的地必須存在。

### <a name="create-event-hubs-namespace"></a>建立事件中樞命名空間

如果您沒有要匯出的現有事件中樞命名空間，請遵循下列步驟：

1. [在 Azure 入口網站中建立新事件中樞命名空間](https://ms.portal.azure.com/#create/Microsoft.EventHub)。 您可以透過 [Azure 事件中樞文件](../../event-hubs/event-hubs-create.md)深入了解。

2. 選擇訂用帳戶。 您可以將資料匯出至與隨用隨付 IoT Central 應用程式不同的訂用帳戶中的其他訂閱。 在此情況下，您會使用連接字串來連接。

3. 在「事件中樞」命名空間中建立一個事件中樞。 移至您的命名空間，然後選取頂端的 [+ 事件中樞] 以建立事件中樞執行個體。

### <a name="create-service-bus-namespace"></a>建立服務匯流排命名空間

如果您沒有要匯出的現有服務匯流排命名空間，請遵循下列步驟：

1. [在 Azure 入口網站中建立新的服務匯流排命名空間](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)。 您可以透過 [Azure 服務匯流排文件](../../service-bus-messaging/service-bus-create-namespace-portal.md)深入了解。
2. 選擇訂用帳戶。 您可以將資料匯出至與隨用隨付 IoT Central 應用程式不同的訂用帳戶中的其他訂閱。 在此情況下，您會使用連接字串來連接。

3. 移至您的「服務匯流排」命名空間，然後選取頂端的 [+ 佇列] 或 [+ 主題] 以建立要作為匯出目的地的佇列或主題。

當您選擇服務匯流排做為匯出目的地時，佇列和主題不能啟用會話或重複偵測。 如果啟用這其中一個選項，有些訊息就不會送達到您的佇列或主題中。

### <a name="create-storage-account"></a>建立儲存體帳戶

如果您沒有可匯出的現有 Azure 儲存體帳戶，請遵循下列步驟：

1. [在 Azure 入口網站中建立新儲存體帳戶](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 您可以深入瞭解如何建立新的[Azure Blob 儲存體帳戶](https://aka.ms/blobdocscreatestorageaccount)或[Azure Data Lake Storage v2 儲存體帳戶](../../storage/blobs/data-lake-storage-quickstart-create-account.md)。

    - 如果您選擇將資料匯出至 Azure Data Lake Storage v2 儲存體帳戶，則必須選擇 [ **BlobStorage** ] 作為**帳戶類型**。
    - 您可以將資料匯出到訂用帳戶中不同于隨用隨付 IoT Central 應用程式的儲存體帳戶。 在此情況下，您將使用連接字串來進行連線。

2. 在您的儲存體帳戶中建立容器。 移至您的儲存體帳戶。 在 [Blob 服務] 下，選取 [瀏覽 Blob]。 選取頂端的 [+ 容器] 以建立新的容器。

## <a name="set-up-continuous-data-export"></a>設定連續資料匯出

現在您已有目的地可匯出資料，請遵循下列步驟來設定連續資料匯出。

1. 登入您的 IoT Central 應用程式。

2. 在左窗格中，選取 [**資料匯出**]。

    > [!Note]
    > 如果您在左窗格中沒有看到 [資料匯出]，則您不是應用程式中的系統管理員。 請連絡系統管理員來設定資料匯出。

3. 選取右上方的 [ **+ 新增**] 按鈕。 選擇**Azure 事件中樞**、 **Azure 服務匯流排**或**Azure Blob 儲存體**的其中一個作為匯出目的地。 每個應用程式的匯出數目上限為五個。

    ![建立新的連續資料匯出](media/howto-export-data-pnp/export-new2.png)

4. 在下拉式清單方塊中，選取您的**事件中樞命名空間**、**服務匯流排命名空間**、**儲存體帳戶命名空間**，或**輸入連接字串**。

    - 您只會在與 IoT Central 應用程式相同的訂用帳戶中看到儲存體帳戶、事件中樞命名空間和服務匯流排命名空間。 如果您想要匯出到此訂用帳戶外的目的地，請選擇 [輸入連接字串]，然後參閱步驟 5。
    - 針對7天試用版應用程式，設定連續資料匯出的唯一方式是透過連接字串。 7天試用版應用程式沒有相關聯的 Azure 訂用帳戶。

    ![建立新的事件中樞](media/howto-export-data-pnp/export-eh.png)

5. (選擇性) 如果您選擇 [輸入連接字串]，就會顯示一個可供您貼上連接字串的新方塊。 取得下列項目的連接字串：
    - 事件中樞或服務匯流排，請移至 Azure 入口網站中的命名空間。
        - 在 [**設定**] 底下，選取 [**共用存取原則**]
        - 選擇預設的 [RootManageSharedAccessKey]，或建立新的原則
        - 複製主要連接字串或次要連接字串
    - 儲存體帳戶，請移至 Azure 入口網站中的儲存體帳戶：
        - 在 [**設定**] 底下，選取 [**存取金鑰**]
        - 複製 key1 連接字串或 key2 連接字串

6. 從下拉式清單方塊中選擇 [事件中樞]、[佇列]、[主題] 或 [容器]。

7. 在 [**要匯出的資料**] 底下，將 [類型] 設定為 [**開啟**]，以選擇要匯出的資料類型。

8. 若要開啟連續資料匯出，請確定 [**資料匯出**切換] 為 [**開啟**]。 選取 [儲存]。

9. 幾分鐘後，您的資料就會出現在您選擇的目的地。

## <a name="data-format"></a>資料格式

資料會以近乎即時的方式匯出至事件中樞或服務匯流排佇列或主題。

每分鐘會將資料匯出至您的儲存體帳戶一次，其中每個檔案都會包含自上次匯出檔案之後的變更批次。 匯出的資料會以 JSON 格式放在三個資料夾中。 您儲存體帳戶中的預設路徑為：

- 遙測： _{container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 裝置： _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 裝置範本： _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

您可以流覽至檔案，然後選擇 [**編輯 blob** ] 索引標籤，以流覽 Azure 入口網站中匯出的檔案。

匯出的遙測資料包含裝置傳送至 IoT Central 的全部訊息，而不只是遙測值本身。 匯出的裝置資料包含所有裝置的屬性和中繼資料變更，而匯出的裝置範本則包含所有裝置範本的變更。 匯出的資料位於 body 屬性中，並採用 JSON 格式。

### <a name="telemetry"></a>遙測

在 IoT Central 收到來自裝置的訊息之後，就會快速匯出新訊息。

- 事件中樞和服務匯流排中的每個匯出訊息，都包含裝置以 JSON 格式傳送至 body 屬性的完整訊息。
- Blob 儲存體中匯出的檔案會使用與[IoT 中樞訊息路由傳送](../../iot-hub/iot-hub-csharp-csharp-process-d2c.md)至 blob 儲存體所匯出的訊息檔案相同的格式。 確定您的裝置正在傳送具有 `contentType: application/JSON` 和 `contentEncoding:utf-8` （或 `utf-16`，`utf-32`）的訊息。 如需範例，請參閱[IoT 中樞檔](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body)。

傳送遙測的裝置會以裝置識別碼來表示（請參閱下列各節）。 若要取得裝置的名稱，請匯出裝置資料，並使用符合裝置訊息**deviceId**的**connectionDeviceId** ，將每個訊息相互關聯。

下列範例顯示在事件中樞或服務匯流排的佇列或主題中收到之遙測資料的相關訊息。

```json
{
  "body":{
    "temp":67.96099945281145,
    "humid":58.51139305465015,
    "pm25":36.91162432340187
  },
  "annotations":{
    "iothub-connection-device-id":"<deviceId>",
    "iothub-connection-auth-method":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id":"<generationId>",
    "iothub-enqueuedtime":1539381029965,
    "iothub-message-source":"Telemetry",
    "x-opt-sequence-number":25325,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539381030200
  },
  "sequenceNumber":25325,
  "enqueuedTimeUtc":"2018-10-12T21:50:30.200Z",
  "offset":"<offset>",
  "properties":{
    "content_type":"application/json",
    "content_encoding":"utf-8"
  }
}
```

下列範例會以 JSON 格式顯示 blob 儲存體中的記錄：

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

### <a name="devices"></a>裝置

包含裝置資料的訊息會以每幾分鐘一次的頻率，傳送到您的事件中樞或「服務匯流排」佇列或主題。 每分鐘會將新的快照集寫入 blob 儲存體。 每個訊息或快照集都包含下列資料：

- 已新增的新裝置
- 屬性值已變更的裝置

快照中的每個訊息或記錄都代表自上次匯出訊息之後，對裝置所做的一或多項變更。 資訊包括：

- IoT Central 中裝置的 `@id`
- 裝置的 `name`
- 來自[裝置佈建服務](/azure/iot-central/core/howto-connect-nodejs) 的 `deviceId`
- 裝置範本資訊
- 屬性值

系統不會匯出自上個批次後刪除的裝置。 目前，在匯出的訊息中並沒有任何適用於已刪除之裝置的指標。

每個裝置所屬的裝置範本會以裝置範本識別碼來表示。 若要取得裝置範本的名稱，請務必一併匯出裝置範本資料。

下列範例示範事件中樞或「服務匯流排」佇列或主題中裝置資料的相關訊息：

```json
{
  "body":{
    "@id":"<id>",
    "@type":"Device",
    "displayName":"Airbox - 266d30aedn5",
    "data":{
      "$cloudProperties":{
        "Color":"blue"
      },
      "EnvironmentalSensor":{
        "thsensormodel":{
          "reported":{
            "value":"A1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "pm25sensormodel":{
          "reported":{
            "value":"P1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      },
      "urn_azureiot_DeviceManagement_DeviceInformation":{
        "totalStorage":{
          "reported":{
            "value":3088.1959855710156,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "totalMemory":{
          "reported":{
            "value":16005.703586477555,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      }
    },
    "instanceOf":"<templateId>",
    "deviceId":"<deviceId>",
    "simulated":true
  },
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2019-10-02T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

匯出的檔案包含每筆記錄一行。 下列範例會顯示 JSON 格式的記錄。

```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates"></a>裝置範本

包含裝置範本資料的訊息會以每幾分鐘一次的頻率，傳送到您的事件中樞或「服務匯流排」佇列或主題。 新的快照集會每分鐘寫入 blob 儲存體一次。 因此，每隔幾分鐘就會收到一批訊息，其中包含下列資料：

包含裝置範本資料的訊息會每隔幾分鐘傳送至您的事件中樞或服務匯流排佇列或主題。 每分鐘會將新的快照集寫入 blob 儲存體。 每個訊息或快照集都包含下列資料：

- 新增或建立版本的新裝置範本
- 具有已變更功能模型、雲端屬性、覆寫和初始值的裝置範本

每個訊息或快照記錄代表自上次匯出訊息之後，對裝置範本的一或多項變更。 在每個訊息或記錄中傳送的資訊包括：

- 裝置範本的 `@id`
- 裝置範本的 `name`
- 裝置範本的 `version`
- 裝置 `capabilityModel` 包括其 `interfaces`，以及遙測、屬性和命令定義
- `cloudProperties` 定義
- 覆寫和初始值，以內嵌 `capabilityModel`

系統不會匯出自上個批次後刪除的裝置範本。 目前，在匯出的訊息中並沒有任何適用於已刪除之裝置範本的指標。

下列範例顯示事件中樞或服務匯流排佇列或主題中的裝置範本訊息：

```json
{
  "body":{
    "@id":"<template-id>",
    "@type":"DeviceModelDefinition",
    "displayName":"Airbox",
    "capabilityModel":{
      "@id":"<id>",
      "@type":"CapabilityModel",
      "implements":[
        {
          "@id":"<id>",
          "@type":"InterfaceInstance",
          "name":"EnvironmentalSensor",
          "schema":{
            "@id":"<id>",
            "@type":"Interface",
            "comment":"Requires temperature and humidity sensors.",
            "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
            "displayName":"Environmental Sensor",
            "contents":[
              {
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current temperature on the device",
                "displayName":"Temperature",
                "name":"temp",
                "schema":"double",
                "unit":"Units/Temperature/celsius",
                "valueDetail":{
                  "@id":"<id>",
                  "@type":"ValueDetail/NumberValueDetail",
                  "minValue":{
                    "@value":"50"
                  }
                },
                "visualizationDetail":{
                  "@id":"<id>",
                  "@type":"VisualizationDetail"
                }
              },
              {
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current humidity on the device",
                "displayName":"Humidity",
                "name":"humid",
                "schema":"integer"
              },
              {
                "@id":"<id>",
                "@type":"Telemetry",
                "description":"Current PM2.5 on the device",
                "displayName":"PM2.5",
                "name":"pm25",
                "schema":"integer"
              },
              {
                "@id":"<id>",
                "@type":"Property",
                "description":"T&H Sensor Model Name",
                "displayName":"T&H Sensor Model",
                "name":"thsensormodel",
                "schema":"string"
              },
              {
                "@id":"<id>",
                "@type":"Property",
                "description":"PM2.5 Sensor Model Name",
                "displayName":"PM2.5 Sensor Model",
                "name":"pm25sensormodel",
                "schema":"string"
              }
            ]
          }
        },
        {
          "@id":"<id>",
          "@type":"InterfaceInstance",
          "name":"urn_azureiot_DeviceManagement_DeviceInformation",
          "schema":{
            "@id":"<id>",
            "@type":"Interface",
            "displayName":"Device information",
            "contents":[
              {
                "@id":"<id>",
                "@type":"Property",
                "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
                "displayName":"Total storage",
                "name":"totalStorage",
                "displayUnit":"kilobytes",
                "schema":"long"
              },
              {
                "@id":"<id>",
                "@type":"Property",
                "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
                "displayName":"Total memory",
                "name":"totalMemory",
                "displayUnit":"kilobytes",
                "schema":"long"
              }
            ]
          }
        }
      ],
      "displayName":"AAEONAirbox52"
    },
    "solutionModel":{
      "@id":"<id>",
      "@type":"SolutionModel",
      "cloudProperties":[
        {
          "@id":"<id>",
          "@type":"CloudProperty",
          "displayName":"Color",
          "name":"Color",
          "schema":"string",
          "valueDetail":{
            "@id":"<id>",
            "@type":"ValueDetail/StringValueDetail"
          },
          "visualizationDetail":{
            "@id":"<id>",
            "@type":"VisualizationDetail"
          }
        }
      ]
    },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

匯出的檔案包含每筆記錄一行。 下列範例會顯示 JSON 格式的記錄。

```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>後續步驟

既然您已知道如何將資料匯出至「Azure 事件中樞」和「Azure 服務匯流排」，請繼續進行下一個步驟：

> [!div class="nextstepaction"]
> [如何觸發 Azure Functions](howto-trigger-azure-functions.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
