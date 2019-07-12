---
title: 使用 Azure 資料總管 （預覽） 的 Azure 監視器中的查詢資料
description: 本主題中，查詢 Azure 監視器中的資料使用 Application Insights 和 Log Analytics 中建立 Azure 資料總管 proxy 的交叉乘積查詢
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: f363e59e6faa6b115eb40a2a5d35432f02299d52
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800207"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>使用 Azure 資料總管 （預覽） 的 Azure 監視器中的查詢資料

Azure 資料總管 proxy 叢集 (ADX Proxy) 是一個實體，可讓您執行 Azure [資料總管] 中，之間的交叉乘積查詢[Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)，並[Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs)中[Azure 監視器](/azure/azure-monitor/)服務。 您可以將 Azure 監視 Log Analytics 工作區或 Application Insights 應用程式，做為 proxy 的叢集。 然後，您可以查詢 proxy 叢集使用 Azure Data Explorer 工具，並在跨叢集查詢中參考它。 本文說明如何連線到 proxy 的叢集、 將 proxy 叢集新增到 Azure 資料總管 Web UI，以及針對您的 AI 應用程式或 LA 工作區執行查詢，從 Azure 資料總管。

Azure 資料總管 proxy 流程： 

![ADX proxy 流程](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>必要條件

> [!NOTE]
> ADX Proxy 為預覽模式。 若要啟用此功能，請連絡[ADXProxy](mailto:adxproxy@microsoft.com)小組。

## <a name="connect-to-the-proxy"></a>連線到 proxy

1. 請確認您的 Azure 資料總管原生叢集 (例如*協助*叢集) 連線到您 Log Analytics 或 Application Insights 的叢集之前，會出現在左側功能表中。

    ![ADX 原生叢集](media/adx-proxy/web-ui-help-cluster.png)

1. 在 Azure Data 總管使用者介面 (https://dataexplorer.azure.com/clusters) ，選取**新增叢集**。

1. 在 **新增叢集**視窗：

    * 將 URL 新增到 LA 或 AI 叢集。 例如：`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * 選取 [新增]  。

    ![新增叢集](media/adx-proxy/add-cluster.png)

    如果您將連接加入多個 proxy 叢集時，為每個不同的名稱。 否則它們將所有具有相同名稱的左窗格中。

1. 建立連線之後，LA 或 AI 叢集會在原生 ADX 叢集中內含的左窗格中。 

    ![Log Analytics 和 Azure 資料總管叢集](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>執行查詢

您可以使用 Kusto 總管 中，ADX web 總管 中，Jupyter Kqlmagic 或 REST API 來查詢 proxy 叢集。 

> [!TIP]
> * 資料庫名稱必須包含指定 proxy 叢集中的資源相同的名稱。 名稱會區分大小寫。
> * 在跨叢集查詢中，請確定[的應用程式和工作區命名](#application-insights-app-and-log-analytics-workspace-names)正確無誤。

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>針對原生 Azure 資料總管叢集查詢 

在您的 Azure 資料總管叢集上執行查詢 (例如*StormEvents*資料表中*協助*叢集)。 當執行查詢時，請確認已選取左窗格中的原生 Azure 資料總管叢集。

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![查詢 StormEvents 資料表](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>查詢對 LA 或 AI 叢集

當您在 LA 或所有叢集上執行查詢時，確認已選取左窗格中的 LA 或 AI 叢集。 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![查詢 LA 工作區](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>查詢從 ADX proxy LA 或 AI 叢集  

當您從 proxy，LA 或 AI 叢集上執行查詢時，確認 ADX 原生叢集已選取左窗格中。 下列範例示範使用原生 ADX 叢集 LA 工作區的查詢

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![查詢從 Azure 資料總管 proxy](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>跨越 ADX proxy LA 或 AI 叢集和 ADX 叢集的查詢 

當您執行從 proxy 的跨叢集的查詢時，確認您 ADX 原生叢集已選取左窗格中。 下列範例將示範如何結合 ADX 叢集資料表 (使用`union`) 與 LA 工作區。

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![從 Azure 資料總管 proxy 跨查詢](media/adx-proxy/cross-query-adx-proxy.png)

使用[`join`運算子](/azure/kusto/query/joinoperator)，而不是等位，可能需要提示，以執行 Azure 資料總管原生叢集上 （和 proxy 上找不到）。 

## <a name="additional-syntax-examples"></a>其他的語法範例

呼叫的 Application Insights (AI) 或 Log Analytics (LA) 的叢集時，有使用下列語法選項：

|語法描述  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| 僅包含已定義的資源在此訂用帳戶在叢集內的資料庫 (**建議用於跨叢集查詢**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| 包含此訂用帳戶中所有應用程式/工作區的叢集    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|叢集包含訂用帳戶中所有應用程式/工作區，而且此資源群組的成員    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|僅包含已定義的資源在此訂用帳戶中的叢集      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

### <a name="application-insights-app-and-log-analytics-workspace-names"></a>Application Insights 應用程式和 Log Analytics 工作區名稱

* 如果名稱包含特殊字元，它們是由 proxy 叢集名稱中編碼的 URL 來取代。 
* 如果名稱包含不符合的字元[KQL 識別項名稱規則](/azure/kusto/query/schema-entities/entity-names)，系統會取代虛線 **-** 字元。

## <a name="next-steps"></a>後續步驟

[撰寫查詢](write-queries.md)