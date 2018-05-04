---
title: 如何將事件傳送至 Azure 時間序列深入解析環境 | Microsoft Docs
description: 本教學課程說明如何建立和設定事件中樞，並執行應用程式範例以將事件推送至 Azure 時間序列深入解析來加以顯示。
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MarkMcGeeAtAquent
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 04/09/2018
ms.openlocfilehash: b418d1114cf6b906dcdee46bbf7e094cbc4a0521
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>使用事件中樞將事件傳送至 Time Series Insights 環境
本文說明如何建立和設定事件中樞，並執行應用程式範例以推送事件。 如果您現有的事件中樞內含 JSON 格式的事件，請跳過本教學課程，並在[時間序列深入解析](https://insights.timeseries.azure.com)檢視您的環境。

## <a name="configure-an-event-hub"></a>設定事件中樞
1. 若要建立事件中樞，請遵循事件中樞[文件](../event-hubs/event-hubs-create.md)的指示。

2. 在搜尋列中搜尋**事件中樞**。 在傳回的清單中按一下 [事件中樞]。

3. 按一下事件中樞的名稱來加以選取。

4. 在中間設定視窗的 [實體] 底下，再次按一下 [事件中樞]。

5. 選取事件中樞的名稱來對它進行設定。

  ![選取事件中樞取用者群組](media/send-events/consumer-group.png)

6. 在 [實體] 底下，選取 [取用者群組]。
 
7. 確定您已建立專供 Time Series Insights 事件來源使用的取用者群組。

   > [!IMPORTANT]
   > 確定任何其他服務 (例如串流分析作業或其他 Time Series Insights 環境) 均未使用此取用者群組。 如果有其他服務使用此取用者群組，讀取作業會對此環境和其他服務造成負面影響。 如果您使用 “$Default” 做為取用者群組，有可能會導致其他讀取者重複使用。

8. 在 [設定] 標題底下，選取 [共用存取原則]。

9. 在事件中樞內建立 **MySendPolicy**，以用來在 Csharp 範例中傳送事件。

  ![選取 [共用存取原則]，然後按一下 [新增] 按鈕](media/send-events/shared-access-policy.png)  

  ![新增共用存取原則](media/send-events/shared-access-policy-2.png)  

## <a name="add-time-series-insights-reference-data-set"></a>新增時間序列深入解析參考資料集 
在 TSI 中使用參考資料與您的遙測資料內容關聯。  該內容會將意義新增到您的資料，讓您更輕鬆地篩選及彙總。  TSI 會在輸入階段聯結參考資料，且無法回溯聯結此資料。  因此，在新增具有資料的事件來源之前，請務必新增參考資料。  諸如位置或感應器類型等資料是有用的維度，您可以將其聯結至裝置/標記/感應器識別碼，以更輕鬆地配量及篩選。  

> [!IMPORTANT]
> 當您上傳歷史資料時，務必要設定參考資料集。

在將歷史資料大量上傳至 TSI 時，請確定您已備妥參考資料。  請記住，如果該事件來源包含資料，則 TSI 會立即從已聯結的事件來源開始讀取。  等到您備妥參考資料之後才將事件來源聯結至 TSI 會很有幫助，特別是在該事件來源中有資料時。 或者，您也可以等到參考資料集備妥之後，才將資料推送至該事件來源。

若要管理參考資料，在 TSI 總管中有 Web 式使用者介面，且有程式設計 C# API。 TSI 總管具有視覺化使用者體驗，可透過 JSON 或 CSV 格式上傳檔案或貼上現有的參考資料集。 您可以透過 API，視需要建置自訂應用程式。

如需有關如何在時間序列深入解析中管理參考資料的詳細資訊，請參閱[參考資料文章](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set)。

## <a name="create-time-series-insights-event-source"></a>建立 Time Series Insights 事件來源
1. 如果您尚未建立事件來源，請遵循[這些指示](time-series-insights-how-to-add-an-event-source-eventhub.md)來建立事件來源。

2. 指定 **deviceTimestamp** 做為時間戳記屬性名稱，這個屬性會做為 C# 範例中的實際時間戳記。 以 JSON 形式將時間戳記屬性傳送至事件中樞時，該屬性的名稱區分大小寫，且值的格式必須是 __yyyy-MM-ddTHH:mm:ss.FFFFFFFK__。 如果事件中不存在該屬性，則系統會使用加入事中樞佇列的時間。

  ![建立事件來源](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>用來推送事件的範例程式碼
1. 移至名為 **MySendPolicy** 的事件中樞原則。 複製**連接字串**與原則金鑰。

  ![複製 MySendPolicy 連接字串](media/send-events/sample-code-connection-string.png)

2. 執行下列程式碼，讓三個裝置各傳送 600 個事件。 使用連接字串更新 `eventHubConnectionString`。

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.Rdx.DataGenerator
{
    internal class Program
    {
        private static void Main(string[] args)
        {
            var from = new DateTime(2017, 4, 20, 15, 0, 0, DateTimeKind.Utc);
            Random r = new Random();
            const int numberOfEvents = 600;

            var deviceIds = new[] { "device1", "device2", "device3" };

            var events = new List<string>();
            for (int i = 0; i < numberOfEvents; ++i)
            {
                for (int device = 0; device < deviceIds.Length; ++device)
                {
                    // Generate event and serialize as JSON object:
                    // { "deviceTimestamp": "utc timestamp", "deviceId": "guid", "value": 123.456 }
                    events.Add(
                        String.Format(
                            CultureInfo.InvariantCulture,
                            @"{{ ""deviceTimestamp"": ""{0}"", ""deviceId"": ""{1}"", ""value"": {2} }}",
                            (from + TimeSpan.FromSeconds(i * 30)).ToString("o"),
                            deviceIds[device],
                            r.NextDouble()));
                }
            }

            // Create event hub connection.
            var eventHubConnectionString = @"Endpoint=sb://...";
            var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubConnectionString);

            using (var ms = new MemoryStream())
            using (var sw = new StreamWriter(ms))
            {
                // Wrap events into JSON array:
                sw.Write("[");
                for (int i = 0; i < events.Count; ++i)
                {
                    if (i > 0)
                    {
                        sw.Write(',');
                    }
                    sw.Write(events[i]);
                }
                sw.Write("]");

                sw.Flush();
                ms.Position = 0;

                // Send JSON to event hub.
                EventData eventData = new EventData(ms);
                eventHubClient.Send(eventData);
            }
        }
    }
}

```
## <a name="supported-json-shapes"></a>支援的 JSON 樣貌
### <a name="sample-1"></a>範例 1

#### <a name="input"></a>輸入

簡單的 JSON 物件。

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```
#### <a name="output---one-event"></a>輸出 - 一個事件

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>範例 2

#### <a name="input"></a>輸入
具有兩個 JSON 物件的 JSON 陣列。 每個 JSON 物件都會轉換成事件。
```json
[
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    },
    {
        "id":"device2",
        "timestamp":"2016-01-17T01:17:00Z"
    }
]
```
#### <a name="output---two-events"></a>輸出 - 兩個事件

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>範例 3

#### <a name="input"></a>輸入

具有巢狀 JSON 陣列的 JSON 物件，此陣列中包含兩個 JSON 物件：
```json
{
    "location":"WestUs",
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
}

```
#### <a name="output---two-events"></a>輸出 - 兩個事件
請注意，系統會將 "location" 屬性複製到每個事件。

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>範例 4

#### <a name="input"></a>輸入

具有巢狀 JSON 陣列的 JSON 物件，此陣列中包含兩個 JSON 物件。 此輸入示範可由複雜 JSON 物件表示的全域屬性。

```json
{
    "location":"WestUs",
    "manufacturer":{
        "name":"manufacturer1",
        "location":"EastUs"
    },
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z",
            "data":{
                "type":"pressure",
                "units":"psi",
                "value":108.09
            }
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z",
            "data":{
                "type":"vibration",
                "units":"abs G",
                "value":217.09
            }
        }
    ]
}
```
#### <a name="output---two-events"></a>輸出 - 兩個事件

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

### <a name="json-shaping-strategies"></a>JSON 成形策略
讓我們使用下列事件範例作為起點，然後討論與其相關的問題，以及減少這些問題的策略。

#### <a name="payload-1"></a>承載 1：
```json
[{
            "messageId": "LINE_DATA",
            "deviceId": "FXXX",
            "timestamp": 1522355650620,
            "series": [{
                        "chId": 3,
                        "value": -3750.0
                  }, {
                        "chId": 13,
                        "value": 0.58015072345733643
                  }, {
                        "chId": 11,
                        "value": 800.0
                  }, {
                        "chId": 21,
                        "value": 0.0
                  }, {
                        "chId": 14,
                        "value": -999.0
                  }, {
                        "chId": 37,
                        "value": 2.445906400680542
                  }, {
                        "chId": 39,
                        "value": 0.0
                  }, {
                        "chId": 40,
                        "value": 1.0
                  }, {
                        "chId": 1,
                        "value": 1.0172575712203979
                  }
            ],
            "EventProcessedUtcTime": "2018-03-29T20:36:21.3245900Z",
            "PartitionId": 2,
            "EventEnqueuedUtcTime": "2018-03-29T20:34:11.0830000Z",
            "IoTHub": {
                  "MessageId": "<17xxx2xx-36x0-4875-9x1x-x428x41x1x68>",
                  "CorrelationId": "<x253x5xx-7xxx-4xx3-91x4-xxx3bx2xx0x3>",
                  "ConnectionDeviceId": "AAAA-ZZ-001",
                  "ConnectionDeviceGenerationId": "<123456789012345678>",
                  "EnqueuedTime": "2018-03-29T20:34:10.7990000Z",
                  "StreamId": null
            }
      }
]
 ```

如果您將此事件陣列以承載的形式推送至 TSI，它會依每一個量值儲存為一個事件。 如此一來，可能會建立過多的事件，而可能不盡理想。 請注意，您可以在 TSI 中使用參考資料，將有意義的名稱新增為屬性。  例如，您可以建立重要屬性為 chId 的參考資料集：  

chId  Measure               Unit 24    Engine Oil Pressure   PSI 25    CALC Pump Rate        bbl/min

如需有關如何在時間序列深入解析中管理參考資料的詳細資訊，請參閱[參考資料文章](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set)。

第一個承載的另一個問題是該時間戳記是以毫秒為單位。 TSI 只接受 ISO 格式的時間戳記。 其中一種解決方案是保留 TSI 中預設的時間戳記行為，也就是使用加入佇列的時間戳記。

作為上述承載的替代方案，讓我們看看另一個範例。  

#### <a name="payload-2"></a>承載 2：
```json
{
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "STATE Engine State": 1,
      "unit": "NONE"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "MPC_AAAA-ZZ-001",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Well Head Px 1": -494162.8515625,
      "unit": "psi"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate": 0,
      "unit": "bbl/min"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Engine Fuel Pressure": 0,
      "unit": "psi"
}, {
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "Engine Oil Pressure": 0.58015072345733643,
      "unit": "psi"
}
```

如同承載 1，TSI 會將每一個量測的值儲存為唯一的事件。  值得注意的差異是，TSI 將會在此正確地讀取如 ISO 的「時間戳記」。  

如果您需要減少所傳送的事件數目，您可以傳送如下所示的資訊。  

#### <a name="payload-3"></a>承載 3：
```json
{
      "line": "Line01",
      "station": "Station 11",
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate": 0,
      "CALC Pump Rate.unit": "bbl/min"
      "Engine Oil Pressure": 0.58015072345733643,
      "Engine Oil Pressure.unit": "psi"
      "Engine Fuel Pressure": 0,
      "Engine Fuel Pressure.unit": "psi"
}
```
以下是最後一個建議。

#### <a name="payload-4"></a>承載 4：
```json
{
              "line": "Line01",
              "station": "Station 11",
              "gatewayid": "AAAA-ZZ-001",
              "deviceid": "F12XX",
              "timestamp": "2018-03-29T20:34:15.0000000Z",
              "CALC Pump Rate": {
                           "value": 0,
                           "unit": "bbl/min"
              },
              "Engine Oil Pressure": {
                           "value": 0.58015072345733643,
                           "unit": "psi"
              },
              "Engine Fuel Pressure": {
                           "value": 0,
                           "unit": "psi"
              }
}
```

此範例會顯示將 JSON 壓平合併後的輸出：

```json
{
      "line": "Line01",
      "station": "Station 11",,
      "gatewayid": "AAAA-ZZ-001",
      "deviceid": "F12XX",
      "timestamp": "2018-03-29T20:34:15.0000000Z",
      "CALC Pump Rate.value": 0,
      "CALC Pump Rate.unit": "bbl/min"
      "Engine Oil Pressure.value": 0.58015072345733643,
      "Engine Oil Pressure.unit": "psi"
      "Engine Fuel Pressure.value": 0,
      "Engine Fuel Pressure.unit": "psi"
}
```

您可以自由地在每個通道本身的 Json 物件內為通道定義不同的屬性，同時保持低事件計數。 這個壓平合併的方法會佔用更多空間，請務必要考慮到這一點。 TSI 容量是以事件和大小為基礎，端視何者先達到。

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> 在[時間序列深入解析總管](https://insights.timeseries.azure.com)中檢視您的環境。
