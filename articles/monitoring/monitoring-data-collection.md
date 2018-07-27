---
title: 在 Azure 中收集監視資料 | Microsoft Docs
description: 概略說明從 Azure 中的應用程式和服務收集的監視資料，以及用來進行分析的工具。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: bwren
ms.openlocfilehash: d3ebd512f8244de74c009ac8a2936ed8e817dad9
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38992235"
---
# <a name="collecting-monitoring-data-in-azure"></a>在 Azure 中收集監視資料
本文概略說明從 Azure 中的應用程式和服務收集的監視資料，以及用來進行分析的工具。 

## <a name="types-of-monitoring-data"></a>監視資料的類型
所有監視資料都會屬於其中一種基本類型：計量或記錄。 每個類型都有不同的特性，而且最適合特定案例，如下所述。

### <a name="metrics"></a>度量
計量為數值，可描述系統在特定時間點的某個方面。 它們包含不同資料，包括值本身、值的收集時間、值所代表的度量類型，以及與此值相關聯的特定資源。 不論值是否變更，系統都會定期收集計量。 例如，您可能會每分鐘從虛擬機器收集處理器使用率，或每隔 10 分鐘收集已登入您應用程式的使用者數目。

計量屬於輕量型，而且能夠支援近乎即時的案例。 它們特別適合用於警示，因為計量可頻繁地進行取樣，而且可使用相對較簡單的邏輯快速地引發警示。 例如，您可以在計量超出臨界值時引發警示，或者在兩個計量值之間的差異達到特定值時引發警示。

個別計量通常自行提供很少的見解。 它們提供單一值，而沒有任何可與簡單閾值比擬的內容。 個別計量在下列情況很實用：與其他計量結合時可識別模式和趨勢，或與記錄結合時可提供以特定值為主的內容。 例如，您的應用程式在特定時間的特定使用者數目可能難以告訴您應用程式的健康狀態。 以相同計量的多個值所表示的使用者數突然下降，可能表示有問題。 由應用程式擲回並以不同計量表示的過度例外狀況，可以識別導致卸除的應用程式問題。 由應用程式建立並可識別應用程式特定元件失敗的事件，可協助您識別根本原因。

以記錄為基礎的警示不會如同以計量為基礎的警示般回應，但它們可以包含更複雜的邏輯。 您可以根據任何對多個來源的資料執行複雜分析的查詢結果，建立警示。

### <a name="logs"></a>記錄檔
記錄包含不同類型的資料，而資料會針對每個類型組織成不同的屬性集。 記錄可以包含數值 (例如計量)，但通常包含具有詳細描述的文字資料。 其在結構方面進一步與計量不同，而且通常不會定期收集。

事件是常見的記錄項目類型。 事件偶爾會在應用程式或服務進行建立時收集，而且通常包含足夠的資訊來自行提供完整的內容。  比方說，事件可能表示已建立或修改特定資源、為了回應增加的流量而啟動新主機，或在應用程式中偵測到錯誤。

記錄特別適合用於結合各種來源的資料，以便進行一段時間的複雜分析和趨勢分析。 資料的格式可能有所不同，因此應用程式可以使用其所需的結構建立自訂記錄。 計量甚至可以在記錄中進行複寫，將它與其他監視資料結合，以便進行趨勢分析和其他資料分析。


## <a name="monitoring-tools-in-azure"></a>Azure 中的監視工具
使用下列各節所述的多個來源，在 Azure 中收集和分析監視資料。

### <a name="azure-metrics"></a>Azure 計量
來自 Azure 資源和應用程式的計量會收集到 Azure計量中。 計量資料會整合到 Azure 入口網站中特定 Azure 資源 (例如虛擬機器) 的頁面，其中包含所選機器的 CPU 和網路使用率等這類計量的圖表。 它也可透過[計量瀏覽器](../monitoring-and-diagnostics/monitoring-metric-charts.md)進行分析，其可將一段時間的多個計量值繪製成圖表。  您可以互動方式檢視圖表，或將其釘選到儀表板，利用其他視覺效果進行檢視。 您也可以利用 [Azure 監視 REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md) 擷取計量。

您可以在 [Azure 中監視資料的來源](monitoring-data-sources.md)，取得各種 Azure 資源所收集的計量資料詳細資訊。 

![計量瀏覽器](media/monitoring-data-collection/metrics-explorer.png)


### <a name="azure-activity-log"></a>Azure 活動記錄檔 
[Azure 活動記錄](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)會儲存有關 Azure 服務的組態和健康狀態。 您可以使用 [活動記錄總管] 在 Azure 入口網站中檢視這些記錄，但它們通常[會複製到 Log Analytics](../log-analytics/log-analytics-activity.md)，以便與其他記錄資料一起分析。

您可以使用 [活動記錄總管] 來檢視篩選成符合特定準則的活動記錄。  在 Azure 入口網站中，大部分資源在其功能表中也會有 [活動記錄] 選項，該選項可顯示針對該資源篩選的 [活動記錄總管]。 您也可以利用 [Azure 監視 REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md) 擷取活動記錄。

![活動記錄總管](media/monitoring-data-collection/activity-log-explorer.png)


### <a name="log-analytics"></a>Log Analytics
Log Analytics 提供通用資料平台，以便在 Azure 中進行管理。 這是在 Azure 中用於儲存和分析記錄的主要服務，該服務可從各種來源收集資料，包括虛擬機器上的代理程式、管理解決方案，以及不同的 Azure 資源。 可以將其他來源 (包括計量和活動記錄) 的資料複製到 Log Analytics 中，以便建立監視資料的完整中央存放庫。

Log Analytics 具有豐富的查詢語言，可用來分析它所收集的資料。  您可以使用[記錄搜尋入口網站](../log-analytics/log-analytics-log-search-portals.md)，以互動方式撰寫和測試查詢及分析其結果。 您也可以[建立檢視](../log-analytics/log-analytics-view-designer.md)，以將記錄搜尋結果視覺化，或將查詢的結果直接貼到 Azure 儀表板。  管理解決方案包含 Log Analytics 中的記錄搜尋和檢視，可供分析它們所收集的資料。 Application Insights 等其他服務會在 Log Analytics 中儲存資料，並提供額外的工具進行分析。  

![記錄檔](media/monitoring-data-collection/logs.png)

### <a name="application-insights"></a>Application Insights
Application Insights 可針對各種平台上安裝的 Web 應用程式收集遙測。 它會將其資料儲存在 Azure 計量和 Log Analytics 中，並且在現有工具之上，提供一組廣泛的豐富工具來分析此資料，以及將其資料視覺化。 這可讓您利用一組常用的服務，例如警示、記錄搜尋，以及您用於其他監視的儀表板。


![應用程式情資](media/monitoring-data-collection/app-insights.png)

### <a name="service-map"></a>服務對應
服務對應會使用其程序和相依性，提供虛擬機器的視覺表示法。 它會在 Log Analytics 中儲存大部分的這項資料，以便您將它與其他管理資料一起分析。 服務對應主控台也會從 Log Analytics 擷取資料，以便在所分析的虛擬機器內容中呈現。

![服務對應](media/monitoring-data-collection/service-map.png)


## <a name="transferring-monitoring-data"></a>傳輸監視資料

### <a name="metrics-to-logs"></a>計量至記錄
計量也可以複寫到 Log Analytics 中，以使用其豐富的查詢語言來執行其他資料類型的複雜分析。 您也可以將記錄資料保留得比計量久，這可讓您執行一段時間的趨勢分析。 當計量或任何其他效能資料儲存在 Log Analytics 時，該資料可當作記錄。 使用記錄進行其他資料的趨勢分析時，使用計量來支援近乎即時的分析和警示。

您可以在[收集 Azure 服務記錄和計量以便使用於 Log Analytics](../log-analytics/log-analytics-azure-storage.md)，取得從 Azure 資源收集計量的指引。 在[使用 Log Analytics 設定 Azure PaaS 資源計量的集合](../log-analytics/log-analytics-collect-azurepass-posh.md)，可取得從 Azure PaaS 資源收集資源計量的指引。

### <a name="logs-to-metrics"></a>計量至記錄
如上所述，計量比記錄更有回應，可讓您以較低成本建立較低延遲的警示。 Log Analytics 會收集大量數值資料，這些資料會適合於計量，但不會儲存在 Azure 計量中。 常見範例是從代理程式和管理解決方案收集的效能資料。 您可將其中一些值複製到 Azure 計量中，以便用於警示以及透過計量瀏覽器進行分析。

在[適用於記錄的更快速計量警示現在處於有限的公開預覽狀態](https://azure.microsoft.com/blog/faster-metric-alerts-for-logs-now-in-limited-public-preview/)可取得這項功能的說明。 在[新計量警示支援的計量和建立方法](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md)可取得值清單支援。

### <a name="event-hub"></a>事件中樞
除了在 Azure 中使用工具來分析監視資料，您也可以將監視資料轉寄到外部工具，例如安全性資訊和事件管理 (SIEM) 產品。 這通常使用 [Azure 事件中樞](https://docs.microsoft.com/azure/event-hubs/)進行。 

您可以在[將 Azure 監視資料串流至事件中樞以供外部工具取用](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)取得各種監視資料的指引。

## <a name="next-steps"></a>後續步驟

- 深入了解可用於 Azure 中不同資源的[監視資料](monitoring-data-sources.md)。 