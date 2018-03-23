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
ms.openlocfilehash: 88995b1f3350fe485e28efccc93779ae0a42eb97
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>近乎即時計量警示 (預覽)
Azure 監視器支援一種新的警示類型，稱為近乎即時計量警示 (預覽)。 此功能目前為公開預覽狀態。

近乎即時計量警示與一般計量警示在一些方面有所不同：

- **改善延遲**：近乎即時計量警示能以小到 1 分鐘的頻率監視計量值中的變更。
- **對計量條件有更好的控制**：您可以在近乎即時計量警示中定義更豐富的警示規則。 警示支援監視計量的最大值、最小值、平均與總計值。
- **來自記錄檔的計量**：從進入 [Log Analytics](../log-analytics/log-analytics-overview.md) 的常用記錄資料中，系統可將計量擷取至「Azure 監視器」中並以近乎即時的方式發出警示
- **可監視多個計量的整合監視功能**：近乎即時計量警示能以單一規則監視多個計量 (目前支援最多兩個計量)。 若兩個計量在指定的時間內都達到其個別閾值，則會觸發警示。
- **模組化通知系統**：近乎即時計量警示使用[動作群組](monitoring-action-groups.md)。 您可以使用動作群組來建立模組化動作。 您可以針對多個警示規則重複使用動作群組。

> [!NOTE]
> 近乎即時計量警示目前為公開預覽版。 來自記錄檔的計量功能為「有限」公開預覽版。 功能與使用者體驗可能會變更。
>

## <a name="metrics-and-dimensions-supported"></a>支援的計量和維度
近乎即時計量警示支援使用維度之計量的警示。 您可以使用維度來將計量篩選到正確層級。 從 [Azure 監視器 - 計量瀏覽器 (預覽)](monitoring-metric-charts.md)，即可探索並以視覺化方式檢視所有支援的計量及適用的維度。

以下是支援近乎即時計量警示的 Azure 監視器型計量資源完整清單：

|計量名稱/詳細資料  |支援的維度  |
|---------|---------|
|Microsoft.ApiManagement/service     | yes        |
|Microsoft.Automation/automationAccounts     |     N/A    |
|Microsoft.Automation/automationAccounts     |   N/A      |
|Microsoft.Cache/Redis     |    N/A     |
|Microsoft.Compute/virtualMachines     |    N/A     |
|Microsoft.Compute/virtualMachineScaleSets     |   N/A      |
|Microsoft.DataFactory/factories     |   N/A      |
|Microsoft.DBforMySQL/servers     |   N/A      |
|Microsoft.DBforPostgreSQL/servers     |    N/A     |
|Microsoft.EventHub/namespaces     |   N/A      |
|Microsoft.Logic/workflows     |     N/A    |
|Microsoft.Network/applicationGateways     |    N/A     |
|Microsoft.Network/publicipaddresses     |  N/A       |
|Microsoft.Search/searchServices     |   N/A      |
|Microsoft.ServiceBus/namespaces     |  N/A       |
|Microsoft.Storage/storageAccounts     |    yes     |
|Microsoft.Storage/storageAccounts/services     |     yes    |
|Microsoft.StreamAnalytics/streamingjobs     |  N/A       |
|Microsoft.CognitiveServices/accounts     |    N/A     |


來自記錄檔的計量目前支援下列常用的 OMS 記錄檔：
- Windows 和 Linux 電腦的[效能計數器](../log-analytics/log-analytics-data-sources-performance-counters.md)
- 電腦的活動訊號記錄
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
|    Update |     是 - Computer、Product、Classification、UpdateState、Optional 及 Approved    |   更新管理 |

> [!NOTE]
> 只有在所選期間中有特定計量和/或維度的資料時，才會顯示特定計量和/或維度

## <a name="create-a-near-real-time-metric-alert"></a>建立近乎即時計量警示
目前，您只能在 Azure 入口網站中建立近乎即時計量警示。 即將支援使用 PowerShell、Azure 命令列介面 (Azure CLI) 與 Azure 監視器 REST API 來設定近乎即時計量警示。

近乎即時計量警示的建立體驗已移至新的**警示 (預覽)** 功能。 即使目前的警示分頁顯示**新增近乎即時計量警示**，也會將您重新導向至**警示 (預覽)** 分頁。

若要了解如何建立近乎即時計量警示，請造訪[在 Azure 入口網站建立警示規則](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal)。

## <a name="manage-near-real-time-metric-alerts"></a>管理近乎即時計量警示
建立近乎即時計量警示之後，您可以使用[在 Azure 入口網站中管理您的警示](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal)中所述的步驟來管理警示。

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

* 深入了解新的[警示 (預覽) 體驗](monitoring-overview-unified-alerts.md)。
* 深入了解 [Azure 警示中的記錄警示 (預覽)](monitor-alerts-unified-log.md)。
* 深入了解 [Azure 中的警示](monitoring-overview-alerts.md)。
