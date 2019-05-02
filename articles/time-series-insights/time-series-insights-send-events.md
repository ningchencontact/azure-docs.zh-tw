---
title: 將事件傳送至 Azure 時間序列深入解析環境 | Microsoft Docs
description: 了解如何設定事件中樞，並執行範例應用程式推送事件，以在 Azure 時間序列深入解析中檢視。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 55b19a6cf71730858fcf42880f71a2c9c07a3b31
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683970"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>使用事件中樞將事件傳送至時間序列深入解析環境

本文說明如何在 Azure 事件中樞中建立和設定事件中樞，然後執行範例應用程式以推送事件。 如果您有內含 JSON 格式事件的現有事件中樞，請跳過本教學課程，並在[時間序列深入解析](./time-series-insights-update-create-environment.md)中檢視您的環境。

## <a name="configure-an-event-hub"></a>設定事件中樞

1. 若要了解如何建立事件中樞，請參閱[事件中樞文件](https://docs.microsoft.com/azure/event-hubs/)。
1. 在搜尋方塊中搜尋**事件中樞**。 在傳回的清單中選取 [事件中樞]。
1. 選取事件中樞。
1. 建立事件中樞時，實際是建立事件中樞命名空間。 如果您尚未在命名空間內建立事件中樞，請在功能表中的 [實體] 下建立事件中樞。  

    ![事件中樞清單][1]

1. 建立事件中樞之後，在事件中樞清單中選取它。
1. 在功能表中的 [實體] 下，選取 [事件中樞]。
1. 選取事件中樞的名稱來對它進行設定。
1. 在 [實體] 下，選取 [取用者群組]，然後選取 [取用者群組]。

    ![建立取用者群組][2]

1. 確定您已建立專供 Time Series Insights 事件來源使用的取用者群組。

    > [!IMPORTANT]
    > 確定任何其他服務 (例如 Azure 串流分析作業或其他 Time Series Insights 環境) 均未使用此取用者群組。 如果有其他服務使用此取用者群組，讀取作業對此環境和其他服務都會造成負面影響。 如果使用 **$Default** 做為取用者群組，有可能會導致其他讀取者重複使用您的取用者群組。

1. 在功能表中的 [設定] 下選取 [共用存取原則]，然後選取 [新增]。

    ![選取 [共用存取原則]，然後選取 [新增] 按鈕][3]

1. 在 [新增共用存取原則] 窗格中，建立名為 **MySendPolicy** 的共用存取。 您將會在本文章稍後的 C# 範例中，使用此共用存取原則傳送事件。

    ![在 [原則名稱] 方塊中，輸入 MySendPolicy][4]

1. 在 [宣告] 下選取 [傳送] 核取方塊。

## <a name="add-a-time-series-insights-instance"></a>新增時間序列深入解析執行個體

時間序列深入解析更新會使用執行個體，將內容資料新增至傳入的遙測資料。 資料會在查詢期間透過使用 [時間序列識別碼].加入。 我們稍後在本文章中所使用範例風車專案的**時間序列識別碼** 為 **Id**。若要深入了解時間序列深入解析和**時間序列識別碼**，請參閱[時間序列模型](./time-series-insights-update-tsm.md)。

### <a name="create-a-time-series-insights-event-source"></a>建立時間序列深入解析事件來源

1. 如果您尚未建立事件來源，請完成這些步驟以[建立事件來源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)。

1. 設定 `timeSeriesId` 的值。 若要深入了解**時間序列識別碼**，請參閱[時間序列模型](./time-series-insights-update-tsm.md)。

### <a name="push-events"></a>推送事件 (風車範例)

1. 在搜尋列中搜尋**事件中樞**。 在傳回的清單中選取 [事件中樞]。

1. 選取事件中樞。

1. 依序移至 [共用存取原則] > [RootManageSharedAccessKey]。 複製 [連接字串 - 主索引鍵] 的值。

    ![複製主索引鍵連接字串的值][5]

1. 移至 https://tsiclientsample.azurewebsites.net/windFarmGen.html。 此 URL 會執行模擬風車裝置。
1. 在網頁的 [事件中樞連接字串] 方塊中，貼上您在[推送事件](#push-events)中複製的連接字串。
  
    ![將主索引鍵連接字串貼到 [事件中樞連接字串] 方塊中][6]

1. 選取 [按一下即可啟動]。 模擬器會產生您可以直接使用的執行個體 JSON。

1. 回到 Azure 入口網站中的事件中樞。 您現在應該會在 [概觀] 頁面上看到事件中樞正在接收的新事件：

    ![顯示事件中樞計量的事件中樞概觀頁面][7]

<a id="json"></a>

## <a name="supported-json-shapes"></a>支援的 JSON 樣貌

### <a name="sample-1"></a>範例 1

#### <a name="input"></a>輸入

簡單的 JSON 物件：

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```

#### <a name="output-one-event"></a>輸出：一個事件

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

#### <a name="output-two-events"></a>輸出：兩個事件

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

#### <a name="output-two-events"></a>輸出：兩個事件

系統會將屬性**位置**複製到每個事件。

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

#### <a name="output-two-events"></a>輸出：兩個事件

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>後續步驟

- [檢視您的環境](https://insights.timeseries.azure.com)在時間序列深入解析總管 中。

<!-- Images -->
[1]: media/send-events/updated.png
[2]: media/send-events/consumer-group.png
[3]: media/send-events/shared-access-policy.png
[4]: media/send-events/shared-access-policy-2.png
[5]: media/send-events/sample-code-connection-string.png
[6]: media/send-events/updated_two.png
[7]: media/send-events/telemetry.png
