---
title: 將資料匯出至 Azure Blob 儲存體 | Microsoft Docs
description: 如何將資料從 Azure IoT Central 應用程式匯出至「Azure Blob 儲存體」
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 9ae57b8ab26780ea975ad74f3348a0deaf8c9cc8
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464634"
---
# <a name="export-your-data-to-azure-blob-storage"></a>將資料匯出至 Azure Blob 儲存體

*此主題適用於系統管理員。*

本文說明如何使用 Azure IoT Central 中的連續資料匯出功能，定期將資料匯出至您**Azure Blob 儲存體帳戶**。 您可以將**度量**、**裝置**及**裝置範本**匯出成 Apache Avro 格式的檔案。 所匯出的資料可用來進行冷路徑分析 (例如，在 Azure Machine Learning 中為模型定型) 或在 Microsoft Power BI 中進行長期趨勢分析。

> [!Note]
> 同樣地，當您開啟連續資料匯出時，只會取得從該時刻起的資料。 目前，無法擷取「連續資料匯出」時的資料。 若要保留更多歷史資料，請儘早開啟「連續資料匯出」。


## <a name="prerequisites"></a>必要條件

- 您必須是 IoT Central 應用程式中的系統管理員


## <a name="set-up-export-destination"></a>設定匯出目的地

如果您沒有將匯出到現有的儲存體，請遵循下列步驟：

## <a name="create-storage-account"></a>建立儲存體帳戶

1. [在 Azure 入口網站中建立新儲存體帳戶](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 您可以透過 [Azure 儲存體文件](https://aka.ms/blobdocscreatestorageaccount)深入了解。
2. 針對帳戶類型，請選擇 [一般用途]  或 [Blob 儲存體]  。
3. 選擇訂用帳戶。 

    > [!Note] 
    > 您現在可以將資料匯出至與隨用隨付 IoT Central 應用程式之訂用帳戶**不同**的其他訂用帳戶。 在此情況下，您將使用連接字串來進行連線。

4. 在您的儲存體帳戶中建立容器。 移至您的儲存體帳戶。 在 [Blob 服務]  下，選取 [瀏覽 Blob]  。 選取  **+ 容器**頂端建立新的容器


## <a name="set-up-continuous-data-export"></a>設定連續資料匯出

既然您已將資料匯出至儲存體目的地，請遵循下列步驟來設定連續資料匯出。 

1. 登入您的 IoT Central 應用程式。

2. 在左側功能表中，選取**連續資料匯出**。

    > [!Note]
    > 如果在左側功能表中沒有看到 [連續資料匯出]，即表示您不是應用程式的系統管理員。 請連絡系統管理員來設定資料匯出。

    ![建立新的 cde 事件中樞](media/howto-export-data/export_menu1.png)

3. 選取  **+ 新增**右上角的按鈕。 選擇**Azure Blob 儲存體**做為您的匯出目的地。 

    > [!NOTE] 
    > 每一應用程式的匯出數目上限是 5。 

    ![建立新的連續資料匯出](media/howto-export-data/export_new1.png)

4. 在下拉式清單方塊中，選取您**儲存體帳戶的命名空間**。 您也可以挑選清單中的最後一個選項，也就是 [輸入連接字串]  。 

    > [!NOTE] 
    > 您只會看到儲存體帳戶中的命名空間**相同的訂用帳戶與您的 IoT Central 應用程式**。 如果您想要匯出到此訂用帳戶外的目的地，請選擇 [輸入連接字串]  ，然後參閱步驟 5。

    > [!NOTE] 
    > 如果是 7 天試用版應用程式，則設定連續資料匯出的唯一方式是透過連接字串。 這是因為 7 天試用版應用程式並沒有相關聯的 Azure 訂用帳戶。

    ![建立新的 cde 事件中樞](media/howto-export-data/export-create-blob.png)

5. (選擇性) 如果您選擇 [輸入連接字串]  ，就會顯示一個可供您貼上連接字串的新方塊。 取得下列項目的連接字串：
    - 儲存體帳戶，請移至 Azure 入口網站中的儲存體帳戶。
        - 底下**設定**，選取**存取金鑰**
        - 複製 key1 連接字串或 key2 連接字串
 
6. 從下拉式清單方塊選擇容器。

7. 在 [要匯出的資料]  下，透過將類型設定為 [開啟]  以指定每種要匯出的資料類型。

6. 若要開啟連續資料匯出，請務必將 [資料匯出]  設定為 [開啟]  。 選取 [ **儲存**]。

   ![設定連續資料匯出](media/howto-export-data/export-list-blob.png)

7. 幾分鐘後，您的資料就會出現在所選擇的目的地中。


## <a name="export-to-azure-blob-storage"></a>匯出至 Azure Blob 儲存體

度量、裝置及裝置範本資料會以每分鐘一次的頻率，匯出到您的儲存體帳戶，其中每個檔案都會包含自上次匯出檔案之後的一批變更。 匯出的資料會採用 [Apache Avro](https://avro.apache.org/docs/current/index.html) 格式，且會匯出到三個資料夾。 您儲存體帳戶中的預設路徑為：
- 訊息：{container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
- 裝置：{container}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
- 裝置範本：{container}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro

### <a name="measurements"></a>度量

匯出的度量資料包含 IoT Central 在該時間期間從所有裝置收到的所有新訊息。 匯出之檔案所使用的格式與 [IoT 中樞訊息路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c)匯出至 Blob 儲存體的訊息檔案格式相同。

> [!NOTE]
> 傳送量測的裝置會以裝置識別碼來表示 (請參閱以下各節)。 若要取得裝置名稱，請匯出裝置快照集。 請使用與裝置記錄之 **deviceId** 相符的 **connectionDeviceId** 來相互關聯每個訊息記錄。

下列範例顯示已解碼之 Avro 檔案中的記錄：

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "<connectionDeviceId>",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "<generationId>",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>裝置

當第一次開啟「連續資料匯出」時，會匯出包含所有裝置的單一快照集。 每個裝置包含：
- IoT Central 中裝置的 `id`
- 裝置的 `name`
- 來自[裝置佈建服務](https://aka.ms/iotcentraldocsdps) 的 `deviceId`
- 裝置範本資訊
- 屬性值
- 設定值

系統每隔一分鐘就會寫入新的快照集。 快照集包括：

- 自上一個快照集以來所新增的裝置。
- 自上一個快照集以來屬性與設定值已變更的裝置。

> [!NOTE]
> 自上一個快照集以來所刪除的裝置則不會匯出。 目前，快照集沒有已刪除之裝置的指標。
>
> 每個裝置所屬的裝置範本會以裝置範本識別碼來表示。 若要取得裝置範本的名稱，請匯出裝置範本快照集。

已解碼之 Avro 檔案中的記錄看起來可能像這樣：

```json
{
    "id": "<id>",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
        "id": "<template id>",
        "version": "1.0.0"
    },
    "properties": {
        "cloud": {
            "location": "New York",
            "maintCon": true,
            "tempThresh": 20
        },
        "device": {
            "lastReboot": "2018-02-09T22:22:47.156Z"
        }
    },
    "settings": {
        "device": {
            "fanSpeed": 0
        }
    }
}
```

### <a name="device-templates"></a>裝置範本

當第一次開啟「連續資料匯出」時，會匯出包含所有裝置範本的單一快照集。 每個裝置範本包含：
- 裝置範本的 `id`
- 裝置範本的 `name`
- 裝置範本的 `version`
- 量測資料類型和最小值/最大值。
- 屬性資料類型和預設值。
- 設定資料類型和預設值。

系統每隔一分鐘就會寫入新的快照集。 快照集包括：

- 自上一個快照集以來所新增的新裝置範本。
- 自上一個快照集以來量測、屬性與設定定義已變更的裝置範本。

> [!NOTE]
> 自上一個快照集以來所刪除的裝置範本則不會匯出。 目前，快照集沒有已刪除之裝置範本的指標。

已解碼之 Avro 檔案中的記錄看起來可能像這樣：

```json
{
    "id": "<id>",
    "name": "Refrigerated Vending Machine",
    "version": "1.0.0",
    "measurements": {
        "telemetry": {
            "humidity": {
                "dataType": "double",
                "name": "Humidity"
            },
            "magnetometerX": {
                "dataType": "double",
                "name": "Magnetometer X"
            },
            "magnetometerY": {
                "dataType": "double",
                "name": "Magnetometer Y"
            },
            "magnetometerZ": {
                "dataType": "double",
                "name": "Magnetometer Z"
            }
        },
        "states": {
            "connectivity": {
                "dataType": "enum",
                "name": "Connectivity"
            }
        },
        "events": {
            "opened": {
                "name": "Door Opened",
                "category": "informational"
            }
        }
    },
    "settings": {
        "device": {
            "fanSpeed": {
                "dataType": "double",
                "name": "Fan Speed",
                "initialValue": 0
            }
        }
    },
    "properties": {
        "cloud": {
            "location": {
                "dataType": "string",
                "name": "Location",
                "initialValue": "Seattle"
            },
            "maintCon": {
                "dataType": "boolean",
                "name": "Maintenance Contract",
                "initialValue": true
            },
            "tempThresh": {
                "dataType": "double",
                "name": "Temperature Alert Threshold",
                "initialValue": 30
            }
        },
        "device": {
            "lastReboot": {
                "dataType": "dateTime",
                "name": "Last Reboot"
            }
        }
    }
}
```

## <a name="read-exported-avro-files"></a>讀取匯出的 Avro 檔案

Avro 是二進位格式，因此讀取檔案時，無法以檔案原始狀態來讀取。 該檔案可解碼為 JSON 格式。 下列範例說明如何剖析度量、裝置及裝置範本 Avro 檔案。 這些範例對應到上一節中所述的範例。

### <a name="read-avro-files-by-using-python"></a>使用 Python 來讀取 Avro 檔案

#### <a name="install-pandas-and-the-pandavro-package"></a>安裝 pandas 與 pandavro 套件

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>剖析度量 Avro 檔案

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)

```

#### <a name="parse-a-devices-avro-file"></a>剖析裝置 Avro 檔案

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["deviceId"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)

```

#### <a name="parse-a-device-templates-avro-file"></a>剖析裝置範本 Avro 檔案

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    templates = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available in the id and version columns.
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>使用 C# 來讀取 Avro 檔案

#### <a name="install-the-microsofthadoopavro-package"></a>安裝 Microsoft.Hadoop.Avro 套件

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>剖析度量 Avro 檔案

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;
using Newtonsoft.Json;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    var systemProperties = record.GetField<IDictionary<string, object>>("SystemProperties");
                    var deviceId = systemProperties["connectionDeviceId"] as string;
                    Console.WriteLine("Device ID: {0}", deviceId);

                    using (var stream = new MemoryStream(record.GetField<byte[]>("Body")))
                    {
                        using (var streamReader = new StreamReader(stream, Encoding.UTF8))
                        {
                            var body = JsonSerializer.Create().Deserialize(streamReader, typeof(IDictionary<string, dynamic>)) as IDictionary<string, dynamic>;
                            var humidity = body["humidity"];
                            Console.WriteLine("Humidity: {0}", humidity);
                        }
                    }
                }
            }
        }
    }
}
```

#### <a name="parse-a-devices-avro-file"></a>剖析裝置 Avro 檔案

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var deviceId = record.GetField<string>("deviceId");

                    // The device template information is stored in a sub-record
                    // under the deviceTemplate field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "Device ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
                        deviceId,
                        templateId,
                        templateVersion,
                        fanSpeed
                    );
                }
            }
        }
    }
}

```

#### <a name="parse-a-device-templates-avro-file"></a>剖析裝置範本 Avro 檔案

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {1}, Version: {2}, Fan Speed: {3}",
                        id,
                        version,
                        fanSpeed
                    );
                }
            }
        }
    }
}
```

### <a name="read-avro-files-by-using-javascript"></a>使用 Javascript 來讀取 Avro 檔案

#### <a name="install-the-avsc-package"></a>安裝 avsc 套件

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>剖析度量 Avro 檔案

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device ID and humidity from each record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the system properties.
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the body from a buffer to a string and parse it.
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property from the body.
        const humidity = body.humidity;

        // Log the retrieved device ID and humidity.
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-devices-avro-file"></a>剖析裝置 Avro 檔案

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the deviceId property.
        const deviceId = record.deviceId;

        // Fetch the template ID and version from the deviceTemplate property.
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device ID and humidity.
        console.log(`deviceID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-device-templates-avro-file"></a>剖析裝置範本 Avro 檔案

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template ID and version from the id and verison properties.
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device id and humidity.
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>後續步驟

現在您已經知道如何匯出資料，請繼續下一個步驟：

> [!div class="nextstepaction"]
> [如何在 Power BI 中將資料視覺化](howto-connect-powerbi.md)
