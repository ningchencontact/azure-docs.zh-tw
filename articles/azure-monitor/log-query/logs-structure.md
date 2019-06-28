---
title: Azure 監視器記錄檔的結構 |Microsoft Docs
description: 您需要記錄查詢來從 Azure 監視器擷取記錄資料。  本文描述新的記錄查詢在 Azure 監視器中的使用方式，並且提供在建立之前需要了解的概念。
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/16/2019
ms.author: bwren
ms.openlocfilehash: e243ebbc31f9e941678ac76a83738276995b5ba1
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297290"
---
# <a name="structure-of-azure-monitor-logs"></a>Azure 監視器記錄檔的結構
能夠快速取得您使用資料的深入[記錄檔查詢](log-query-overview.md)是強大的功能，Azure 監視器。 若要建立有效率且實用的查詢，您應該了解一些基本的概念，例如您想要的資料所在的位置和結構的方式。 本文提供開始所需的基本概念。

## <a name="overview"></a>概觀
在 Azure 監視器記錄檔中的資料會儲存在 Log Analytics 工作區或 Application Insights 應用程式。 兩者都由[Azure 資料總管](/azure/data-explorer/)這表示它們運用其功能強大的資料引擎和查詢語言。

工作區和應用程式中的資料會組織成資料表，其中每一個儲存不同類型的資料，並有它自己組唯一的屬性。 大部分[zdroje dat](../platform/data-sources.md) Application Insights 將會寫入至一組預先定義的 Application Insights 應用程式中的資料表時，會將寫入自己在 Log Analytics 工作區中的資料表。 記錄檔查詢時相當有彈性可讓您輕鬆地結合多個資料表的資料，並結合來自多個工作區中的資料表的資料，或撰寫結合的工作區和應用程式資料的查詢，甚至使用跨資源查詢。

下圖顯示寫入查詢範例中所使用的不同資料表的資料來源的範例。

![資料表](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Log Analytics 工作區
Azure 監視器記錄檔，除了 Application Insights 所收集的所有資料會都儲存在[Log Analytics 工作區](../platform/manage-access.md)。 您可以建立一或多個工作區，根據您特定的需求。 [資料來源](../platform/data-sources.md)活動記錄檔和診斷記錄，從 Azure 資源，例如虛擬機器，並從深入資訊和監視解決方案的資料上的代理程式會將資料寫入至您設定其上架的一部分的一個或多個工作區。 其他服務，例如[Azure 資訊安全中心](/azure/security-center/)並[Azure Sentinel](/azure/sentinel/)也使用 Log Analytics 工作區，來儲存其資料，因此可以使用記錄檔查詢，以及來自其他監視資料進行分析來源。

不同類型的資料會儲存在不同資料表中的工作區中，而且每個資料表有一組唯一的屬性。 一組標準的資料表時建立，並針對不同的資料來源、 解決方案和服務會加入新的資料表，因為它們只會新增到工作區上架。 您也可以建立使用自訂資料表[資料收集器 API](../platform/data-collector-api.md)。

您可以瀏覽工作區中的其結構描述中的資料表**結構描述**中 Log Analytics 工作區 索引標籤。

![工作區結構描述](media/scope/workspace-schema.png)

使用下列查詢，清單中的工作區的記錄數目的資料表收集到每個透過前一天。 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
請參閱文件的每個資料來源，如需詳細資訊，他們建立的資料表。 範例包括文章[代理程式資料來源](../platform/agent-data-sources.md)，[診斷記錄檔](../platform/diagnostic-logs-schema.md)，並[監視解決方案](../insights/solutions-inventory.md)。

### <a name="workspace-permissions"></a>工作區的權限
請參閱[工作區的權限和範圍](../platform/manage-access.md#workspace-permissions-and-scope)如需有關提供工作區中資料的存取權。 除了本身的工作區的存取權授與，您可以限制使用的個別資料表的存取權[資料表的層級 RBAC](../platform/manage-access.md#table-level-rbac)。

## <a name="application-insights-application"></a>Application Insights 應用程式
當您在 Application Insights 中建立應用程式時，則對應的應用程式會自動建立在 Azure 監視器記錄檔中。 不需要設定以收集資料，以及應用程式會自動為您撰寫監視資料，例如頁面檢視、 要求和例外狀況。

不同的 Log Analytics 工作區中，於 Application Insights 應用程式會有一組固定的資料表。 您無法設定其他資料來源，以寫入應用程式，因此可以不建立任何額外的資料表。 

| 資料表 | 描述 | 
|:---|:---|
| availabilityResults | 從可用性測試的摘要資料。 |
| browserTimings      | 用戶端效能，例如處理傳入的資料所花費的時間相關資料。 |
| customEvents        | 您的應用程式所建立的自訂事件。 |
| customMetrics       | 您的應用程式所建立的自訂計量。 |
| 相依性        | 從應用程式的外部元件呼叫。 |
| 例外狀況          | 應用程式執行階段擲回的例外狀況。 |
| pageViews           | 每個網站的相關資料檢視與瀏覽器資訊。 |
| performanceCounters | 從支援的應用程式的計算資源的效能度量。 |
| requests            | 每個應用程式要求的詳細資料。  |
| traces              | 分散式追蹤結果。 |

您可以檢視每個資料表中的結構描述**結構描述**Log Analytics 中的索引標籤上，應用程式。

![應用程式結構描述](media/scope/application-schema.png)

## <a name="standard-properties"></a>標準屬性
雖然 Azure 監視器記錄檔中的每個資料表都有自己的結構描述，但有共用的所有資料表的標準屬性。 請參閱[在 Azure 監視器記錄檔中的標準屬性](../platform/log-standard-properties.md)的每個詳細資料。

| Log Analytics 工作區 | Application Insights 應用程式 | 描述 |
|:---|:---|:---|
| TimeGenerated | timestamp  | 建立記錄的日期和時間。 |
| 類型          | itemType   | 從擷取的記錄資料表的名稱。 |
| _ResourceId   |            | 相關聯的資源記錄的唯一識別碼。 |
| _IsBillable   |            | 指定是否可計費的內嵌的資料。 |
| _BilledSize   |            | 指定的大小，以位元組為單位的資料，其收費。 |

## <a name="next-steps"></a>後續步驟
- 了解如何使用[來建立和編輯的 Log Analytics 記錄搜尋](../log-query/portals.md)。
- 請參閱使用新的查詢語言[撰寫查詢的教學課程](../log-query/get-started-queries.md)。
