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
ms.openlocfilehash: cd1002929ad749ac1742e914a9f2411f09ec91d5
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2017
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

## <a name="near-real-time-metric-alerts-on-metrics-with-dimensions"></a>維度的標準幾近即時度量警示
接近即時度量警示支援維度度量的警示。 維度是篩選您的權限層級度量的方式。 接近即時的度量維度度量的警示類型都支援下列資源

* Microsoft.ApiManagement/service
* Microsoft.storage / （僅支援美國地區的儲存體帳戶）
* Microsoft.Storage/storageAccounts/services （僅支援美國地區的儲存體帳戶）


## <a name="create-a-near-real-time-metric-alert"></a>建立近乎即時計量警示
目前，近乎即時計量警示只能透過 Azure 入口網站來建立。 即將支援透過 PowerShell、命令列介面 (CLI) 與 Azure 監視器 REST API 來設定近乎即時計量警示。

接近即時度量警示建立警示功能已移至新**Alerts(Preview)**體驗。 即使目前的警示頁面上顯示**接近即時度量加入警示**，將您重新導向至新的體驗。

您可以建立使用所述的步驟幾近即時度量警示[這裡](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal)。

## <a name="managing-near-real-time-metric-alerts"></a>管理近乎即時計量警示
一旦您建立**接近即時度量警示**，它可以使用所述的步驟來管理[這裡](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal)。

## <a name="next-steps"></a>後續步驟

* [深入了解新的警示 （預覽） 體驗](monitoring-overview-unified-alerts.md)
* [深入了解 Azure 警示 （預覽） 中的記錄檔警示](monitor-alerts-unified-log.md)
* [深入了解 Azure 中的警示](monitoring-overview-alerts.md)