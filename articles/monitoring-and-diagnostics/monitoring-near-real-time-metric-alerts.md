---
title: "Azure 監視器中近乎即時計量警示 | Microsoft Docs"
description: "近乎即時計量警示可讓您以極高的頻率 (小至 1 分鐘) 監視 Azure 資源計量。"
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: d3e88a98e0ba93a630d131c25ca4dd5cb16f1b1a
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>近乎即時計量警示 (預覽)
Azure 監視器現在支援一種新的計量警示，稱為近乎即時計量警示 (預覽)。 此功能目前為公開預覽狀態。
這些警示在數方面都與一般計量警示不同

- **改善延遲** - 近乎即時計量警示能以每 1 分鐘一次這樣快的頻率監視計量值中的變更。
- **對計量條件有更好的控制** - 近乎即時計量警示可讓使用者定義更豐富的警示規則。 警示現在支援監視計量的最大值、最小值、平均與總計值。
- **可監視多個計量的整合監視功能** - 近乎即時計量警示能以單一值監視多個計量 (目前支援兩個)。 若兩個計量在指定的時間內都達到其個別閾值，則會觸發警示。
- **模組化通知系統** - 近乎即時計量警示使用[動作群組](monitoring-action-groups.md)。 此功能可讓使用者以模組化方式建立動作，並針對多個警示規則重複使用它們。

> [!NOTE]
> 近乎即時計量警示功能目前處於公開預覽狀態。 功能與使用者體驗可能會變更。
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>我可以為哪種資源建立近乎即時計量警示？
近乎即時計量警示支援的完整資源類型清單：

* Microsoft.ApiManagement/service
* Microsoft.Automation/automationAccounts
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Storage/storageAccounts
* Microsoft.Storage/storageAccounts/services
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.CognitiveServices/accounts

## <a name="near-real-time-metric-alerts-on-metrics-with-dimensions"></a>維度計量的近乎即時計量警示
近乎即時計量警示支援維度計量的警示。 維度是篩選計量到正確層級的方式。 對於下列資源類型，支援維度計量的近乎即時計量警示

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (僅支援美國地區的儲存體帳戶)
* Microsoft.Storage/storageAccounts/services (僅支援美國地區的儲存體帳戶)


## <a name="create-a-near-real-time-metric-alert"></a>建立近乎即時計量警示
目前，近乎即時計量警示只能透過 Azure 入口網站來建立。 即將支援透過 PowerShell、命令列介面 (CLI) 與 Azure 監視器 REST API 來設定近乎即時計量警示。

近乎即時計量警示的建立警示體驗已移至新的 **Alerts(Preview)** 功能。 即使目前的警示頁面顯示**新增近乎即時計量警示**，也會將您重新導向至新的功能。

您可以使用[這裡](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal)所述的步驟建立近乎即時計量警示。

## <a name="managing-near-real-time-metric-alerts"></a>管理近乎即時計量警示
您建立**近乎即時計量警示**後，即可使用[這裡](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal)所述的步驟來管理警示。

## <a name="payload-schema"></a>承載結構描述

POST 作業對於所有近乎即時計量警示會包含下列 JSON 承載和結構描述。

```json
{
    "WebhookName": "Alert1510875839452",
    "RequestBody": {
        "status": "Activated",
        "context": {
            "condition": {
                "metricName": "Percentage CPU",
                "metricUnit": "Percent",
                "metricValue": "17.7654545454545",
                "threshold": "1",
                "windowSize": "10",
                "timeAggregation": "Average",
                "operator": "GreaterThan"
            },
            "resourceName": "ContosoVM1",
            "resourceType": "microsoft.compute/virtualmachines",
            "resourceRegion": "westus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/automationtest/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "timestamp": "2017-11-16T23:54:03.9517451Z",
            "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/microsoft.insights/alertrules/VMMetricAlert1",
            "name": "VMMetricAlert1",
            "description": "A metric alert for the VM Win2012R2",
            "conditionType": "Metric",
            "subscriptionId": "00000000-0000-0000-0000-000000000000",
            "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "resourceGroupName": "ContosoVM"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    },
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "azure-insights/0.9",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="next-steps"></a>後續步驟

* [深入了解新的警示 (預覽) 功能](monitoring-overview-unified-alerts.md)
* [深入了解 Azure 警示 (預覽) 中的記錄警示](monitor-alerts-unified-log.md)
* [深入了解 Azure 中的警示](monitoring-overview-alerts.md)