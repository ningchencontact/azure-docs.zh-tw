---
title: Azure 監視器所支援資源中的新版計量警示 | Microsoft Docs
description: 新版 Azure 近乎即時計量警示之支援計量和記錄的相關參考。
author: snehithm
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: 6ccb095f3739a90bdab2408965a742f9cbc19359
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2018
---
# <a name="newer-metric-alerts-for-azure-services-in-the-azure-portal"></a>Azure 入口網站中 Azure 服務的新版計量警示
Azure 監視器現在支援新的計量警示類型。 新版警示在幾個方面與[傳統計量警示](insights-alerts-portal.md)不同：

- **改善延遲**：新版計量警示的執行頻率可以達到每分鐘一次。 舊版計量警示的執行頻率一律是每 5 分鐘一次。 記錄警示的延遲仍然超過 1 分鐘，因為會花費時間擷取記錄。 
- **支援多維度計量**：您可以針對維度計量發出警示，這可讓您只監視計量中感興趣的某個區段。 
- **對計量條件有更多的控制**：您可以定義更豐富的警示規則。 新版警示支援監視計量的最大值、最小值、平均及總計值。 
- **可合併監視多個計量**：您可以使用單一規則來監視多個計量 (目前最多兩個計量)。 若兩個計量在指定的期間內都超出其個別閾值，就會觸發警示。 
- **更好的通知系統**：所有新版警示都使用[動作群組](monitoring-action-groups.md)，這些是可在多個警示中重複使用的具名通知及動作群組。 警示計量警示及舊版 Log Analytics 警示並不使用動作群組。 
- **來自記錄的計量** (有限公開預覽)：現在可將進入 Log Analytics 的記錄資料擷取並轉換成「Azure 監視器」計量，然後針對這些計量發出警示，就像任何其他計量一樣。 

若要了解如何在 Azure 入口網站中建立新版計量警示，請參閱[在 Azure 入口網站中建立警示規則](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal)。 建立警示之後，您可以使用[在 Azure 入口網站中管理警示](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal)中所述的步驟來管理警示。


## <a name="portal-powershell-cli-rest-support"></a>入口網站、PowerShell、CLI、REST 支援
目前，您只能在 Azure 入口網站或 REST API 中建立新版計量警示。 即將支援使用 PowerShell 和 Azure 命令列介面 (Azure CLI 2.0) 來設定新版警示。

## <a name="metrics-and-dimensions-supported"></a>支援的計量和維度
新版計量警示支援針對使用維度的計量發出警示。 您可以使用維度來將計量篩選到正確層級。 從 [Azure 監視器 - 計量瀏覽器 (預覽)](monitoring-metric-charts.md)，即可探索並以視覺化方式檢視所有支援的計量及適用的維度。

以下是新版警示所支援的 Azure 監視器計量來源完整清單：

|資源類型  |支援的維度  | 可用的計量|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | yes        | [API 管理](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     yes   | [自動化帳戶](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N/A| [Batch 帳戶](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    N/A     |[Redis 快取](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    N/A     | [虛擬機器](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   N/A      |[虛擬機器擴展集](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.DataFactory/factories     |   yes     |[資料處理站 (V2)](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   N/A      |[適用於 MySQL 的 DB](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    N/A     | [適用於 PostgreSQL 的 DB](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  yes      |[事件中樞](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.Logic/workflows     |     N/A    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    N/A     | [應用程式閘道](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/publicipaddresses     |  N/A       |[公用 IP 位址](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Search/searchServices     |   N/A      |[搜尋服務](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  yes       |[服務匯流排](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    yes     | [儲存體帳戶](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     yes    | [Blob 服務](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices)、[檔案服務](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices)、[佇列服務](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices)及[資料表服務](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  N/A       | [串流分析](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    N/A     | [認知服務](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces (預覽) | yes|[Log Analytics 工作區](#log-analytics-logs-as-metrics-for-alerting)|


## <a name="log-analytics-logs-as-metrics-for-alerting"></a>使用 Log Analytics 記錄作為警示計量 

您也可以針對在「來自記錄的計量」(預覽版) 程序中擷取來作為計量的常用 Log Analytics 記錄，使用新版計量警示。  
- Windows 和 Linux 電腦的[效能計數器](../log-analytics/log-analytics-data-sources-performance-counters.md)
- [代理程式健全狀況的活動訊號記錄](../operations-management-suite/oms-solution-agenthealth.md)
- [更新管理](../operations-management-suite/oms-solution-update-management.md)記錄
 
> [!NOTE]
> 只有在所選期間中有特定計量和/或維度的資料時，才會顯示特定計量和/或維度。 這些計量可供在美國東部、美國中西部及西歐有工作區且已選擇加入預覽的客戶使用。 如果您想要參與此預覽，請透過[問卷](https://aka.ms/MetricLogPreview)註冊。

以下是支援的 Log Analytics 記錄型計量來源清單：

計量名稱/詳細資料  |支援的維度  | 記錄檔類型  |
|---------|---------|---------|
|Average_Avg. Disk sec/Read     |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Windows 效能計數器      |
| Average_Avg. Disk sec/Write     |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Windows 效能計數器      |
| Average_Current Disk Queue Length   |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Windows 效能計數器      |
| Average_Disk Reads/sec    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Windows 效能計數器      |
| Average_Disk Transfers/sec    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Windows 效能計數器      |
|   Average_% Free Space    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Windows 效能計數器      |
| Average_Available MBytes     |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Windows 效能計數器      |
| Average_% Committed Bytes In Use    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Windows 效能計數器      |
| Average_Bytes Received/sec    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Windows 效能計數器      |
|  Average_Bytes Sent/sec    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Windows 效能計數器      |
|  Average_Bytes Total/sec    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Windows 效能計數器      |
|  Average_% Processor Time    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Windows 效能計數器      |
|   Average_Processor Queue Length    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Windows 效能計數器      |
|   Average_% Free Inodes   |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_% Free Space   |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_% Used Inodes  |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_% Used Space   |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Disk Read Bytes/sec    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Disk Reads/sec |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Disk Transfers/sec |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Disk Write Bytes/sec   |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Disk Writes/sec    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Free Megabytes |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Logical Disk Bytes/sec |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_% Available Memory |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_% Available Swap Space |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_% Used Memory  |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_% Used Swap Space  |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Available MBytes Memory    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Available MBytes Swap  |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Page Reads/sec |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Page Writes/sec    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Pages/sec  |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Used MBytes Swap Space |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Used Memory MBytes |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Total Bytes Transmitted    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Total Bytes Received   |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Total Bytes    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Total Packets Transmitted  |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Total Packets Received |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Total Rx Errors    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Total Tx Errors    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Total Collisions   |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Avg. Disk sec/Read |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Avg. Disk sec/Transfer |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Avg. Disk sec/Write    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Physical Disk Bytes/sec    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Pct Privileged Time    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Pct User Time  |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Used Memory kBytes |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Virtual Shared Memory  |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_% DPC Time |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_% Idle Time    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_% Interrupt Time   |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_% IO Wait Time |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_% Nice Time    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_% Privileged Time  |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_% Processor Time   |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_% User Time    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Free Physical Memory   |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Free Space in Paging Files |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Free Virtual Memory    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Processes  |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Size Stored In Paging Files    |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Uptime |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Average_Users  |     是 - Computer、ObjectName、InstanceName、CounterPath 及 SourceSystem    |   Linux 效能計數器      |
|    Heartbeat  |     是 - Computer、OSType、Version 及 SourceComputerId    |   活動訊號記錄 |
|    更新 |     是 - Computer、Product、Classification、UpdateState、Optional 及 Approved    |   更新管理 |



## <a name="payload-schema"></a>承載結構描述

當使用已適當設定的[動作群組](monitoring-action-groups.md)時，POST 作業會針對所有新版計量警示，包含下列 JSON 承載和結構描述：

```json
{"schemaId":"AzureMonitorMetricAlert","data":
    {
    "version": "2.0",
    "status": "Activated",
    "context": {
    "timestamp": "2018-02-28T10:44:10.1714014Z",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
    "name": "StorageCheck",
    "description": "",
    "conditionType": "SingleResourceMultipleMetricCriteria",
    "condition": {
      "windowSize": "PT5M",
      "allOf": [
        {
          "metricName": "Transactions",
          "dimensions": [
            {
              "name": "AccountResourceId",
              "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
            },
            {
              "name": "GeoType",
              "value": "Primary"
            }
          ],
          "operator": "GreaterThan",
          "threshold": "0",
          "timeAggregation": "PT5M",
          "metricValue": 1.0
        },
      ]
    },
    "subscriptionId": "00000000-0000-0000-0000-000000000000",
    "resourceGroupName": "Contoso",
    "resourceName": "diag500",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
    "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
  },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    }
}
```

## <a name="next-steps"></a>後續步驟

* 深入了解新的[警示體驗](monitoring-overview-unified-alerts.md)。
* 了解 [Azure 中的記錄警示](monitor-alerts-unified-log.md)。
* 深入了解 [Azure 中的警示](monitoring-overview-alerts.md)。
