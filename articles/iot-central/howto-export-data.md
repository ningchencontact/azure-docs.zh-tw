---
title: 匯出 Azure IoT Central 中的資料 | Microsoft Docs
description: 如何從 Azure IoT Central 應用程式匯出資料
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/3/2018
ms.topic: article
ms.prod: azure-iot-central
manager: peterpr
ms.openlocfilehash: 5b9564dfe40f292d289ee9ed680e816771d0b0ed
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282869"
---
# <a name="export-your-data-in-azure-iot-central"></a>匯出 Azure IoT Central 中的資料

使用「連續資料匯出」可定期將資料匯出至 Azure Blob 儲存體帳戶。 選擇在 [Apache AVRO](https://avro.apache.org/docs/current/index.html) 格式的檔案中匯出**量測**、**裝置**和**裝置範本**。 使用所匯出的資料進行冷路徑分析 (例如，在 Azure Machine Learning 中訓練模型) 或在 Power BI 中進行長期趨勢分析。

> [!Note]
> 當您開啟「連續資料匯出」時，只會取得從該時刻起所進入的資料。 目前無法在「連續資料匯出」關閉時擷取資料。 請盡早開啟「連續資料匯出」以保留更多歷史資料！

## <a name="prerequisites"></a>必要條件

- 延長的 30 天試用版應用程式或付費應用程式
- 具有 Azure 訂用帳戶的 Azure 帳戶
- 同一個 Azure 帳戶也是 IoT Central 應用程式的系統管理員
- 同一個 Azure 帳戶有權限可以建立儲存體帳戶，或存取相同 Azure 訂用帳戶中的現有儲存體帳戶

## <a name="types-of-data-to-export"></a>要匯出的資料類型

### <a name="measurements"></a>量測

裝置所傳送的量測會匯出至儲存體帳戶。 量測資料大約一分鐘會匯出一次，其中包含 IoT Central 在該時間範圍內從所有裝置收到的所有新訊息。 所匯出 AVRO 檔案的格式，會和 [IoT 中樞訊息路由](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-csharp-csharp-process-d2c)匯出至 Blob 儲存體的訊息檔案格式相同。

> [!NOTE]
> 傳送量測的裝置會以裝置識別碼來表示 (請參閱下文)。 若要取得裝置的名稱，則還需要匯出裝置快照集。 您可以使用與裝置識別碼相符的 connectionDeviceId 來將每個訊息記錄相互關聯。

這是已解碼 AVRO 檔案中的記錄範例。

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "2383d8ba-c98c-403a-b4d5-8963859643bb",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "636614021491644195",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>裝置

當您第一次開啟「連續資料匯出」時，系統會匯出包含所有裝置的單一快照集。 其中包括：
- 裝置識別碼
- 裝置名稱
- 裝置範本識別碼
- 屬性值
- 設定值

系統大約一分鐘會寫入一次包含下列項目的新快照集：

- 自上一個快照集以來所新增的裝置
- 自上一個快照集以來有變更屬性和設定值的裝置

> [!NOTE]
> 自上一個快照集以來所刪除的裝置則不會匯出。 在此時刪除的裝置，其快照集中不會有任何指標。

> [!NOTE]
> 每個裝置所屬的裝置範本會以裝置範本識別碼來表示。 若要取得裝置範本的名稱，則還需要匯出裝置範本快照集。

已解碼 AVRO 檔案中的每個記錄看起來會像下面這樣：

```json
{
    "id": "2383d8ba-c98c-403a-b4d5-8963859643bb",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceTemplate": {
        "id": "c318d580-39fc-4aca-b995-843719821049",
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

當您第一次開啟「連續資料匯出」時，系統會匯出包含所有裝置範本的單一快照集。 其中包括： 
- 裝置範本識別碼
- 量測資料類型和最小值/最大值
- 屬性資料類型和預設值
- 設定資料類型和預設值

系統大約一分鐘會寫入一次包含下列項目的新快照集：

- 自上一個快照集以來所新增的裝置範本
- 自上一個快照集以來有變更量測、屬性和設定定義的裝置範本

> [!NOTE]
> 自上一個快照集以來所刪除的裝置範本則不會匯出。 在此時刪除的裝置範本，其快照集中不會有任何指標。

已解碼 AVRO 檔案中的每個記錄看起來會像下面這樣：

```json
{
    "id": "c318d580-39fc-4aca-b995-843719821049",
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

## <a name="how-to-set-up-data-export"></a>如何設定資料匯出

1. 如果您還沒有 Azure 儲存體帳戶，請在**應用程式所在的 Azure 訂用帳戶中**建立帳戶。 [按一下這裡](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)跳至 Azure 入口網站，以建立新的 Azure 儲存體帳戶。

    - 選擇「一般用途」或「Blob 儲存體」帳戶類型
    - 選取 IoT Central 應用程式所在的訂用帳戶。 如果您沒有看到訂用帳戶，您可能需要登入不同 Azure 帳戶，或要求訂用帳戶的存取權。
    - 您可以選擇現有資源群組或建立新群組。 了解[如何建立新的儲存體帳戶](https://aka.ms/blobdocscreatestorageaccount)

2. 在儲存體帳戶中建立容器以供存放匯出的 IoT Central 資料。 移至 [儲存體帳戶] -> [瀏覽 Blob]，然後建立新的容器。 ![建立容器映像](media/howto-export-data/createcontainer.png)

3. 使用相同的 Azure 帳戶登入 IoT Central 應用程式。
1. 移至 [系統管理] -> [連續資料匯出]。
![IoT Central CDE](media/howto-export-data/continuousdataexport.PNG)
1. 使用下拉式清單挑選儲存體帳戶和容器。 然後，使用開關來開啟或關閉要匯出的不同資料類型。
1. 最後，使用開關開啟「連續資料匯出」，並點擊 [儲存]。
1. 等候幾分鐘的時間，然後您應該就會看到資料出現在儲存體帳戶中。 您可以瀏覽至儲存體帳戶、選取 [瀏覽 Blob]、選取容器，然後就會看到三個資料夾。 包含不同資料類型的 AVRO 檔案預設路徑為：
- 訊息：**{container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- 裝置：**{container}/devices/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- 裝置範本：**{container}/deviceTemplates/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**

## <a name="how-to-read-exported-avro-files"></a>如何讀取匯出的 AVRO 檔案

AVRO 是二進位格式，因此無法以原始狀態讀取檔案。 其可解碼為 JSON 格式。 下列範例說明如何使用上述範例來剖析量測、裝置和裝置範本 AVRO 檔案。

## <a name="python"></a>Python

### <a name="install-pandas-and-the-pandaavro-package"></a>安裝 Pandas 和 PandaAvro 套件：

```python
pip install pandas
pip install pandavro
```
### <a name="parse-measurements-avro-file"></a>剖析量測 AVRO 檔案

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    measurements = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary with the device id
    # located under the "connectionDeviceId" key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of utf-8 bytes that is stringified and parsed
    # as json. In this example, we pull the "humidity" property off of each one
    # to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, we print the new DataFrame with our device ids and humidities
    print(transformed)

```
### <a name="parse-devices-avro-file"></a>剖析裝置 AVRO 檔案

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    devices = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device id is available directly in the "id" column
    transformed["device_id"] = devices["id"]

    # The template id and version are present in a dictionary under the
    # deviceTemplate column
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)

```

### <a name="parse-device-templates-avro-file"></a>剖析裝置範本 AVRO 檔案

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    templates = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available directly in the "id" and "version"
    # columns, respectively
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)
```

## <a name="c"></a>C#

### <a name="install-microsofthadoopavro"></a>安裝 Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

### <a name="parse-measurements-avro-file"></a>剖析量測 AVRO 檔案

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
            // For one Avro Container, it may contain multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the fields
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

### <a name="parse-devices-avro-file"></a>剖析裝置 AVRO 檔案

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var deviceId = record.GetField<string>("id");

                    // The device template information is stored in a sub-record
                    // under the "deviceTemplate" field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
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
### <a name="parse-device-templates-avro-file"></a>剖析裝置範本 AVRO 檔案

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
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

## <a name="javascript"></a>Javascript

### <a name="install-avsc"></a>安裝 avsc

```javascript
npm install avsc
```

### <a name="parse-measurements-avro-file"></a>剖析量測 AVRO 檔案

```javascript
const avro = require('avsc');

// Read the avro file and parse the device id and humidity from each record
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the system properties
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the Body from a Buffer to a string and parse it
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property off of the body
        const humidity = body.humidity;

        // Log the device id and humidity we found
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-devices-avro-file"></a>剖析裝置 AVRO 檔案

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the id property
        const deviceId = record.id;

        // Fetch the tempalte id and version from the deviceTemplate property
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`ID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-device-templates-avro-file"></a>剖析裝置範本 AVRO 檔案

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template id and version from the id and verison properties
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>後續步驟

現在您已了解如何匯出資料，以下是建議執行的下一個步驟：

> [!div class="nextstepaction"]
> [如何在 Power BI 中將資料視覺化](howto-connect-powerbi.md)