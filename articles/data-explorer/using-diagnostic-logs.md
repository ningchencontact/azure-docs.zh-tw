---
title: 使用診斷記錄來監視 Azure 資料總管內嵌作業
description: 瞭解如何設定 Azure 資料總管的診斷記錄來監視內嵌作業。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 7d0fec56791c0d3e7ae60d78da83cf286532b9ab
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/19/2019
ms.locfileid: "71124002"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>使用診斷記錄來監視 Azure 資料總管內嵌作業（預覽）

Azure 資料總管是快速、完全受控的資料分析服務，可即時分析來自應用程式、網站、IoT 裝置等的大量資料流。 若要使用 Azure 資料總管，請先建立叢集，然後在該叢集中建立一或多個資料庫。 然後將資料內嵌（載入）至資料庫中的資料表，讓您可以對其執行查詢。 [Azure 監視器診斷記錄](/azure/azure-monitor/platform/diagnostic-logs-overview)會提供有關 Azure 資源作業的資料。 Azure 資料總管會使用診斷記錄，以取得有關內嵌成功和失敗的見解。 您可以將作業記錄匯出至 Azure 儲存體、事件中樞或 Log Analytics，以監視內嵌狀態。 Azure 儲存體和 Azure 事件中樞的記錄可以路由至 Azure 資料總管叢集中的資料表，以進行進一步的分析。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請建立[免費的 azure 帳戶](https://azure.microsoft.com/free/)。
* 建立叢集[和資料庫](create-cluster-database-portal.md)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>設定 Azure 資料總管叢集的診斷記錄

診斷記錄可以用來設定下列記錄資料的收集：
* 成功的內嵌作業：這些記錄檔包含成功完成內嵌作業的相關資訊。
* 失敗的內嵌作業：這些記錄包含有關失敗的內嵌作業的詳細資訊，包括錯誤詳細資料。 

然後，資料會封存到儲存體帳戶、串流至事件中樞，或傳送至 Log Analytics （依據您的規格）。

### <a name="enable-diagnostic-logs"></a>啟用診斷記錄

診斷記錄預設為停用。 若要啟用診斷記錄，請執行下列步驟：

1. 在  [Azure 入口網站](https://portal.azure.com)中，選取您要監視的 Azure 資料總管叢集資源。
1. 在 [監視] 下方，選取 [診斷設定]。
  
    ![新增診斷記錄](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. 選取 [**新增診斷設定**]。
1. 在 [**診斷設定**] 視窗中：
 
    ![診斷設定](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. 選取診斷設定的 [**名稱**]。
    1. 選取一個或多個目標：儲存體帳戶、事件中樞或 Log Analytics。
    1. 選取要收集的記錄： `SucceededIngestion`或`FailedIngestion`。
    1. 選取要收集的[計量](using-metrics.md)（選擇性）。   
    1. 選取 [**儲存**] 以儲存新的診斷記錄設定和計量。
    1. 在 Azure 入口網站中建立**新的支援要求**，以要求啟用診斷記錄。

將會在幾分鐘內設定新設定。 然後，記錄會出現在設定的封存目標（儲存體帳戶、事件中樞或 Log Analytics）中。 

## <a name="diagnostic-logs-schema"></a>診斷記錄結構描述

所有[Azure 監視器診斷記錄都會共用通用的最上層架構](/azure/azure-monitor/platform/diagnostic-logs-schema)。 Azure 資料總管具有本身事件的唯一屬性。 所有記錄都是以 JSON 格式儲存。

### <a name="ingestion-logs-schema"></a>內嵌記錄架構

記錄 JSON 字串包括下表所列的元素：

|Name               |描述
|---                |---
|time               |報表的時間
|resourceId         |Azure Resource Manager 資源識別碼
|operationName      |作業的名稱：MICROSOFT.KUSTO/叢集/內嵌/動作 '
|operationVersion   |架構版本：'1.0' 
|category           |作業的類別。 `SucceededIngestion` 或 `FailedIngestion`。 [成功操作](#successful-ingestion-operation-log)或[失敗](#failed-ingestion-operation-log)作業的屬性會有所不同。
|properties         |作業的詳細資訊。

#### <a name="successful-ingestion-operation-log"></a>成功的內嵌操作記錄

**範例:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**成功作業診斷記錄的屬性**

|Name               |描述
|---                |---
|succeededOn        |內嵌完成的時間
|operationId        |Azure 資料總管內嵌作業識別碼
|database           |目標資料庫的名稱
|table              |目標資料表的名稱
|ingestionSourceId  |內嵌資料來源的識別碼
|IngestionSourcePath|內嵌資料來源或 blob URI 的路徑
|rootActivityId     |活動識別碼

#### <a name="failed-ingestion-operation-log"></a>無法內嵌操作記錄檔

**範例:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**失敗作業診斷記錄的屬性**

|Name               |描述
|---                |---
|FailedOn           |內嵌完成的時間
|operationId        |Azure 資料總管內嵌作業識別碼
|database           |目標資料庫的名稱
|table              |目標資料表的名稱
|ingestionSourceId  |內嵌資料來源的識別碼
|IngestionSourcePath|內嵌資料來源或 blob URI 的路徑
|rootActivityId     |活動識別碼
|details            |失敗和錯誤訊息的詳細描述
|errorCode          |錯誤碼 
|FailureStatus      |`Permanent` 或 `Transient`。 暫時性失敗的重試可能會成功。
|originatesFromUpdatePolicy|如果失敗源自更新原則，則為 True
|shouldRetry        |如果重試可能成功，則為 True

## <a name="next-steps"></a>後續步驟

[使用計量來監視叢集健康情況](using-metrics.md)
