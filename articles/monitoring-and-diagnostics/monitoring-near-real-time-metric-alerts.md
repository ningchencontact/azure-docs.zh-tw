---
title: "Azure 監視器中近乎即時計量警示 | Microsoft Docs"
description: "了解如何使用近乎即時計量警示，以小到 1 分鐘的頻率來監視 Azure 資源計量。"
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
ms.openlocfilehash: 6370f4596e6b20962c6de0dbcbd5f86c3b7777cc
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>近乎即時計量警示 (預覽)
Azure 監視器支援一種新的警示類型，稱為近乎即時計量警示 (預覽)。 此功能目前為公開預覽狀態。

近乎即時計量警示在數方面都與一般計量警示不同：

- **改善延遲**：近乎即時計量警示能以小到 1 分鐘的頻率監視計量值中的變更。
- **對計量條件有更好的控制**：您可以在近乎即時計量警示中定義更豐富的警示規則。 警示支援監視計量的最大值、最小值、平均與總計值。
- **可監視多個計量的整合監視功能**：近乎即時計量警示能以單一規則監視多個計量 (目前支援最多兩個計量)。 若兩個計量在指定的時間內都達到其個別閾值，則會觸發警示。
- **模組化通知系統**：近乎即時計量警示使用[動作群組](monitoring-action-groups.md)。 您可以使用動作群組來建立模組化動作。 您可以針對多個警示規則重複使用動作群組。

> [!NOTE]
> 近乎即時計量警示功能目前處於公開預覽狀態。 功能與使用者體驗可能會變更。
>

## <a name="resources-you-can-use-with-near-real-time-metric-alerts"></a>您可以與近乎即時計量警示搭配使用的資源
以下是支援近乎即時計量警示的完整資源類型清單：

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

## <a name="near-real-time-metric-alerts-for-metrics-that-use-dimensions"></a>使用維度的近乎即時計量警示
近乎即時計量警示支援使用維度之計量的警示。 您可以使用維度來將計量篩選到正確層級。 對於下列資源類型，支援使用維度之計量的近乎即時計量警示：

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (僅支援美國地區的儲存體帳戶)
* Microsoft.Storage/storageAccounts/services (僅支援美國地區的儲存體帳戶)

## <a name="create-a-near-real-time-metric-alert"></a>建立近乎即時計量警示
目前，您只能在 Azure 入口網站中建立近乎即時計量警示。 即將支援使用 PowerShell、Azure 命令列介面 (Azure CLI) 與 Azure 監視器 REST API 來設定近乎即時計量警示。

近乎即時計量警示的建立體驗已移至新的**警示 (預覽)** 功能。 即使目前的警示分頁顯示**新增近乎即時計量警示**，也會將您重新導向至**警示 (預覽)** 分頁。

若要了解如何建立近乎即時計量警示，請造訪[在 Azure 入口網站建立警示規則](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal)。

## <a name="manage-near-real-time-metric-alerts"></a>管理近乎即時計量警示
建立近乎即時計量警示之後，您可以使用[在 Azure 入口網站中管理您的警示](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal)中所述的步驟來管理警示。

## <a name="payload-schema"></a>承載結構描述

POST 作業對於所有近乎即時計量警示會包含下列 JSON 承載和結構描述：

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

* 深入了解新的[警示 (預覽) 體驗](monitoring-overview-unified-alerts.md)。
* 深入了解 [Azure 警示中的記錄警示 (預覽)](monitor-alerts-unified-log.md)。
* 深入了解 [Azure 中的警示](monitoring-overview-alerts.md)。