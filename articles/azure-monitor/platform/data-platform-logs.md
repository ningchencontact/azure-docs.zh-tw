---
title: 登入 Azure 監視器 |Microsoft Docs
description: 說明在 Azure 監視器中可用來監視資料的進階分析的記錄檔。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 0203/26/2019
ms.author: bwren
ms.openlocfilehash: a7271aa3faf438b42319f8c2c297c6e39baab92e
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904146"
---
# <a name="logs-in-azure-monitor"></a>Azure 監視器中的記錄

> [!NOTE]
> Azure 監視器所收集的所有資料都適合其中兩個基本的型別，度量和記錄檔。 本文說明記錄檔。 請參閱[Azure 監視器計量](data-platform-metrics.md)取得度量資訊和詳細的描述[Azure 監視器所收集的監視資料](data-platform.md)兩個比較。

Azure 監視器中的記錄檔是特別適合從各種來源的資料執行複雜的分析。 本文說明如何記錄檔會在 Azure 監視器中，您可以如何處理資料中，結構化和識別在記錄檔中儲存資料的不同資料來源。

> [!NOTE]
> 請務必區分 Azure 監視器記錄檔和記錄資料，在 Azure 中的來源。 比方說，在 Azure 中的訂用帳戶層級事件會寫入[活動記錄檔](activity-logs-overview.md)，您可以從 Azure 監視器功能表檢視。 最多資源會將操作資訊寫入[診斷記錄](diagnostic-logs-overview.md)，您可以將它轉送至不同的位置。 Azure 監視器記錄檔會收集活動記錄檔和診斷記錄連同其他監視資料，以提供深入分析您的資源的整個集合的記錄檔資料平台。

## <a name="what-are-azure-monitor-logs"></a>Azure 監視器記錄檔有哪些？

Azure 監視器中的記錄檔包含不同類型的資料組織成不同的每個類型的屬性集的記錄。 記錄檔可以包含數值的值，例如 Azure 監視器計量，但通常會包含文字資料，其中詳細描述。 它們進一步不同於計量資料，因為它們而有所不同，其結構中通常不是收集在定期間隔。 例如事件和追蹤遙測會儲存 Azure 監視器記錄檔，除了效能資料，讓它可以全部結合進行分析。

常見的記錄項目類型是偶發性收集的事件。 事件由應用程式或服務，而且通常包含足夠的資訊來提供完整的內容靠自己。 例如，事件可以表示已建立或修改特定資源、為了回應增加的流量而啟動新主機，或在應用程式中偵測到錯誤。

 資料的格式可能有所不同，應用程式可以使用其所需的結構建立自訂記錄。 甚至可以結合其他監視資料的趨勢和其他資料分析的記錄檔中儲存計量資料。


## <a name="what-can-you-do-with-azure-monitor-logs"></a>您可以使用 Azure 監視器記錄檔來做什麼？
下表列出您可以在 Azure 監視器中使用記錄的不同方式。


|  |  |
|:---|:---|
| 分析 | 使用[Log Analytics](../log-query/get-started-portal.md)在 Azure 入口網站中撰寫[記錄查詢](../log-query/log-query-overview.md)並以互動方式分析記錄資料使用功能強大的資料總管分析引擎。<br>使用[Application Insights analytics 主控台](../app/analytics.md)寫入記錄檔查詢，並以互動方式分析記錄資料從 Application Insights 在 Azure 入口網站。 |
| 視覺化 | 將查詢結果轉譯為資料表或圖表釘選[Azure 儀表板](../../azure-portal/azure-portal-dashboards.md)。<br>建立[活頁簿](../app/usage-workbooks.md)結合多個互動式的報表中的資料集。 <br>將查詢的結果匯出到 [Power BI](powerbi.md) 以使用不同的視覺效果，並與 Azure 外部的使用者共用。<br>若要查詢的結果匯出[Grafana](grafana-plugin.md)運用其儀表板，並結合其他資料來源。|
| 警示 | 設定[記錄警示規則](alerts-log.md)，在查詢結果符合特定結果時，傳送通知或採取[自動化動作](action-groups.md)。<br>設定[計量警示規則](alerts-metric-logs.md)擷取來作為計量的特定記錄檔資料記錄檔。 |
| 擷取 | 從命令列使用存取記錄檔查詢結果[Azure CLI](/azure/ext/log-analytics/monitor/log-analytics)。<br>從命令列使用存取記錄檔查詢結果[PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.operationalinsights)。<br>從自訂應用程式使用存取記錄檔查詢結果[REST API](https://dev.loganalytics.io/)。 |
| 匯出 | 建立擷取記錄檔資料，並將它複製到外部位置使用的工作流程[Logic Apps](~/articles/logic-apps/index.yml)。 |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>有何結構化的 Azure 監視器記錄檔中的資料？
Azure 監視器記錄檔所收集的資料會儲存在[Log Analytics 工作區](../platform/manage-access.md)。 您可以[建立多個工作區](manage-access.md#determine-the-number-of-workspaces-you-need)訂用帳戶來管理不同的記錄資料集。 每個工作區包含多個資料表，每個儲存來自特定來源的資料。 雖然所有資料表都共用[一些通用屬性](log-standard-properties.md)，各有一組唯一的屬性，根據它所儲存的資料類型。 新的工作區會有標準集的資料表，並將加入更多的資料表不同的監視解決方案和其他服務的工作區中寫入的。

從 Application Insights 的記錄資料相同的 Log Analytics 引擎做為工作區，但每個受監視的應用程式會分別儲存它。 每個應用程式有一組標準的資料表來保存資料，例如應用程式的要求、 例外狀況和頁面檢視。

使用 Log Analytics 工作區的資料，或是 Application Insights 應用程式，將會記錄查詢。 您可以使用[跨資源查詢](../log-query/cross-workspace-query.md)分析應用程式資料，以及其他記錄檔資料，或建立包含多個工作區或應用程式的查詢。

![工作區](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>記錄查詢
在 Azure 監視器記錄檔中的資料使用擷取[記錄檔查詢](../log-query/log-query-overview.md)撰寫[Kusto 查詢語言](../log-query/get-started-queries.md)，可讓您快速擷取、 彙總，並分析收集的資料。 使用[Log Analytics](../log-query/portals.md)撰寫及測試 Azure 入口網站中的記錄查詢。 它可讓您以互動方式使用結果，或將其釘選到儀表板來檢視其他視覺效果。

![Log Analytics](media/data-platform-logs/log-analytics.png)

開啟[Log Analytics 從 Application Insights](../app/analytics.md)來分析 Application Insights 資料。

![Application Insights 分析](media/data-platform-logs/app-insights-analytics.png)

您也可以使用擷取的記錄資料[Log Analytics API](https://dev.loganalytics.io/documentation/overview)並[Application Insights REST API](https://dev.applicationinsights.io/documentation/overview)。


## <a name="sources-of-azure-monitor-logs"></a>Azure 監視器記錄檔的來源
Azure 監視器可以在 Azure 中及內部部署資源的各種來源收集資料。 下表列出可從不同的資源將資料寫入至 Azure 監視器記錄檔的不同資料來源。 每個對所有必要的設定詳細資料的連結。

### <a name="azure-tenant-and-subscription"></a>Azure 租用戶和訂用帳戶

| 資料 | 描述 |
|:---|:---|
| Azure Active Directory 稽核記錄 | 透過針對每個目錄的診斷設定的設定。 請參閱[整合 Azure AD 與 Azure 監視器記錄檔的記錄檔](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)。  |
| 活動記錄 | 根據預設，分開儲存，並近乎即時警示可用於。 安裝要寫入至 Log Analytics 工作區的 Activity Log Analytics 解決方案。 請參閱[收集和分析 Log Analytics 中的 Azure 活動記錄](collect-activity-logs.md)。 |

### <a name="azure-resources"></a>Azure 資源

| 資料 | 描述 |
|:---|:---|
| 資源診斷 | 設定診斷設定寫入診斷資料，包括 Log Analytics 工作區的計量。 請參閱[Stream 至 Log Analytics 的 Azure 診斷記錄](diagnostic-logs-stream-log-store.md)。 |
| 監視解決方案 | 監視解決方案將資料寫入至其 Log Analytics 工作區所收集。 請參閱[在 Azure 中的管理解決方案的資料收集詳細資料](../insights/solutions-inventory.md)取得一份解決方案。 請參閱[監視 Azure 監視器中的解決方案](../insights/solutions.md)如需有關安裝和使用的解決方案。 |
| 度量 | Azure 監視器資源的平台計量傳送至 Log Analytics 工作區保留較長的記錄資料，並使用其他資料型別的執行複雜的分析[Kusto 查詢語言](/azure/kusto/query/)。 請參閱[Stream 至 Log Analytics 的 Azure 診斷記錄](diagnostic-logs-stream-log-store.md)。 |
| Azure 表格儲存體 | 收集的資料從 Azure 儲存體撰寫一些 Azure 資源的位置監視資料。 請參閱[具有 Log Analytics 之事件的 IIS 和 Azure 資料表儲存體使用 Azure blob 儲存體](azure-storage-iis-table.md)。 |

### <a name="virtual-machines"></a>虛擬機器

| 資料 | 描述 |
|:---|:---|
|  代理程式資料來源 | 從收集的資料來源[Windows](agent-windows.md)並[Linux](../learn/quick-collect-linux-computer.md)代理程式包含事件、 效能資料，以及自訂記錄檔。 請參閱[代理程式在 Azure 監視器中的資料來源](data-sources.md)取得一份資料來源和組態的詳細資訊。 |
| 監視解決方案 | 監視解決方案將資料寫入它們收集來自代理程式至其 Log Analytics 工作區。 請參閱[在 Azure 中的管理解決方案的資料收集詳細資料](../insights/solutions-inventory.md)取得一份解決方案。 請參閱[監視 Azure 監視器中的解決方案](../insights/solutions.md)如需有關安裝和使用的解決方案。 |
| System Center Operations Manager | 若要從內部部署代理程式的事件和效能資料收集到記錄檔的 Azure 監視器連接 Operations Manager 管理群組。 請參閱[Operations Manager 連接到 Log Analytics](om-agents.md)如需有關這項設定。 |


### <a name="applications"></a>[應用程式]

| 資料 | 描述 |
|:---|:---|
| 要求和例外狀況 | 應用程式的要求和例外狀況的詳細的資料位於_要求_， _pageViews_，並_例外狀況_資料表。 若要呼叫[外部元件](../app/asp-net-dependencies.md)處於_相依性_資料表。 |
| 使用情況和效能 | 應用程式的效能可用於_要求_， _browserTimings_並_performanceCounters_資料表。 資料[自訂度量](../app/api-custom-events-metrics.md#trackevent)處於_customMetrics_資料表。|
| 追蹤資料 | 所得[分散式追蹤](/app/distributed-tracing)會儲存在_追蹤_資料表。 |
| 可用性集合 | 從摘要資料[可用性測試](/app/monitor-web-app-availability)會儲存在_availabilityResults_資料表。 從這些測試的詳細的資料位於不同的儲存體，並在 Azure 入口網站中存取從 Application Insights。 |

### <a name="insights"></a>深入解析

| 資料 | 描述 |
|:---|:---|
| 適用於容器的 azure 監視器 | 清查和效能資料收集[適用於容器的 Azure 監視器](../insights/container-insights-overview.md)。 請參閱[容器資料收集詳細資料](../insights/container-insights-analyze.md#container-data-collection-details)取得一份資料表。 |
| 適用於 VM 的 Azure 監視器 | 對應和效能資料收集[Vm 的 Azure 監視器](../insights/vminsights-overview.md)。 請參閱[如何從 Azure 監視器的記錄檔查詢 Vm](../insights/vminsights-log-search.md)如需有關查詢此資料。 |

### <a name="custom"></a>自訂 

| 資料 | 描述 |
|:---|:---|
| REST API | 寫入資料到 Log Analytics 工作區中，從任何 REST 用戶端。 請參閱[記錄檔將資料傳送至 Azure 監視器與 「 HTTP 資料收集器 API](data-collector-api.md)如需詳細資訊。
| 邏輯應用程式 | 寫入 Log Analytics 工作區中的任何資料，從邏輯應用程式工作流程**Azure Log Analytics 資料收集器**動作。 |

### <a name="security"></a>安全性

| 資料 | 描述 |
|:---|:---|
| Azure 資訊安全中心 | [Azure 資訊安全中心](/azure/security-center/)儲存它，進行分析與其他記錄資料的 Log Analytics 工作區中收集的資料。 請參閱[Azure 資訊安全中心的資料收集](../../security-center/security-center-enable-data-collection.md)的工作區設定的詳細資訊。 |
| Azure Sentinel | [Azure 的 Sentinel](/azure/sentinel/)將從資料來源的資料儲存到 Log Analytics 工作區。 請參閱 [](/sentinel/connect-data-sources.md)  |


## <a name="next-steps"></a>後續步驟

- 深入了解[Azure 監視器的資料平台](data-platform.md)。
- 深入了解[Azure 監視器計量](data-platform-metrics.md)。
- 深入了解可用於 Azure 中不同資源的[監視資料](data-sources.md)。
