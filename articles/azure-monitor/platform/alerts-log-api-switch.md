---
title: 從舊版 Log Analytics 警示 API 切換至新的 Azure 警示 API
description: 概述以舊版 savedSearch 為基礎的 Log Analytics 警示 API 遭汰用的過程，以及將警示規則切換至新的 ScheduledQueryRules API 的程序，並詳細說明如何消除客戶常見的顧慮。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: cdc3e7ec6ec55c3376aeb545e1f64079ad1f6323
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407397"
---
# <a name="switch-api-preference-for-log-alerts"></a>切換記錄警示的 API 喜好設定

> [!NOTE]
> 內容所述適用於使用者僅限 Azure 公用雲端並**不**適用於 Azure Government 或 Azure 中國雲端。  

直到最近，您都還是在 Microsoft Operations Management Suite 入口網站中管理警示規則。 新的警示體驗已與 Microsoft Azure 中的各種服務 (包括 Log Analytics) 整合，我們要求[您將警示規則從 OMS 入口網站延伸至 Azure](alerts-extend.md)。 但為了確保能盡可能縮短客戶的中斷時間，此過程並未改變其供使用的程式設計介面 - 以 SavedSearch 為基礎的 [Log Analytics 警示 API](api-alerts.md)。

不過，您現在為 Log Analytics 警示使用者發佈了真正的 Azure 程式設計替代工具 - [Azure 監視器 - ScheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)；這也會反映在您的 [Azure 計費 - 記錄警示](alerts-unified-log.md#pricing-and-billing-of-log-alerts)中。 若要深入了解如何使用此 API 管理您的記錄警示，請參閱[使用 Azure 資源範本管理記錄警示](alerts-log.md#managing-log-alerts-using-azure-resource-template)和[使用 PowerShell、CLI 或 API 管理記錄警示](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api)。

## <a name="benefits-of-switching-to-new-azure-api"></a>切換至新 Azure API 的好處

使用 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 來建立和管理警示有幾項[舊版 Log Analytics 警示 API](api-alerts.md) 無法提供的好處；以下列出部分最主要的優點：

- 能夠在警示規則中[跨工作區進行記錄搜尋](../log-query/cross-workspace-query.md)，並且可延伸到外部資源，例如 Log Analytics 工作區甚或 Application Insights 應用程式
- 使用多個欄位在查詢中進行分組時，使用者可利用 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 來指定要在 Azure 入口網站中彙總哪些欄位
- 記錄警示若是使用 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 來建立的，可定義的期間最長為 48 小時，且擷取資料的期間比以前長
- 在同一作業中將警示規則建立為單一資源，而不需要像[舊版 Log Analytics 警示 API](api-alerts.md) 一樣建立三個層級的資源
- 在 Azure 中各種以查詢為基礎的記錄警示全都使用同一個程式設計介面 - 新的 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 可用來管理 Log Analytics 和 Application Insights 的規則
- 所有新的記錄警示功能和未來的開發都將只能透過新的 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 來使用和進行

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>從舊版記錄警示 API 切換的程序

從[舊版 Log Analytics 警示 API](api-alerts.md) 移轉警示規則的程序，不包含以任何方式變更您的警示定義、查詢或設定。 警示規則和監視都不會影響和警示將不會停止，或停止，期間或之後的參數。

使用者可以自行選擇使用[舊版 Log Analytics 警示 API](api-alerts.md) 或新的 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)。 無論以哪個 API 建立的警示規則，都將*只能以相同的 API 來管理* - 以及從 Azure 入口網站來管理。 根據預設，Azure 監視器將會繼續使用[舊版 Log Analytics 警示 API](api-alerts.md) 從 Azure 入口網站建立任何新的警示規則。

喜好設定切換至 scheduledQueryRules API 的影響如下所述：

- 為了透過程式設計介面來管理記錄警示所做的所有互動，現在都必須改用 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 來完成。 如需詳細資訊，請參閱[透過 Azure 資源範本的使用範例](alerts-log.md#managing-log-alerts-using-azure-resource-template)和[透過 Azure CLI 和 PowerShell 的使用範例](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api)
- 任何在 Azure 入口網站中建立的新記錄警示規則，都將使用 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 來建立，並且也允許使用者透過 Azure 入口網站使用[新 API 的其他功能](#benefits-of-switching-to-new-azure-api)
- 記錄警示規則的嚴重性將會從轉換：*重大、 警告和資訊*，以*嚴重性值 0、 1 與 2*。 以及用於建立/更新警示規則嚴重性以及 4 的選項。

> [!CAUTION]
> 當使用者選擇要切換至新的喜好設定[scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)，選擇 上一步或還原為較舊的使用方式的規則無法[舊版 Log Analytics 警示 API](api-alerts.md)。

任何自願切換至新的 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)，並停止使用[舊版 Log Analytics 警示 API](api-alerts.md) 的客戶，都可在下列 API 執行 PUT 呼叫，以切換所有與特定 Log Analytics 工作區相關聯的警示規則，而達到此目的。

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

使用包含下列 JSON 的要求本文。

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

存取此 API 時，也可以從 PowerShell 命令列使用 [ARMClient](https://github.com/projectkudu/ARMClient) (一個可簡化 Azure Resource Manager API 叫用流程的開放原始碼命令列工具) 來存取。 如下所示，在範例 PUT 呼叫中，是使用 ARMclient 工具來切換與特定 Log Analytics 工作區相關聯的所有警示規則。

```PowerShell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

如果 Log Analytics 工作區中的所有警示規則成功切換為使用新的 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)，系統將提供下列回應。

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

使用者也可以查看 Log Analytics 工作區的目前狀態，並確認工作區是否已切換為僅使用 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)。 若要加以確認，使用者可以在下列 API 執行 GET 呼叫。

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

若要使用 PowerShell 命令列以 [ARMClient](https://github.com/projectkudu/ARMClient) 工具執行上述程式碼，請參閱下方範例。

```PowerShell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

如果指定的 Log Analytics 工作區已切換為僅使用 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)，回應 JSON 將如下所示。

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
反之，如果指定的 Log Analytics 工作區尚未切換為僅使用 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)，回應 JSON 將如下所示。

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 監視器 - 記錄警示](alerts-unified-log.md)。
- 了解如何建立 [Azure 警示中的記錄警示](alerts-log.md)。
- 深入了解 [Azure 警示體驗](../../azure-monitor/platform/alerts-overview.md)。
