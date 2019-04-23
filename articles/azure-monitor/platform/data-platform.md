---
title: Azure 監視器資料平台 |Microsoft Docs
description: Azure 監視器所收集的監視資料，會分為輕量型且能夠支援近乎即時之案例的計量，以及用於進階分析的記錄。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 8883c1e7f2874e1e2e61b8eca122f2ec294c7849
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790921"
---
# <a name="azure-monitor-data-platform"></a>Azure 監視器資料平台

啟用跨現今複雜的計算環境執行仰賴雲端和內部部署服務的分散式應用程式的可檢視性需要的每個圖層與分散式系統的每個元件的操作資料的集合。 您必須要能夠執行這項資料的深入解析，並合併成單一的半透明效果，具有不同的檢視方塊，以支援您組織中的專案關係人的窗格。

[Azure 監視器](../overview.md)會收集並彙總來自各種來源通用的資料平台，其用它來分析、 視覺化和警示資料。 它提供一致的體驗，在多個來源，這可讓您深入了解跨所有受監視資源和甚至是資料從其他服務，將其資料儲存在 Azure 監視器中的資料之上。


![Azure 監視器概觀](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Azure 監視器中的可檢視性資料
計量、 記錄和分散式的追蹤通常稱為可檢視性的三個要件。 這些是不同種類的監視工具必須收集並分析提供足夠的可檢視性的受監視系統的資料。 可檢視性可藉由相互關聯來自多個要件的資料，並在整個集合的受監視的資源彙總資料。 因為 Azure 監視器會儲存在一起的多個來源的資料，資料可以相互關聯，並且分析使用一組常用的工具。 它也會將相互關聯資料跨多個 Azure 訂用帳戶和租用戶，除了裝載其他服務的資料。

Azure 資源產生大量的監視資料。 Azure 監視器將此資料，以及監視從其他來源的資料合併度量或記錄檔的平台。 每個適用於特定的監視案例，以及每個 Azure 監視器中支援不同的功能。 例如資料分析、 視覺效果，或警示的功能要求您了解這些差異，以便您可以實作您所需的案例中最有效率且符合成本效益的方式。 這類的 Azure 監視器中的深入解析[Application Insights](../app/app-insights-overview.md)或是[Vm 的 Azure 監視器](../insights/vminsights-overview.md)有分析工具，可讓您專注於特定的監視案例中，而不需要了解兩種資料類型之間的差異。 


### <a name="metrics"></a>度量
[計量](data-platform-metrics.md)為數值，可描述系統在特定時間點的某個方面。 它們在定期間隔會收集，並具有時間戳記、 名稱、 值和一或多個定義的標籤。 計量可以使用各種不同的演算法，相較於其他度量，以及經過一段時間的趨勢分析彙總。 

Azure 監視器中的度量資訊會儲存在時間序列資料庫適合用於分析時間戳記資料。 這可讓計量，特別適合用於警示及快速偵測問題。 它們可以告訴您如何執行您的系統，但通常需要結合記錄檔以判定問題的根本原因。

計量可供使用 Azure 入口網站中的互動式分析[計量瀏覽器](../app/metrics-explorer.md)。 它們可以加入[Azure 儀表板](../learn/tutorial-app-dashboards.md)與其他資料組合中的視覺效果，用於即時[警示](alerts-metric.md)。

深入了解如何將其來源中的資料包括 Azure 監視器計量[Azure 監視器計量](data-platform-metrics.md)。

### <a name="logs"></a>記錄
[記錄檔](data-platform-logs.md)是系統內發生的事件。 它們可以包含不同類型的資料，可能結構化或自由格式文字加上時間戳記。 它們可能會建立偶而會在環境中的事件都會產生記錄項目，以及負載過重的系統通常會產生詳細的記錄磁碟區。

Azure 監視器中的記錄檔會儲存在 Log Analytics 工作區為基礎[Azure 資料總管](/azure/data-explorer/)其中提供功能強大的分析引擎，以及[豐富的查詢語言](/azure/kusto/query/)。 記錄檔通常會提供足夠的資訊，以提供所識別之問題的完整內容，然後而言非常有用來識別問題的根本原因。

> [!NOTE]
> 請務必區分 Azure 監視器記錄檔和記錄資料，在 Azure 中的來源。 比方說，在 Azure 中的訂用帳戶層級事件會寫入[活動記錄檔](activity-logs-overview.md)，您可以從 Azure 監視器功能表檢視。 最多資源會將操作資訊寫入[診斷記錄](diagnostic-logs-overview.md)，您可以將它轉送至不同的位置。 Azure 監視器記錄檔會收集活動記錄檔和診斷記錄連同其他監視資料，以提供深入分析您的資源的整個集合的記錄檔資料平台。


 您可以使用[記錄查詢](../log-query/log-query-overview.md)互動[Log Analytics](../log-query/portals.md)在 Azure 入口網站中或新增至結果[Azure 儀表板](../learn/tutorial-app-dashboards.md)結合的視覺效果其他資料。 您也可以建立[的記錄警示](alerts-log.md)將觸發警示的依據排程查詢的結果。

深入了解 Azure 監視器記錄檔，包括其來源中的資料[登入 Azure 監視器](data-platform-logs.md)。

### <a name="distributed-traces"></a>分散式的追蹤
追蹤是一系列的遵循透過分散式系統的使用者要求的相關事件。 它們可用來判斷應用程式程式碼的行為和不同的交易效能。 雖然分散式系統的個別元件會經常建立記錄檔，追蹤會測量元件在整個集合的作業與應用程式的效能。

Azure 監視器中的分散式的追蹤已啟用[Application Insights SDK](../app/distributed-tracing.md)，和追蹤資料會儲存與 Application Insights 收集的其他應用程式記錄檔資料。 這可讓您能夠使用相同的分析工具，為其他記錄檔資料，包括記錄檔查詢、 儀表板和警示。

深入了解分散式追蹤在[什麼是分散式追蹤？](../app/distributed-tracing.md)。


## <a name="compare-azure-monitor-metrics-and-logs"></a>比較 Azure 監視器計量和記錄

下表比較計量與 Azure 監視器中的記錄。

| 屬性  | 度量 | 記錄 |
|:---|:---|:---|
| 優點 | 輕量且能夠幾近即時的案例，例如警示。 適用於快速偵測問題。 | 利用豐富的分析查詢語言。 適用於深入分析及找出根本原因。 |
| 資料 | 僅限數字值 | 文字或數值資料 |
| Structure | 屬性包括取樣時間、 受監視的資源、 一個數字值的標準集合。 某些度量資訊會包含進一步定義多個維度。 | 唯一一組屬性視記錄檔類型而定。 |
| 集合 | 收集在定期間隔。 | 可能偶而會收集事件觸發程序建立的記錄。 |
| 在 Azure 入口網站中檢視 | 計量瀏覽器 | Log Analytics |
| 包含資料來源 | 從 Azure 資源收集的平台計量。<br>Application insights 監視應用程式。<br>應用程式或 API 所定義的自訂。 | 應用程式和診斷記錄檔。<br>監視解決方案。<br>代理程式和 VM 擴充功能。<br>應用程式的要求和例外狀況。<br>Azure 資訊安全中心。<br>資料收集器 API。 |

## <a name="collect-monitoring-data"></a>收集監視資料
不同[Azure 監視器的資料來源的](data-sources.md)會寫入至 Log Analytics 工作區 （記錄檔） 或 Azure 監視器計量資料庫 （度量），或兩者。 某些來源會直接寫入這些資料存放區中，而其他人可能會寫入到另一個位置，例如 Azure 儲存體需要執行一些設定，填入記錄或度量資訊。 

請參閱[Azure 監視器中的計量](data-platform-metrics.md)並[登入 Azure 監視器](data-platform-logs.md)填入每個類型的不同資料來源的清單。


## <a name="stream-data-to-external-systems"></a>將資料串流處理到外部系統
除了使用 Azure 中的工具來分析監視資料，您可能也有將監視資料轉送到外部工具的需求，例如安全性資訊和事件管理 (SIEM) 產品。 此轉送通常是透過 [Azure 事件中樞](/azure/event-hubs/)，直接從受監視的資源來完成。 若要將資料傳送至事件中樞直接，雖然您可以使用另一個處理序，例如邏輯應用程式來擷取所需的資料，可以設定某些來源。 請參閱[Stream Azure 監視資料至事件中樞供外部工具](stream-monitoring-data-event-hubs.md)如需詳細資訊。



## <a name="next-steps"></a>後續步驟

- 深入了解[Azure 監視器計量](data-platform-metrics.md)。
- 深入了解[登入 Azure 監視器](data-platform-logs.md)。
- 深入了解可用於 Azure 中不同資源的[監視資料](data-sources.md)。
