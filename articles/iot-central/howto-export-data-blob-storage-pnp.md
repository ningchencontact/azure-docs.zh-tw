---
title: 將資料匯出至 Azure Blob 儲存體 | Microsoft Docs
description: 如何將資料從 Azure IoT Central 應用程式匯出至「Azure Blob 儲存體」
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 9ac650273a53da715b89e3233b30cf50710cfcfd
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973238"
---
# <a name="export-your-data-to-azure-blob-storage-preview-features"></a>將您的資料匯出至 Azure Blob 儲存體（預覽功能）

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*此主題適用於系統管理員。*

本文說明如何使用 Azure 中的連續資料匯出功能 IoT Central，定期將資料匯出至您的**Azure Blob 儲存體帳戶**或**Azure Data Lake Storage Gen2 儲存體帳戶**。 您可以將**遙測**、**裝置**和**裝置範本**匯出為 JSON 格式的檔案。 所匯出的資料可用來進行冷路徑分析 (例如，在 Azure Machine Learning 中為模型定型) 或在 Microsoft Power BI 中進行長期趨勢分析。

> [!Note]
> 當您開啟「連續資料匯出」時，只會取得從該時刻的資料。 目前，無法擷取「連續資料匯出」時的資料。 若要保留更多歷史資料，請儘早開啟「連續資料匯出」。


## <a name="prerequisites"></a>必要條件

- 您必須是 IoT Central 應用程式中的系統管理員

## <a name="create-storage-account"></a>建立儲存體帳戶
如果您沒有可匯出的現有儲存體，請遵循下列步驟：

1. [在 Azure 入口網站中建立新儲存體帳戶](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 您可以深入瞭解如何建立新的[Azure Blob 儲存體帳戶](https://aka.ms/blobdocscreatestorageaccount)或[Azure Data Lake Storage v2 儲存體帳戶](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account)。

    > [!Note] 
    > 如果您選擇將資料匯出至 ADLS v2 儲存體帳戶，您必須選擇 [**帳戶種類**] 做為 [ **BlobStorage**]。 

    > [!Note] 
    > 您可以將資料匯出到訂用帳戶中不同于隨用隨付 IoT Central 應用程式的儲存體帳戶。 在此情況下，您將使用連接字串來進行連線。

2. 在您的儲存體帳戶中建立容器。 移至您的儲存體帳戶。 在 [Blob 服務] 下，選取 [瀏覽 Blob]。 選取頂端的 [+ 容器] 以建立新的容器。


## <a name="set-up-continuous-data-export"></a>設定連續資料匯出

現在您已有要匯出資料的儲存體目的地，請遵循下列步驟來設定連續資料匯出。 

1. 登入您的 IoT Central 應用程式。

2. 在左側功能表中，選取 [**資料匯出**]。

    > [!Note]
    > 如果您在左側功能表中看不到 [資料匯出]，則您不是應用程式中的系統管理員。 請連絡系統管理員來設定資料匯出。

3. 選取右上方的 [ **+ 新增**] 按鈕。 選擇 [ **Azure Blob 儲存體**] 做為匯出的目的地。 

    > [!NOTE] 
    > 每一應用程式的匯出數目上限是 5。 

    ![建立新的連續資料匯出](media/howto-export-data-pnp/export-new2.png)

4. 在下拉式清單方塊中，選取您的**儲存體帳戶命名空間**。 您也可以挑選清單中的最後一個選項，也就是 [輸入連接字串]。 

    > [!NOTE] 
    > 您只會在與**IoT Central 應用程式相同的訂**用帳戶中看到儲存體帳戶命名空間。 如果您想要匯出到此訂用帳戶外的目的地，請選擇 [輸入連接字串]，然後參閱步驟 5。

    > [!NOTE] 
    > 如果是 7 天試用版應用程式，則設定連續資料匯出的唯一方式是透過連接字串。 這是因為 7 天試用版應用程式並沒有相關聯的 Azure 訂用帳戶。

    ![建立新的匯出至 Blob](media/howto-export-data-pnp/export-create-blob2.png)

5. (選擇性) 如果您選擇 [輸入連接字串]，就會顯示一個可供您貼上連接字串的新方塊。 若要取得儲存體帳戶的連接字串，請移至 Azure 入口網站中的儲存體帳戶：
    - 在 [**設定**] 底下，選取 [**存取金鑰**]
    - 複製 key1 連接字串或 key2 連接字串
 
6. 從下拉式清單方塊中選擇容器。 如果您沒有容器，請移至您在 Azure 入口網站中的儲存體帳戶：
    - 在 [ **Blob 服務**] 底下，選取 [ **blob**]。 按一下 [ **+ 容器**]，並為您的容器提供名稱。 選擇適用于您資料的公用存取層級（任何都可以使用連續資料匯出）。 從[Azure 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)檔深入瞭解。

7.  在 [要匯出的資料] 下，透過將類型設定為 [開啟] 以指定每種要匯出的資料類型。
   
    > [!NOTE] 
    > 資料會以 JSON 格式匯出。

8. 若要開啟連續資料匯出，請確定 [**資料匯出**切換] 為 [開啟]。 選取 [儲存]。

   ![設定連續資料匯出](media/howto-export-data-pnp/export-list-blob2.png)

9. 幾分鐘後，您的資料就會出現在您的儲存體帳戶中。


## <a name="path-structure"></a>路徑結構

遙測、裝置和裝置範本資料會每分鐘匯出至您的儲存體帳戶一次，每個檔案都包含自上次匯出檔案後的變更批次。 匯出的資料會以 JSON 格式放在三個資料夾中。 您儲存體帳戶中的預設路徑為：
- 遙測： {container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- 裝置： {container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- 裝置範本： {container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}

您可以流覽至檔案，然後選擇 [**編輯 blob** ] 索引標籤，以流覽 Azure 入口網站中匯出的檔案。

## <a name="data-format"></a>資料格式
### <a name="telemetry"></a>遙測

匯出的遙測資料會包含在該時間內從所有裝置 IoT Central 接收的所有新訊息。 匯出之檔案所使用的格式與 [IoT 中樞訊息路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c)匯出至 Blob 儲存體的訊息檔案格式相同。

> [!NOTE]
> 請確定您的裝置正在傳送具有 `contentType: application/JSON` 和 `contentEncoding:utf-8` （或 `utf-16`，`utf-32`）的訊息。 如需範例，請參閱[IoT 中樞檔](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body)。

> [!NOTE]
> 傳送遙測的裝置會以裝置識別碼來表示（請參閱下列各節）。 若要取得裝置名稱，請匯出裝置快照集。 請使用與裝置記錄之 **deviceId** 相符的 **connectionDeviceId** 來相互關聯每個訊息記錄。

下列範例會顯示 JSON 格式的記錄。

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

當第一次開啟「連續資料匯出」時，會匯出包含所有裝置的單一快照集。 每個裝置包含：
- IoT Central 中裝置的 `@id`
- 裝置的 `name`
- 來自[裝置佈建服務](https://aka.ms/iotcentraldocsdps) 的 `deviceId`
- 裝置範本資訊
- 屬性值

系統每隔一分鐘就會寫入新的快照集。 快照集包括：

- 自上一個快照集以來所新增的裝置。
- 自上一個快照集以來已變更屬性值的裝置。

> [!NOTE]
> 自上一個快照集以來所刪除的裝置則不會匯出。 目前，快照集沒有已刪除之裝置的指標。
>
> 每個裝置所屬的裝置範本都是以 [`instanceOf`] 欄位表示。 若要取得裝置範本的名稱，請匯出裝置範本快照集。

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

當第一次開啟「連續資料匯出」時，會匯出包含所有裝置範本的單一快照集。 每個裝置範本包含：
- 裝置範本的 `@id`
- 裝置範本的 `name`
- 裝置範本的 `version`
- 裝置 `capabilityModel`，包括其 `interfaces`，以及遙測、屬性和命令定義
- `cloudProperties` 定義
- 覆寫和初始值，以內嵌 `capabilityModel`

系統每隔一分鐘就會寫入新的快照集。 快照集包括：

- 自上一個快照集以來所新增的新裝置範本。 這包括新版本的裝置範本。
- 自上一個快照集以來已變更覆寫、初始值和雲端屬性定義的裝置範本。

> [!NOTE]
> 自上一個快照集以來所刪除的裝置範本則不會匯出。 目前，快照集沒有已刪除之裝置範本的指標。

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

現在您已經知道如何匯出資料，請繼續下一個步驟：

> [!div class="nextstepaction"]
> [如何使用 IoT Central 裝置橋接器連接其他 IoT 雲端](howto-build-iotc-device-bridge.md)
