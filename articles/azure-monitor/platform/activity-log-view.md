---
title: 在 Azure 監視器中檢視 Azure 活動記錄事件
description: 在 Azure 監視器中檢視 Azure 活動記錄檔，並使用 PowerShell、 CLI 和 REST API 擷取。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 32578f77f2b3f30d80953bdd1099d22c945c640b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248110"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>檢視和擷取 Azure 活動記錄檔事件

[Azure 活動記錄檔](activity-logs-overview.md)提供深入了解在 Azure 中發生的訂用帳戶層級事件。 這篇文章會提供不同的方法，來檢視和擷取活動記錄事件的詳細資料。

## <a name="azure-portal"></a>Azure 入口網站
檢視活動記錄檔，所有的資源**監視器**在 Azure 入口網站中的功能表。 檢視特定資源中的活動記錄檔**活動記錄檔**該資源的功能表中的選項。

![檢視活動記錄檔](./media/activity-logs-overview/view-activity-log.png)

您可以篩選活動記錄事件，由下列欄位：

* **Timespan**:事件開始和結束時間。
* **類別**：事件類別目錄中所述[活動記錄檔中的類別目錄](activity-logs-overview.md#categories-in-the-activity-log)。
* 訂用帳戶  ：一或多個 Azure 訂用帳戶的名稱。
* **资源组**：一或多個資源群組選取的訂用帳戶中。
* **資源 （名稱）** :-特定資源的名稱。
* **資源類型**：資源類型，例如_microsoft.compute/virtualmachines_。
* **作業名稱**-Azure Resource Manager 作業，例如名稱_Microsoft.SQL/servers/Write_。
* **嚴重性**：事件的嚴重性層級。 可用的值為_Informational_，_警告_，_錯誤_，_重大_。
* **事件起始者**：執行此作業的使用者。
* **開啟搜尋**:開啟文字搜尋方塊搜尋該字串的所有欄位中的所有事件。

### <a name="view-change-history"></a>檢視變更歷程記錄

檢閱時，活動記錄檔，它可以協助查看變更期間發生該事件的時間。 您可以檢視這項資訊搭配**修訂歷程記錄**。 選取您想要尋找更深入到活動記錄檔中的事件。 選取 **修訂歷程記錄 （預覽）** 索引標籤來檢視任何與該事件相關聯的變更。

![變更事件的歷程記錄清單](media/activity-logs-overview/change-history-event.png)

如果有任何與事件相關聯的變更，您會看到一份您可以選取的變更。 這會開啟**修訂歷程記錄 （預覽）** 頁面。 在此頁面上，您會看到該資源所做的變更。 您可以看到下列的範例中，我們可不只會看到 VM 變更大小，但先前的 VM 大小已變更之前，它已變更為。

![顯示差異的變更歷程記錄頁面](media/activity-logs-overview/change-history-event-details.png)

若要深入了解變更歷程記錄，請參閱[取得資源變更](../../governance/resource-graph/how-to/get-resource-changes.md)。


## <a name="log-analytics-workspace"></a>Log Analytics 工作區
按一下 **記錄檔**頂端**活動記錄檔**頁面，即可開啟[Activity Log Analytics 監視解決方案](activity-log-collect.md)訂用帳戶。 這可讓您檢視分析活動記錄檔，並執行[記錄查詢](../log-query/log-query-overview.md)具有**AzureActivity**資料表。 如果您的活動記錄檔未連線到 Log Analytics 工作區中，系統會提示您執行這項設定。



## <a name="powershell"></a>PowerShell
使用[Get AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) cmdlet 從 PowerShell 擷取活動記錄檔。 以下是一些常見的範例。

> [!NOTE]
> `Get-AzLog` 只提供 15 天的歷程記錄。 使用 **-MaxEvents**查詢超過 15 天前的 n 個事件的參數。 若要存取 15 天前的事件，使用 REST API 或 SDK。 如果您未包含 **StartTime**，則預設值是 **EndTime** 減去一小時。 如果您未包含 **EndTime**，則預設值是目前的時間。 所有時間都是採用 UTC 格式。


取得特定的日期時間之後建立的記錄項目：

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

取得記錄項目之間的日期時間範圍：

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

取得特定資源群組中的記錄檔項目︰

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

從特定的資源提供者之間的日期時間範圍中取得記錄項目：

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

取得與特定的呼叫端的記錄項目：

```powershell
Get-AzLog -Caller 'myname@company.com'
```

取得最後 1000 個事件：

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>CLI
使用[az 監視活動記錄檔](cli-samples.md#view-activity-log-for-a-subscription)以擷取 CLI 中的活動記錄檔。 以下是一些常見的範例。


檢視所有可用的選項。

```azurecli
az monitor activity-log list -h
```

取得特定資源群組中的記錄檔項目︰

```azurecli
az monitor activity-log list --resource-group <group name>
```

取得與特定的呼叫端的記錄項目：

```azurecli
az monitor activity-log list --caller myname@company.com
```

取得資源類型，日期範圍內的呼叫端的記錄檔：

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST API
使用[Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor/)從 REST 用戶端擷取活動記錄檔。 以下是一些常見的範例。

開始篩選活動記錄：

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

取得活動記錄篩選條件，選取：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

取得與選取的活動記錄：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

取得活動記錄檔，而不需要篩選或選取：

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>後續步驟

* [讀取活動記錄概觀](activity-logs-overview.md)
* [活動記錄封存至儲存體，或將其串流至事件中樞](activity-log-export.md)
* [將 Azure 活動記錄檔串流至事件中樞](activity-logs-stream-event-hubs.md)
* [Azure 活動記錄封存至儲存體](archive-activity-log.md)

