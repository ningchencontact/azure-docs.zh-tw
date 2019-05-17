---
title: Azure 監視器中的 azure SQL 分析解決方案 |Microsoft Docs
description: Azure SQL 分析解決方案可協助您管理 Azure SQL 資料庫
services: log-analytics
ms.service: log-analytics
ms.custom: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: carlrab
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: 0617dc617309d49cdc7c8cddd4e91619b873b914
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785691"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>使用 Azure SQL 分析來監視 Azure SQL Database (預覽)

![Azure SQL 分析符號](./media/azure-sql/azure-sql-symbol.png)

Azure SQL 分析是一個進階雲端監視解決方案，可以透過單一窗格跨多個訂用帳戶大規模監視 Azure SQL 資料庫、彈性集區和受控執行個體的效能。 它會收集重要的 Azure SQL Database 效能計量，並且以視覺效果方式呈現，具有內建智慧可以執行效能疑難排解。

藉由使用您以解決方案收集的計量，您可以建立自訂的監視規則和警示。 解決方案可協助您找出應用程式堆疊中每個層級的問題。 它使用 Azure 诊断指标和 Azure Monitor 视图，在单个 Log Analytics 工作区中呈现有关所有 Azure SQL 数据库和弹性池和托管实例中的数据库的数据。 Azure Monitor 可帮助用户收集、关联和可视化结构化和非结构化数据。

如需使用 Azure SQL Analytics 解決方案，以及一般使用案例的實際操作概觀，請觀看內嵌影片：

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>連接的來源

Azure SQL 分析是僅限雲端的監視解決方案，支援適用於 Azure SQL 資料庫 (單一、集區，以及受控執行個體資料庫) 的診斷遙測串流。 解決方案不使用連接至 Azure 監視代理程式，因為方案不支援監視裝載的 SQL Server 內部部署或在 Vm 中，請參閱以下的相容性表格。

| 連接的來源 | 支援 | 描述 |
| --- | --- | --- |
| [Azure 診斷](../platform/collect-azure-metrics-logs.md) | **是** | Azure 計量與記錄資料會直接傳送至 Azure 監視器記錄檔 Azure。 |
| [Azure 儲存體帳戶](../platform/collect-azure-metrics-logs.md) | 否 | Azure 監視器不會從儲存體帳戶讀取資料。 |
| [Windows 代理程式](../platform/agent-windows.md) | 否 | 解決方案不使用直接 Windows 代理程式。 |
| [Linux 代理程式](../learn/quick-collect-linux-computer.md) | 否 | 解決方案不使用直接 Linux 代理程式。 |
| [System Center Operations Manager 管理群組](../platform/om-agents.md) | 否 | 從 Operations Manager 代理程式的直接連線至 Azure 監視器不會使用此解決方案。 |

## <a name="configuration"></a>組態
使用[从解决方案库中添加 Azure Monitor 解决方案](../../azure-monitor/insights/solutions.md)中所述的流程，将 Azure SQL Analytics（预览）解决方案添加到 Log Analytics 工作区。

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>設定 Azure SQL Database、彈性集區 和受控執行個體以串流診斷遙測

在您於工作區中建立 Azure SQL Analytics 解決方案之後，您必須**設定每個**您想要監視的資源，以將它的診斷遙測串流至解決方案。 遵循此頁面上的詳細指示：

- 為 Azure SQL 資料庫啟用 Azure 診斷，以[將診斷遙測串流至 Azure SQL 分析](../../sql-database/sql-database-metrics-diag-logging.md)。

上述頁面同時提供啟用支援將單一 Azure SQL 分析工作區作為單一窗口來監視多個 Azure 訂用帳戶的操作指示。

## <a name="using-the-solution"></a>使用解決方案

當您將解決方案新增至您的工作區時，Azure SQL 分析圖格會新增至您的工作區，而且會顯示在 [概觀] 中。 選取 檢視摘要連結，以載入磚內容。

![Azure SQL 分析摘要圖格](./media/azure-sql/azure-sql-sol-tile-01.png)

載入之後，圖格會顯示 Azure SQL 資料庫、 彈性集區、 受控執行個體和資料庫的數字，方案會接收從診斷遙測的受控執行個體中。

![Azure SQL 分析圖格](./media/azure-sql/azure-sql-sol-tile-02.png)

解決方案可提供兩個不同的檢視，一個用於監視 Azure SQL Database 和彈性集區，另一個檢視用於監視受控執行個體和受控執行個體中的資料庫。

若要檢視適用於 Azure SQL Database 和彈性集區的 Azure SQL 分析監視儀表板，請按一下圖格的上半部。 若要檢視適用於受控執行個體和受控執行個體資料庫的 Azure SQL 分析監視儀表板，請按一下圖格的下半部。

### <a name="viewing-azure-sql-analytics-data"></a>檢視 Azure SQL 分析資料

儀表板包含透過不同檢視方塊監視之所有資料庫的概觀。 要使不同透视起效，必须允许将有关 SQL 资源的适当指标或日志流式传输到 Log Analytics 工作区。

请注意，如果某些指标或日志未流式传输到 Azure Monitor，则不会使用监视信息填充解决方案中的磁贴。

### <a name="azure-sql-database-and-elastic-pool-view"></a>Azure SQL Database 和彈性集區檢視

選取適用於資料庫的 [Azure SQL 分析] 圖格後，便會顯示監視儀表板。

![Azure SQL 分析概觀](./media/azure-sql/azure-sql-sol-overview.png)

選取任何磚，以便在特定的檢視方塊中開啟向下鑽研報表。 一旦選取檢視方塊，向下鑽研報表隨即開啟。

![Azure SQL 分析逾時](./media/azure-sql/azure-sql-sol-metrics.png)

此檢視中的每個檢視方塊都會提供訂用帳戶、伺服器、彈性集區和資料庫層級的摘要。 此外，每個檢視方塊都會在右側顯示檢視方塊專屬的報表。 從清單中選取訂用帳戶、伺服器、集區或資料庫可繼續往下鑽研。

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>受控執行個體和受控執行個體中的資料庫檢視

選取適用於資料庫的 [Azure SQL 分析] 圖格後，便會顯示監視儀表板。

![Azure SQL 分析概觀](./media/azure-sql/azure-sql-sol-overview-mi.png)

選取任何磚，以便在特定的檢視方塊中開啟向下鑽研報表。 一旦選取檢視方塊，向下鑽研報表隨即開啟。

選取 [受控執行個體] 檢視會顯示受控執行個體使用量的詳細資料、其所包含的資料庫，以及跨執行個體所執行的查詢遙測。

![Azure SQL 分析逾時](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="perspectives"></a>檢視方塊

下表概述兩個儀表板版本所支援的檢視方塊，一個適用於 Azure SQL 資料庫和彈性集區，另一個適用於受控執行個體。

| 檢視方塊 | 描述 | SQL Database 和彈性集區支援 | 支援受控執行個體 |
| --- | ------- | ----- | ----- |
| 資源 (依類型) | 可計算所有受監視資源的檢視方塊。 | 是 | 是 |
| 深入解析 | 可透過階層的方式，向下鑽研至 Intelligent Insights 乃至效能。 | 是 | 是 |
| Errors | 可透過階層的方式，向下鑽研至資料庫上發生的 SQL 錯誤。 | 是 | 是 |
| 逾時 | 可透過階層的方式，向下鑽研至資料庫上發生的 SQL 逾時。 | 是 | 否 |
| 封鎖 | 可透過階層的方式，向下鑽研至資料庫上發生的 SQL 封鎖。 | 是 | 否 |
| 資料庫等候 | 可透過階層的方式，向下鑽研至資料庫層級的 SQL 等候統計資料。 包含總等候時間及每種等候類型等候時間的摘要。 |是 | 是 |
| 查詢持續時間 | 可透過階層的方式，向下鑽研至查詢執行統計資料，例如查詢持續時間、CPU 使用量、資料 IO 使用量、記錄 IO 使用量。 | 是 | 是 |
| 查詢等候 | 可透過階層的方式，依等候類別，向下鑽研至查詢等候統計資料。 | 是 | 是 |

### <a name="intelligent-insights-report"></a>Intelligent Insights 報表

Azure SQL Database [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) 可讓您了解所有 Azure SQL 資料庫的效能情況。 收集的所有 Intelligent Insights 都可以透過 Insights 檢視方塊視覺化及存取。

![Azure SQL 分析見解](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>彈性集區和資料庫報表

彈性集區和 SQL Database 都有自己特定的報表，可顯示在指定的時間，針對資源收集的所有資料。

![Azure SQL 分析資料庫](./media/azure-sql/azure-sql-sol-database.png)

![Azure SQL 彈性集區](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>查詢報表

您可以透過查詢持續時間和查詢等候檢視方塊，將任何查詢的效能透過查詢報表相互關聯。 此報表會比較不同資料庫上的查詢效能，並可讓您輕鬆地找出所選查詢執行速度良好與緩慢的資料庫。

![Azure SQL 分析查詢](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>權限

若要使用 Azure SQL 分析，使用者需至少在 Azure 中取得「讀者」角色使用權限。 不過此角色並無法讓使用者取得觀看查詢文字，或執行任何自動調整動作的權限。 Azure 中可讓您取得最完整解決方案使用權限的角色為擁有者、參與者、SQL DB 參與者和 SQL Server 參與者。 您也可能會考慮在入口網站中建立自訂角色，並將使用權限設定為僅限 Azure SQL 分析，不可存取管理其他資源。

### <a name="creating-a-custom-role-in-portal"></a>在入口網站中建立自訂角色

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

我們了解到某些組織會在 Azure 中實施嚴格的使用權限控制，因此請參閱下列 PowerShell 指令碼，它可以在 Azure 入口網站中建立自訂角色「SQL 分析監視人員」，此角色僅擁有使用完整 Azure SQL 分析所需之最低限度讀取和寫入使用權限。

在下列指令碼中，請將 “{SubscriptionId}" 取代為您的 Azure 訂用帳戶識別碼，接著以擁有者或參與者角色身分登入 Azure 並執行指令碼。

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/*");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

新角色建立後，請將這個角色指派給需要 Azure SQL 分析使用權限的每一位使用者。

## <a name="analyze-data-and-create-alerts"></a>分析資料並建立警示

Azure SQL 分析中的資料分析以 [Log Analytics 語言](../log-query/get-started-queries.md)為基礎，提供您自訂查詢和報告功能。 請參閱[可用的計量和記錄](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available)，內有針對收集自資料庫資源可自訂查詢的資料的說明。

解決方案中的自動化警示以寫入的 Log Analytics 查詢為依據，一旦符合條件，該查詢便會觸發警示。 以下請參考數個可在解決方案中設為警示設定的 Log Analytics 查詢。

### <a name="creating-alerts-for-azure-sql-database"></a>建立 Azure SQL Database 的警示

您可以使用來自 Azure SQL Database 資源的資料，輕鬆[建立警示](../platform/alerts-metric.md)。 以下是您可以搭配記錄警示使用的一些實用[記錄查詢](../log-query/log-query-overview.md)：

#### <a name="high-cpu-on-azure-sql-database"></a>Azure SQL Database 上的高 CPU

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - 此警示所設定的預先需求是該受監視的資料庫資料流至解決方案的基本計量。
> - 請將 MetricName 值 cpu_percent 更換為 dtu_consumption_percent，即可改為取得高 DTU 結果。

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Azure SQL Database 彈性集區上的高 CPU

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - 此警示所設定的預先需求是該受監視的資料庫資料流至解決方案的基本計量。
> - 請將 MetricName 值 cpu_percent 更換為 dtu_consumption_percent，即可改為取得高 DTU 結果。

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>過去 1 小時的平均 Azure SQL Database 儲存體高於 95%

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
> - 此警示所設定的預先需求是該受監視的資料庫資料流至解決方案的基本計量。
> - 這項查詢需要將警示規則設定為會在有查詢結果 (> 0 個結果) 時引發警示，這表示某些資料庫上有此情況。 其輸出中會列出所定義 time_range 內高於 storage_threshold 的資料庫資源。
> - 其輸出中會列出所定義 time_range 內高於 storage_threshold 的資料庫資源。

#### <a name="alert-on-intelligent-insights"></a>針對 Intelligent insights 的警示

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
> - 若要設定此警示，先決條件是受監視的資料庫會將 SQLInsights 診斷記錄串流至解決方案。
> - 這項查詢需要將警示規則設定為利用和 alert_run_interval 相同的頻率來執行，以避免產生重複結果。 請將此規則設定為會在有查詢結果 (> 0 個結果) 時引發警示。
> - 自訂 alert_run_interval 來指定時間範圍，以檢查設定為會將 SQLInsights 記錄串流至解決方案的資料庫上是否發生此情況。
> - 自訂 insights_string 以擷取深入解析根本原因分析文字的輸出。 在可從現有深入解析使用的解決方案中，其 UI 中會顯示此相同文字。 或者，您也可以使用下列查詢來查看訂用帳戶所產生的所有深入解析文字。 請使用查詢的輸出來搜集可供對深入解析設定警示的不同字串。

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instance"></a>建立受控執行個體的警示

#### <a name="managed-instance-storage-is-above-90"></a>受控執行個體儲存體高於 90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
> - 若要設定此警示，預先需求是所監視的受控執行個體已對解決方案啟用 ResourceUsageStats 記錄的串流。
> - 這項查詢需要將警示規則設定為會在有查詢結果 (> 0 個結果) 時引發警示，這表示受控執行個體上有此情況。 輸出是受控執行個體上的儲存體耗用量百分比。

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>過去 1 小時的受控執行個體 CPU 平均耗用量高於 95%

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
> - 若要設定此警示，預先需求是所監視的受控執行個體已對解決方案啟用 ResourceUsageStats 記錄的串流。
> - 這項查詢需要將警示規則設定為會在有查詢結果 (> 0 個結果) 時引發警示，這表示受控執行個體上有此情況。 輸出是受控行個體上在定義期間中的平均 CPU 使用率百分比耗用量。

### <a name="pricing"></a>價格

雖然可免費使用解決方案，但診斷資料的使用量若超過每個月所配置的免費資料擷取單位，則需付費，請參閱 [Log Analytics 定價](https://azure.microsoft.com/pricing/details/monitor)。 所提供的免費資料擷取單位可讓您每個月免費監視多個資料庫。 請注意，相較於閒置的資料庫，較繁重工作負載的更多作用中資料庫會擷取更多資料。 您可以藉由選取 Azure SQL 分析導覽功能表上的 OMS 工作區，然後選取 [使用量和估計成本]，輕鬆地監視您解決方案中的資料擷取耗用。

## <a name="next-steps"></a>後續步驟

- 使用 [Azure Monitor 中的日志查询](../log-query/log-query-overview.md)查看详细的 Azure SQL 数据。
- [建立您自己的儀表板](../learn/tutorial-logs-dashboards.md)來顯示 Azure SQL 資料。
- 在特定的 Azure SQL 事件發生時[建立警示](../platform/alerts-overview.md)。
