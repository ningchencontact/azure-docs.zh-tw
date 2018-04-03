---
title: Azure 監視器中近乎即時計量警示 | Microsoft Docs
description: 了解如何使用近乎即時計量警示，以小到 1 分鐘的頻率來監視 Azure 資源計量。
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
ms.date: 02/26/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: 15b9b0b69f3805b3e3af1d3973fd3a77bea62ab9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="use-the-newer-metric-alerts-for-azure-services-in-azure-portal"></a>在 Azure 入口網站中針對 Azure 服務使用新版計量警示
「Azure 監視器」支援一種新的警示類型，稱為近乎即時計量警示。 

近乎即時計量警示在幾個方面與[傳統計量警示](insights-alerts-portal.md)不同：

- **改善延遲**：近乎即時計量警示的執行頻率可以達到每分鐘一次。 舊版計量警示的執行頻率一律是每 5 分鐘一次。
- **支援多維度計量**：您可以針對維度計量發出警示，以便監視計量中某個您感興趣的區段。
- **對計量條件有更好的控制**：您可以在近乎即時計量警示中定義更豐富的警示規則。 警示支援監視計量的最大值、最小值、平均與總計值。
- **可監視多個計量的整合監視功能**：近乎即時計量警示能以單一規則監視多個計量 (目前支援最多兩個計量)。 若兩個計量在指定的時間內都達到其個別閾值，則會觸發警示。
- **模組化通知系統**：近乎即時計量警示使用[動作群組](monitoring-action-groups.md)。 您可以使用動作群組來建立模組化動作。 您可以針對多個警示規則重複使用動作群組。
- **來自記錄檔的計量**：從進入 [Log Analytics](../log-analytics/log-analytics-overview.md) 的常用記錄資料中，系統可將計量擷取至「Azure 監視器」中並以近乎即時的方式發出警示。


## <a name="metrics-and-dimensions-supported"></a>支援的計量和維度
近乎即時計量警示支援使用維度之計量的警示。 您可以使用維度來將計量篩選到正確層級。 從 [Azure 監視器 - 計量瀏覽器 (預覽)](monitoring-metric-charts.md)，即可探索並以視覺化方式檢視所有支援的計量及適用的維度。

以下是支援近乎即時計量警示的 Azure 監視器型計量資源完整清單：

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
|Microsoft.OperationalInsights/workspaces (預覽) | yes|[Log Analytics 工作區](#support-for-oms-logs-as-metrics-for-alerting)|


## <a name="create-a-newer-metric-alert"></a>建立新版計量警示
目前，您只能在 Azure 入口網站或 REST API 中建立新版計量警示。 即將支援使用 PowerShell、Azure 命令列介面 (Azure CLI) 來設定近乎即時計量警示。

若要了解如何在 Azure 入口網站中建立新版計量警示，請參閱[在 Azure 入口網站中建立警示規則](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal)。

## <a name="manage-newer-metric-alerts"></a>管理新版計量警示
建立近乎即時計量警示之後，您可以使用[在 Azure 入口網站中管理您的警示](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal)中所述的步驟來管理警示。

## <a name="support-for-oms-logs-as-metrics-for-alerting"></a>支援使用 OMS 記錄作為警示計量

您也可以針對擷取來作為計量 (作為來自 [記錄預覽] 之 [計量] 的一部分) 的常用 OMS 記錄使用近乎即時計量警示。  
- Windows 和 Linux 電腦的[效能計數器](../log-analytics/log-analytics-data-sources-performance-counters.md)
- [代理程式健全狀況的活動訊號記錄](../operations-management-suite/oms-solution-agenthealth.md)
- [更新管理](../operations-management-suite/oms-solution-update-management.md)記錄

以下是支援近乎即時計量警示的 OMS 記錄檔型計量資源完整清單：

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

> [!NOTE]
> 只有在所選期間中有特定計量和/或維度的資料時，才會顯示特定計量和/或維度。 這些計量可供在美國東部、美國中西部及西歐有工作區且已選擇加入預覽的客戶使用。 如果您想要參與此預覽，請透過[問卷](https://aka.ms/MetricLogPreview)註冊。


## <a name="payload-schema"></a>承載結構描述

當使用已適當設定的[動作群組](monitoring-action-groups.md)時，POST 作業會針對所有近乎即時計量警示，包含下列 JSON 承載和結構描述：

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
