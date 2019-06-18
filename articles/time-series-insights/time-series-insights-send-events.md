---
title: 將事件傳送至 Azure 時間序列深入解析環境 | Microsoft Docs
description: 了解如何設定事件中樞，並執行範例應用程式推送事件，以在 Azure 時間序列深入解析中檢視。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: ae59e8115ca2d1ba69c8a3a099216eb3d98e2658
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237701"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>使用事件中樞將事件傳送至時間序列深入解析環境

這篇文章說明如何建立和設定 Azure 事件中樞中的事件中樞。 它也會說明如何執行範例應用程式，以推送事件至 Azure Time Series Insights，從事件中樞。 如果您有現有事件中樞與事件，以 JSON 格式，請略過本教學課程中，並檢視您的環境中[Azure Time Series Insights](./time-series-insights-update-create-environment.md)。

## <a name="configure-an-event-hub"></a>設定事件中樞

1. 若要了解如何建立事件中樞，請參閱[事件中樞文件](https://docs.microsoft.com/azure/event-hubs/)。
1. 在搜尋方塊中搜尋**事件中樞**。 在傳回的清單中選取 [事件中樞]  。
1. 選取事件中樞。
1. 當您建立事件中樞時，您要建立事件中樞命名空間。 如果您尚未建立事件中樞命名空間中的功能表上，底下**實體**，建立事件中樞。  

    [![事件中樞的清單](media/send-events/updated.png)](media/send-events/updated.png#lightbox)

1. 建立事件中樞之後，在事件中樞清單中選取它。
1. 在功能表底下**實體**，選取**事件中樞**。
1. 選取事件中樞的名稱來對它進行設定。
1. 在 [實體]  下，選取 [取用者群組]  ，然後選取 [取用者群組]  。

    [![建立取用者群組](media/send-events/consumer-group.png)](media/send-events/consumer-group.png#lightbox)

1. 請確定您建立專供 Time Series Insights 事件來源的取用者群組。

    > [!IMPORTANT]
    > 請確定不會由其他服務，例如 Azure Stream Analytics 作業或其他 Time Series Insights 環境中使用此取用者群組。 如果有其他服務使用此取用者群組，讀取作業對此環境和其他服務都會造成負面影響。 如果使用 **$Default** 做為取用者群組，有可能會導致其他讀取者重複使用您的取用者群組。

1. 在功能表中，在**設定**，選取**共用存取原則**，然後選取**新增**。

    [![選取 [共用存取原則，，然後選取 [新增] 按鈕](media/send-events/shared-access-policy.png)](media/send-events/shared-access-policy.png#lightbox)

1. 在 [新增共用存取原則]  窗格中，建立名為 **MySendPolicy** 的共用存取。 您可以使用此共用的存取原則中傳送事件C#本文稍後的範例。

    [![在 [原則名稱] 方塊中，輸入 [MySendPolicy](media/send-events/shared-access-policy-2.png)](media/send-events/shared-access-policy-2.png#lightbox)

1. 底下**宣告**，選取**傳送**核取方塊。

## <a name="add-a-time-series-insights-instance"></a>新增時間序列深入解析執行個體

時間序列深入解析更新會使用執行個體，將內容資料新增至傳入的遙測資料。 資料會在查詢期間透過使用 [時間序列識別碼]  .加入。 **時間序列識別碼**我們稍後在本文中所使用的專案是針對範例 windmills `id`。 若要深入了解時間序列深入解析和**時間序列識別碼**，請參閱[時間序列模型](./time-series-insights-update-tsm.md)。

### <a name="create-a-time-series-insights-event-source"></a>建立時間序列深入解析事件來源

1. 如果您尚未建立事件來源，請完成這些步驟以[建立事件來源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)。

1. 設定 `timeSeriesId` 的值。 若要深入了解**時間序列識別碼**，請參閱[時間序列模型](./time-series-insights-update-tsm.md)。

### <a name="push-events"></a>推送事件 (風車範例)

1. 在搜尋列中搜尋**事件中樞**。 在傳回的清單中選取 [事件中樞]  。

1. 選取事件中樞。

1. 依序移至 [共用存取原則]   > [RootManageSharedAccessKey]  。 複製的值**連接字串-主索引鍵**。

    [![複製主索引鍵連接字串的值](media/send-events/sample-code-connection-string.png)](media/send-events/sample-code-connection-string.png#lightbox)

1. 移至 https://tsiclientsample.azurewebsites.net/windFarmGen.html 。 此 URL 會執行模擬風車裝置。
1. 在網頁的 [事件中樞連接字串]  方塊中，貼上您在[推送事件](#push-events)中複製的連接字串。
  
    [![在 [事件中樞連接字串] 方塊中貼上的主索引鍵連接字串](media/send-events/updated_two.png)](media/send-events/updated_two.png#lightbox)

1. 選取 [按一下即可啟動]  。 模擬器會產生您可以直接使用的執行個體 JSON。

1. 回到 Azure 入口網站中的事件中樞。 在 [**概觀**] 頁面上，您會看到新的事件所接收的事件中樞。

    [![顯示度量的事件中樞的事件中樞概觀頁面](media/send-events/telemetry.png)](media/send-events/telemetry.png#lightbox)

## <a name="json"></a>支援的 JSON 圖形

### <a name="example-one"></a>其中一個範例

* **輸入**：簡單的 JSON 物件。

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **輸出**：一個事件。

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>範例二

* **輸入**：具有兩個 JSON 物件的 JSON 陣列。 每個 JSON 物件都會轉換成事件。

    ```JSON
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

* **輸出**：兩個事件。

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>範例三

* **輸入**：具有巢狀 JSON 陣列的 JSON 物件，此陣列中包含兩個 JSON 物件。

    ```JSON
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

* **輸出**：兩個事件。 系統會將屬性**位置**複製到每個事件。

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>範例 4

* **輸入**：具有巢狀 JSON 陣列的 JSON 物件，此陣列中包含兩個 JSON 物件。 此輸入示範可由複雜 JSON 物件表示的全域屬性。

    ```JSON
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

* **輸出**：兩個事件。

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>後續步驟

- [檢視您的環境](https://insights.timeseries.azure.com)在時間序列深入解析總管 中。
