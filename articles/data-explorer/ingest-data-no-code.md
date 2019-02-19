---
title: 教學課程：在 Azure 資料總管中內嵌診斷和活動記錄資料，而不需任何一行程式碼
description: 在本教學課程中，您將了解如何將資料內嵌至 Azure 資料總管 (不需任何一行程式碼)，然後查詢該資料。
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 2/5/2019
ms.openlocfilehash: 39019c4b11d055aa8f550928bd677e4ce33d6252
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885217"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>教學課程：在 Azure 資料總管中內嵌資料，而不需任何一行程式碼

本教學課程將教導您如何將診斷和活動記錄資料內嵌至 Azure 資料總管叢集，而不需任何一行程式碼。 此簡單擷取方法可讓您快速開始查詢 Azure 資料總管，以便進行資料分析。

在本教學課程中，您將了解如何：
> [!div class="checklist"]
> * 在 Azure 資料總管資料庫中建立資料表和擷取對應。
> * 使用更新原則將內嵌的資料格式化。
> * 建立[事件中樞](/azure/event-hubs/event-hubs-about)並將它連線至 Azure 資料總管。
> * 將資料從 [Azure 監視器診斷記錄](/azure/azure-monitor/platform/diagnostic-logs-overview)和 [Azure 監視器活動記錄](/azure/azure-monitor/platform/activity-logs-overview)串流至事件中樞。
> * 使用 Azure 資料總管查詢內嵌的資料。

> [!NOTE]
> 在相同的 Azure 位置/區域中建立所有資源。 這是 Azure 監視器診斷記錄的需求。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* [Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)。 在本教學課程中，資料庫名稱為 *AzureMonitoring*。

## <a name="azure-monitoring-data-provider---diagnostic-and-activity-logs"></a>Azure 監視資料提供者 - 診斷和活動記錄

檢視並了解 Azure 監視診斷和活動記錄所提供的資料。 我們將根據這些資料結構描述建立擷取管線。

### <a name="diagnostic-logs-example"></a>診斷記錄範例

Azure 診斷記錄是由 Azure 服務發出的計量，以提供關於該服務作業的資料。 資料會以 1 分鐘的時間粒紋進行彙總。 每個診斷記錄事件都包含一筆記錄。 以下是 Azure 資料總管計量事件結構描述的範例 (查詢持續時間)：

```json
{
    "count": 14,
    "total": 0,
    "minimum": 0,
    "maximum": 0,
    "average": 0,
    "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
    "time": "2018-12-20T17:00:00.0000000Z",
    "metricName": "QueryDuration",
    "timeGrain": "PT1M"
}
```

### <a name="activity-logs-example"></a>活動記錄範例

Azure 活動記錄是訂用帳戶層級記錄，其中包含記錄的集合。 此記錄可讓您深入了解對訂用帳戶中的資源所執行的作業。 不同於診斷記錄，活動記錄事件有記錄陣列。 我們稍後必須在本教學課程中分割此記錄陣列。 以下是用於檢查存取的活動記錄事件範例：

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```

## <a name="set-up-ingestion-pipeline-in-azure-data-explorer"></a>在 Azure 資料總管中設定擷取管線 

Azure 資料總管管線設定包含各種步驟，其中包括[資料表建立和資料擷取](/azure/data-explorer/ingest-sample-data#ingest-data)。 您也可以操作、對應和更新資料。

### <a name="connect-to-azure-data-explorer-web-ui"></a>連線至 Azure 資料總管 Web UI

1. 在 Azure 資料總管的 *AzureMonitoring* 資料庫中，選取 [查詢]，這會開啟 Azure 資料總管 Web UI。

    ![查詢](media/ingest-data-no-code/query-database.png)

### <a name="create-target-tables"></a>建立目標資料表

使用 Azure 資料總管 Web UI，在 Azure 資料總管資料庫中建立目標資料表。

#### <a name="diagnostic-logs-table"></a>診斷記錄資料表

1. 使用 `.create table` 控制命令，在 *AzureMonitoring* 資料庫中建立 *DiagnosticLogsRecords* 資料表，該資料表將會收到診斷記錄：

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. 選取 [執行] 以建立資料表。

    ![執行查詢](media/ingest-data-no-code/run-query.png)

#### <a name="activity-logs-tables"></a>活動記錄資料表

因為活動記錄結構不是表格式，所以您必須操作資料並將每個事件展開為一或多筆記錄。 未經處理的資料會內嵌至中繼資料表 *ActivityLogsRawRecords*。 屆時會操作並展開資料。 接著會使用更新原則，將展開的資料內嵌到 *ActivityLogsRecords* 資料表。 因此，您必須建立兩個不同的資料表，以便擷取活動記錄。

1. 在 *AzureMonitoring* 資料庫中建立 *ActivityLogsRecords* 資料表，該資料表將會收到活動記錄。 執行下列 Azure 資料總管查詢來建立資料表：

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. 在 *AzureMonitoring* 資料庫中建立 *ActivityLogsRawRecords* 中繼資料資料表，以便操作資料：

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>建立資料表對應

 資料格式為 `json`，因此需要資料對應。 `json` 對應會將每個 json 路徑對應至資料表資料行名稱。

#### <a name="diagnostic-logs-table-mapping"></a>診斷記錄資料表對應

使用下列查詢將資料對應至資料表：

```kusto
.create table DiagnosticLogsRecords ingestion json mapping 'DiagnosticLogsRecordsMapping' '[
{"column":"Timestamp","path":"$.time"},
{"column":"ResourceId","path":"$.resourceId"},
{"column":"MetricName","path":"$.metricName"},
{"column":"Count","path":"$.count"},
{"column":"Total","path":"$.total"},
{"column":"Minimum","path":"$.minimum"},
{"column":"Maximum","path":"$.maximum"},
{"column":"Average","path":"$.average"},
{"column":"TimeGrain","path":"$.timeGrain"}]'
```

#### <a name="activity-logs-table-mapping"></a>活動記錄資料表對應

使用下列查詢將資料對應至資料表：

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[
{"column":"Records","path":"$.records"}]'
```

### <a name="create-update-policy"></a>建立更新原則

1. 建立可展開記錄集合的[函式](/azure/kusto/management/functions)，讓集合中的每個值收到不同的資料列。 使用 [`mvexpand`](/azure/kusto/query/mvexpandoperator) 運算子：

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mvexpand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            OperationName = tostring(events["operationName"]),
            Category = tostring(events["category"]),
            ResultType = tostring(events["resultType"]),
            ResultSignature = tostring(events["resultSignature"]),
            DurationMs = toint(events["durationMs"]),
            IdentityAuthorization = events["identity.authorization"],
            IdentityClaims = events["identity.claims"],
            Location = tostring(events["location"]),
            Level = tostring(events["level"])
    }
    ```

2. 將[更新原則](/azure/kusto/concepts/updatepolicy)新增至目標資料表。 該原則會對 *ActivityLogsRawRecords* 中繼資料資料表中的任何新內嵌資料自動執行查詢，並將其結果內嵌到 *ActivityLogsRecords* 資料表中：

    ```kusto
    .alter table ActivityLogRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-event-hub-namespace"></a>建立事件中樞命名空間

Azure 診斷記錄能夠將計量匯出至儲存體帳戶或事件中樞。 在本教學課程中，我們會透過事件中樞來路由傳送計量。 您將在下列步驟中，建立診斷記錄的事件中樞命名空間和事件中樞。 Azure 監視會建立活動記錄的事件中樞 *insights-operational-logs*。

1. 在 Azure 入口網站中使用 Azure Resource Manager 範本建立事件中樞。 使用下列按鈕開始部署。 按一下滑鼠右鍵並選取 [在新視窗中開啟]，以便依照本文中的其餘步驟操作。 [部署至 Azure] 按鈕可將您帶往 Azure 入口網站。

    [![部署至 Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. 建立診斷記錄的事件中樞命名空間和事件中樞。

    ![事件中樞的建立](media/ingest-data-no-code/event-hub.png)

    在表單中填寫以下資訊。 對於下表中未列出的任何設定，請使用預設值。

    **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 訂用帳戶 | 您的訂用帳戶 | 選取您要用於事件中樞的 Azure 訂用帳戶。|
    | 資源群組 | *test-resource-group* | 建立新的資源群組。 |
    | 位置 | 選取最符合您需求的區域。 | 在與其他資源相同的位置建立事件中樞命名空間。
    | 命名空間名稱 | *AzureMonitoringData* | 選擇可識別您命名空間的唯一名稱。
    | 事件中樞名稱 | *DiagnosticLogsData* | 事件中樞位於命名空間之下，其會提供專屬的唯一範圍容器。 |
    | 取用者群組名稱 | *adxpipeline* | 建立取用者群組名稱。 讓多個取用應用程式各自擁有獨立的事件串流檢視。 |
    | | |

## <a name="connect-azure-monitoring-logs-to-event-hub"></a>將 Azure 監視記錄連線到事件中樞

### <a name="diagnostic-logs-connection-to-event-hub"></a>事件中樞的診斷記錄連線

選取要從中匯入計量的資源。 有數個資源類型能夠匯出診斷記錄，包括事件中樞命名空間、KeyVault、IoT 中樞和 Azure 資料總管叢集。 本教學課程中，我們會使用 Azure 資料總管叢集作為資源。

1. 在 Azure 入口網站中選取您的 Kusto 叢集。

    ![診斷設定](media/ingest-data-no-code/diagnostic-settings.png)

1. 從左側功能表中選取 [診斷設定]。
1. 按一下 [開啟診斷] 連結。 [診斷設定] 視窗隨即開啟。

    ![診斷設定視窗](media/ingest-data-no-code/diagnostic-settings-window.png)

1. 在 [診斷設定] 窗格中：
    1. 為您的診斷記錄資料命名：*ADXExportedData*
    1. 選取 [AllMetrics] 核取方塊 (選擇性)。
    1. 選取 [串流至事件中樞] 核取方塊。
    1. 按一下 [設定]

1. 在 [選取事件中樞] 窗格中，設定匯出至您所建立的事件中樞：
    1. 從下拉式清單中**選取事件中樞命名空間** *AzureMonitoringData*。
    1. 從下拉式清單中**選取事件中樞名稱** *diagnosticlogsdata*。
    1. 從下拉式清單中**選取事件中樞原則名稱**。
    1. 按一下 [確定]。

1. 按一下 [檔案] 。 事件中樞命名空間、名稱和原則名稱會出現在視窗中。

    ![儲存診斷設定](media/ingest-data-no-code/save-diagnostic-settings.png)

### <a name="activity-logs-connection-to-event-hub"></a>事件中樞的活對記錄連線

1. 在 Azure 入口網站的左側功能表中，選取 [活動記錄]
1. [活動記錄] 視窗隨即開啟。 **按一下 [匯出至事件中樞]**

    ![活動記錄檔](media/ingest-data-no-code/activity-log.png)

1. 在 [匯出活動記錄] 視窗中：
 
    ![匯出活動記錄](media/ingest-data-no-code/export-activity-log.png)

    1. 選取您的訂用帳戶。
    1. 在 [區域] 下拉式清單中，選擇 [全選]
    1. 選取 [匯出至事件中樞] 核取方塊。
    1. 按一下 [選取服務匯流排命名空間] 以開啟 [選取事件中樞] 窗格。
    1. 在 [選取事件中樞] 窗格中，從下拉式功能表中選取：您的訂用帳戶、您的事件命名空間 *AzureMonitoringData*，以及預設事件中樞原則名稱。
    1. 按一下 [確定]。
    1. 按一下視窗右上角的 [儲存]。 將會建立名稱為 *insights-operational-logs* 的事件中樞。

### <a name="see-data-flowing-to-your-event-hubs"></a>查看流向事件中樞的資料

1. 在定義連線，以及完成活動記錄匯出至事件中樞之前，等候幾分鐘。 移至您的事件中樞命名空間，查看您所建立的事件中樞。

    ![建立的事件中樞](media/ingest-data-no-code/event-hubs-created.png)

1. 查看流向事件中樞的資料：

    ![事件中樞資料](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-event-hub-to-azure-data-explorer"></a>將事件中樞連線至 Azure 資料總管

### <a name="diagnostic-logs-data-connection"></a>診斷記錄資料連線

1. 在您的 Azure 資料總管叢集 *kustodocs* 中，選取左側功能表中的 [資料庫]。
1. 在 [資料庫] 視窗中，選取您的資料庫名稱 *AzureMonitoring*
1. 在左側功能表中，選取 [資料擷取]
1. 在 [資料擷取] 視窗中，按一下 [+ 新增資料連線]
1. 在 [資料連線] 視窗中，輸入下列資訊：

    ![事件中樞連線](media/ingest-data-no-code/event-hub-data-connection.png)

    資料來源：

    **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 資料連線名稱 | *DiagnosticsLogsConnection* | 您想要在 Azure 資料總管中建立的連線名稱。|
    | 事件中樞命名空間 | *AzureMonitoringData* | 您先前選擇用來辨識命名空間的名稱。 |
    | 事件中樞 | *diagnosticlogsdata* | 您建立的事件中樞。 |
    | 取用者群組 | *adxpipeline* | 在您所建立事件中樞中定義的取用者群組。 |
    | | |

    目標資料表：

    路由有兩個選項：*靜態*和*動態*。 在本教學課程中，您將使用靜態路由 (預設值)，而指定資料表名稱、檔案格式和對應。 因此，將 [我的資料包含路由資訊] 保留為未選取。

     **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 資料表 | *DiagnosticLogsRecords* | 您在 *AzureMonitoring* 中建立的資料表。 |
    | 資料格式 | *JSON* | 資料表中的格式。 |
    | 資料行對應 | *DiagnosticLogsRecordsMapping* | 您在 *AzureMonitoring* 資料庫中建立的對應，會將傳入的 JSON 資料對應至 *DiagnosticLogsRecords* 的資料行名稱與資料類型。|
    | | |

1. 按一下 [建立]   

### <a name="activity-logs-data-connection"></a>活動記錄資料連線

重複執行[診斷記錄資料連線](#diagnostic-logs-data-connection)一節中的步驟，以建立活動記錄資料連線。

1. 在 [資料連線] 視窗中插入下列設定：

    資料來源：

    **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 資料連線名稱 | *ActivityLogsConnection* | 您想要在 Azure 資料總管中建立的連線名稱。|
    | 事件中樞命名空間 | *AzureMonitoringData* | 您先前選擇用來辨識命名空間的名稱。 |
    | 事件中樞 | *insights-operational-logs* | 您建立的事件中樞。 |
    | 取用者群組 | *$Default* | 預設取用者群組。 如有需要，您可以建立不同的取用者群組。 |
    | | |

    目標資料表：

    路由有兩個選項：*靜態*和*動態*。 在本教學課程中，您將使用靜態路由 (預設值)，而指定資料表名稱、檔案格式和對應。 因此，將 [我的資料包含路由資訊] 保留為未選取。

     **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 資料表 | *ActivityLogsRawRecords* | 您在 *AzureMonitoring* 中建立的資料表。 |
    | 資料格式 | *JSON* | 資料表中的格式。 |
    | 資料行對應 | *ActivityLogsRawRecordsMapping* | 您在 *AzureMonitoring* 資料庫中建立的對應，會將傳入的 JSON 資料對應至 *ActivityLogsRawRecords* 的資料行名稱與資料類型。|
    | | |

1. 按一下 [建立]   

## <a name="query-the-new-tables"></a>查詢新的資料表

您有具備資料流程的管線。 根據預設，透過叢集擷取需要 5 分鐘的時間，因此容許資料流程在幾分鐘後開始查詢。

### <a name="diagnostic-logs-table-query-example"></a>診斷記錄資料表查詢範例

下列查詢會分析 Azure 資料總管診斷記錄中的查詢持續時間資料：

```kusto
DiagnosticLogsRecords
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

查詢結果：

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

### <a name="activity-logs-table-query-example"></a>活動記錄資料表查詢範例

下列查詢會分析 Azure 資料總管活動記錄中的資料：

```kusto
ActivityLogsRecords
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

查詢結果：
|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768.333 |
| | |

## <a name="next-steps"></a>後續步驟

了解如何使用下列文件，對您從 Azure 資料總管擷取的資料撰寫更多查詢：

> [!div class="nextstepaction"]
> [撰寫 Azure 資料總管的查詢](write-queries.md)
