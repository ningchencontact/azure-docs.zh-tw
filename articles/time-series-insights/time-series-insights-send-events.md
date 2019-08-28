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
ms.date: 08/26/2019
ms.custom: seodec18
ms.openlocfilehash: 84eb0e230875b999218b67d47a66a3c92b494253
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072753"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>使用事件中樞將事件傳送至時間序列深入解析環境

本文說明如何在 Azure 事件中樞中建立和設定事件中樞。 它也會說明如何執行範例應用程式, 以將事件推送至事件中樞的 Azure 時間序列深入解析。 如果您現有的事件中樞具有 JSON 格式的事件, 請略過此教學課程, 並在[Azure 時間序列深入解析](./time-series-insights-update-create-environment.md)中查看您的環境。

## <a name="configure-an-event-hub"></a>設定事件中樞

1. 若要了解如何建立事件中樞，請參閱[事件中樞文件](https://docs.microsoft.com/azure/event-hubs/)。
1. 在搜尋方塊中搜尋**事件中樞**。 在傳回的清單中選取 [事件中樞]。
1. 選取事件中樞。
1. 當您建立事件中樞時, 您正在建立事件中樞命名空間。 如果您尚未在命名空間內建立事件中樞, 請在功能表的 [**實體**] 底下, 建立事件中樞。  

    [![事件中樞清單](media/send-events/updated.png)](media/send-events/updated.png#lightbox)

1. 建立事件中樞之後，在事件中樞清單中選取它。
1. 在功能表的 [**實體**] 底下, 選取 [**事件中樞**]。
1. 選取事件中樞的名稱來對它進行設定。
1. 在 **[總覽**] 底下, 選取 [取用**者群組**], 然後選取 [取用**者群組**]。

    [![建立取用者群組](media/send-events/consumer-group.png)](media/send-events/consumer-group.png#lightbox)

1. 請確定您建立的取用者群組僅供您的時間序列深入解析事件來源使用。

    > [!IMPORTANT]
    > 請確定此取用者群組未由任何其他服務使用, 例如 Azure 串流分析作業或其他時間序列深入解析環境。 如果有其他服務使用此取用者群組，讀取作業對此環境和其他服務都會造成負面影響。 如果使用 **$Default** 做為取用者群組，有可能會導致其他讀取者重複使用您的取用者群組。

1. 在功能表的 [**設定**] 底下, 選取 [**共用存取原則**], 然後選取 [**新增**]。

    [![選取 [共用存取原則], 然後選取 [新增] 按鈕](media/send-events/shared-access-policy.png)](media/send-events/shared-access-policy.png#lightbox)

1. 在 [新增共用存取原則] 窗格中，建立名為 **MySendPolicy** 的共用存取。 您可以使用此共用存取原則, 在本文稍後C#的範例中傳送事件。

    [![在 [原則名稱] 方塊中, 輸入 MySendPolicy](media/send-events/shared-access-policy-2.png)](media/send-events/shared-access-policy-2.png#lightbox)

1. 在[宣告] 底下, 選取 [**傳送**] 核取方塊。

## <a name="add-a-time-series-insights-instance"></a>新增時間序列深入解析執行個體

時間序列深入解析更新會使用執行個體，將內容資料新增至傳入的遙測資料。 資料會在查詢期間透過使用 [時間序列識別碼].加入。 我們稍後在本文中使用之範例風車專案的**時間序列識別碼**為`id`。 若要深入了解時間序列深入解析和**時間序列識別碼**，請參閱[時間序列模型](./time-series-insights-update-tsm.md)。

### <a name="create-a-time-series-insights-event-source"></a>建立時間序列深入解析事件來源

1. 如果您尚未建立事件來源，請完成這些步驟以[建立事件來源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)。

1. 設定 `timeSeriesId` 的值。 若要深入了解**時間序列識別碼**，請參閱[時間序列模型](./time-series-insights-update-tsm.md)。

### <a name="push-events"></a>推送事件 (風車範例)

1. 在搜尋列中搜尋**事件中樞**。 在傳回的清單中選取 [事件中樞]。

1. 選取您的事件中樞實例。

1. 移至 [**共用存取原則** > ]**MySendPolicy**。 複製 [**連接字串-主要金鑰**] 的值。

    [![複製主要金鑰連接字串的值](media/send-events/sample-code-connection-string.png)](media/send-events/sample-code-connection-string.png#lightbox)

1. 移至 https://tsiclientsample.azurewebsites.net/windFarmGen.html 。 此 URL 會執行模擬風車裝置。
1. 在網頁的 [事件中樞連接字串] 方塊中，貼上您在[推送事件](#push-events)中複製的連接字串。
  
    [![在 [事件中樞連接字串] 方塊中貼上主要金鑰連接字串](media/send-events/updated_two.png)](media/send-events/updated_two.png#lightbox)

1. 選取 [按一下即可啟動]。 模擬器會產生您可以直接使用的執行個體 JSON。

1. 回到 Azure 入口網站中的事件中樞。 在 [**總覽**] 頁面上, 您會看到事件中樞所接收的新事件。

    [![顯示事件中樞計量的事件中樞 [總覽] 頁面](media/send-events/telemetry.png)](media/send-events/telemetry.png#lightbox)

## <a name="json"></a>支援的 JSON 圖形

### <a name="example-one"></a>範例一

* **輸入**：簡單的 JSON 物件。

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **輸出**：一個事件。

    |ID|timestamp|
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

    |ID|timestamp|
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

    |位置|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>範例四

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

    |位置|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>後續步驟

- 在 [時間序列深入解析 explorer] 中[查看您的環境](https://insights.timeseries.azure.com)。
