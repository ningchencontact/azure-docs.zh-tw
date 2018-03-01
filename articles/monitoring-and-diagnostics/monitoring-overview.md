---
title: "監視 Azure 應用程式和資源 | Microsoft Docs"
description: "構成您 Azure 服務和應用程式之完整監視策略的各種不同 Microsoft 服務和功能的概觀。"
author: robb
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: robb,bwren
ms.openlocfilehash: 505e92b5fc63f570bc4d0f8899ae977b93850356
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="monitoring-azure-applications-and-resources"></a>監視 Azure 應用程式和資源

監視係指收集和分析資料來判斷您商務應用程式及其相依資源之效能、健康情況和可用性的行為。 有效的監視策略將可協助您了解應用程式不同元件的詳細作業，並主動通知您重大問題，讓您能夠在這些問題變成問題之前便將其解決，以提升正常運作時間。

Azure 包含多個服務，這些服務個別在監視領域中執行特定角色或工作，並且一起提供一個全方位的解決方案，可收集及分析來自應用程式和支援它們之底層 Azure 資源的遙測資料，並根據這些資料採取行動。  它們也可以用來監視重要的內部部署資源，以提供混合式監視環境。   了解可用的工具和資料是開發完整應用程式監視策略的第一步。 

下圖顯示合作提供 Azure 資源監視功能之各種不同元件的概念檢視。  以下各節將會說明這當中的每一個元件，並提供詳細技術資訊的連結。

![監視概觀](media/monitoring-overview/overview.png)

## <a name="basic-monitoring"></a>基本監視
基本監視提供監視所有 Azure 資源所需的基本功能。  這些服務只需最基本的設定，並且會收集供進階監視服務運用的核心遙測資料。    

### <a name="azure-monitor"></a>Azure 監視器
[Azure 監視器](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md)會透過允許收集[計量](../monitoring-and-diagnostics/monitoring-overview-metrics.md)、[活動記錄](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)及[診斷記錄](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)，來啟用 Azure 服務的基本監視功能。  例如，活動記錄將會告訴您新資源的建立或修改時間。  系統會提供計量，這些計量可提供不同資源 (甚至是虛擬機器內的作業系統) 的效能統計資料。  您可以使用 Azure 入口網站中的其中一個總管來檢視此資料、將它傳送給 Log Analytics 以進行趨勢和詳細分析，或是建立警示規則來主動通知您重大問題。

### <a name="service-health"></a>服務健康情況
您應用程式的健康情況取決於其相依的 Azure 服務。  [Azure 服務健康狀態](../service-health/service-health-overview.md)可識別任何可能影響您應用程式的 Azure 服務問題，並可協助您為任何排程維護進行規劃。

### <a name="azure-advisor"></a>Azure 建議程式
[Azure Advisor](../advisor/advisor-overview.md) 會持續監視您的資源設定和使用情況遙測資料，以根據最佳做法為您提供個人化建議。  依照這些建議操作將可協助您提升支援應用程式之資源的效能、安全性及可用性。


## <a name="premium-monitoring-services"></a>進階監視服務
下列 Azure 服務提供可收集和分析監視資料的豐富功能。  它們以基本監視功能為基礎，並且運用 Azure 中的通用功能，可透過所收集的資料提供強大的分析，為您提供應用程式和基礎結構的獨特深入解析。  它們可以在針對不同對象的特定案例內容中呈現資料。

### <a name="application-insights"></a>Application Insights
[Application Insights](http://azure.microsoft.com/documentation/services/application-insights) 可讓您監視應用程式的可用性、效能及使用情況，不論該應用程式是裝載在雲端還是內部部署環境。  透過檢測您的應用程式以與 Application Insights 搭配運作，您將可以獲得可讓您快速識別和診斷錯誤的深入解析，而不需要等待使用者回報錯誤。 有了所收集的資訊之後，您便可以針對應用程式的維護和改善，進行資訊充分的選擇。  除了有可與所收集資料互動的廣泛工具之外，Application Insights 還將其資料儲存在通用存放庫中來利用共用功能，例如警示、儀表板及運用 Log Analytics 查詢語言的深入分析。

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) 在 Azure 監視功能中扮演一個中心角色，它會從各種資源將資料收集到單一存放庫中，以便以強大的查詢語言進行分析。  Application Insights 和「Azure 資訊安全中心」會將其資料儲存在 Log Analytics 資料存放區中並利用其分析引擎。  這會與從「Azure 監視器」、管理解決方案及安裝在雲端或內部部署環境中虛擬機器上的代理程式所收集到的資料結合，讓您能夠完整了解整個環境。 


### <a name="service-map"></a>服務對應
[服務對應](../operations-management-suite/operations-management-suite-service-map.md)可透過以虛擬機器的各種不同程序，以及與其他電腦和外部程序的相依性，來分析虛擬機器，為您提供 IaaS 環境的深入解析。  它會將事件、效能資料及管理解決方案整合在 Log Analytics 中，讓您能夠在每部電腦的內容及它與環境其餘部分的關聯中檢視此資料。  「服務對應」與 [Application Insights 中的應用程式對應](../application-insights/app-insights-app-map.md)類似，但著重在支援您應用程式的基礎結構元件。

### <a name="network-watcher"></a>網路監看員
[網路監看員](../network-watcher/network-watcher-monitoring-overview.md)可針對 Azure 中的各種不同網路案例，提供案例型監視和診斷。  它會將資料儲存在 Azure 計量和診斷中，以供進一步分析及與下列網路監視解決方案搭配運作，來監視您網路的各個方面：
* [網路效能監控 (NPM)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/) - 雲端式網路監視解決方案，可監視跨公用雲端、資料中心和內部部署環境的連線
* [ExpressRoute 監視](https://azure.microsoft.com/en-in/blog/monitoring-of-azure-expressroute-in-preview/) - 可透過 ExpressRoute 線路監視端對端連線能力和效能的 NPM 功能。
* 流量分析 - 雲端式解決方案，可顯示雲端網路上的使用者和應用程式活動。
* [DNS 分析](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-dns) - 根據您的 DNS 伺服器來提供安全性、效能和作業相關的深入資訊。

### <a name="management-solutions"></a>管理解決方案
[管理解決方案](../log-analytics/log-analytics-add-solutions.md)是幾組封裝的邏輯，可提供特定應用程式或服務的深入解析。  它們倚賴 Log Analytics 來儲存和分析所收集的監視資料。  您可以從 Microsoft 以及為各種 Azure 和協力廠商服務提供監視功能的合作夥伴取得管理解決方案。 監視解決方案的範例包括可協助您檢視和管理容器主機的[容器監視](../log-analytics/log-analytics-containers.md)，以及可為 SQL Azure 資料庫收集效能計量並以視覺化方式呈現的 [Azure SQL 分析](../log-analytics/log-analytics-azure-sql.md)。


## <a name="shared-functionality"></a>共用功能
下列 Azure 工具會為進階監視服務提供重要功能。  這些工具由多個服務共用，可讓您利用跨多個服務的通用功能和設定。

### <a name="alerts"></a>警示
[Azure 警示](../monitoring-and-diagnostics/monitoring-overview-alerts.md)會主動通知您重大情況，並可能採取矯正措施。  警示規則可以利用來自各種來源 (包括計量和記錄) 的資料。 它們會使用[動作群組](../monitoring-and-diagnostics/monitoring-action-groups.md)，其中包含幾組獨特的收件人及回應警示的動作。  您可以根據需求，使用 Webhook 讓警示啟動外部動作，並與您的 ITSM 工具整合。

### <a name="dashboards"></a>儀表板
[Azure 儀表板](../azure-portal/azure-portal-dashboards.md)可讓您將不同類型的資料結合至 Azure 入口網站的單一窗格中，然後與其他 Azure 使用者共用。  例如，您可以建立一個儀表板，將顯示計量圖表、活動記錄表、來自 Application Insights 的使用情況圖表及 Log Analytics 中之記錄搜尋輸出的圖格都結合在一起。

您也可以將 Log Analytics 資料匯出至 [Power BI](https://docs.microsoft.com/power-bi/)，以利用額外的視覺效果，並且也將資料提供給您組織內外的其他使用者。

### <a name="metrics-explorer"></a>計量瀏覽器
[計量](../monitoring-and-diagnostics/monitoring-overview-metrics.md)是 Azure 資源所產生的數值，可協助您了解資源的作業與效能。 您可以將計量傳送給 Log Analytics 以與其他來源的資料一起分析。



### <a name="activity-logs"></a>活動記錄檔
[活動記錄](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)可提供 Azure 資源作業的相關資料。  這包括資源的設定變更、服務健康情況事件、如何更有效運用資源的建議等資訊，以及與自動調整規模作業相關的資訊。  您可以在 Azure 入口網站中特定資源的頁面上檢視該資源的記錄，或是從「活動記錄總管」中的多個資源檢視記錄。  您也可以將活動記錄傳送給 Log Analytics，以便與管理解決方案、虛擬機器上的代理程式及其他資源所收集的資料一起分析。


## <a name="example-scenarios"></a>範例案例
以下是一些概要範例，說明如何針對不同的案例，利用 Azure 中的不同監視工具。

### <a name="monitoring-a-web-application"></a>監視 Web 應用程式
請思考一下一個使用「應用程式服務」、「Azure 儲存體」及 SQL 資料庫在 Azure 中部署的 Web 應用程式。  您可以從在 Azure 入口網站中這每個個別資源的頁面上存取其[計量](../monitoring-and-diagnostics/monitoring-overview-metrics.md)和[活動記錄](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)開始著手。  除了識別所有設定變更之外，這還會包括對應用程式發出的要求數目和平均回應時間等重要資訊。

您可以接著移至入口網站中的 [監視]，來一起檢視不同資源的計量和記錄。  在決定計量的標準參數時，您會[建立警示規則](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)來主動通知您狀況，例如當平均回應時間的增加超出閾值時。  為了快速檢視您應用程式的每日效能，您會建立 Azure 儀表板來顯示代表重要 KPI 之計量的圖表。

若要執行更深入的應用程式監視，您將需要[針對 Application Insights 進行設定](../application-insights/quick-monitor-portal.md)。  您現在可以收集可提供進一步應用程式作業與效能深入解析的額外資料。  Application Insights 會偵測您應用程式元件間的底層關聯性，以便透過[應用程式對應](../application-insights/app-insights-app-map.md)以視覺方式呈現這些關聯性，並搭配[端對端追蹤](../application-insights/app-insights-transaction-diagnostics.md)來診斷出問題發生所在的確切元件、相依性或例外狀況。  您會建立[可用性測試](../application-insights/app-insights-monitor-web-app-availability.md)，以主動從不同的區域測試您的應用程式。  為了協助您的開發人員，您會[啟用分析工具](../application-insights/enable-profiler-compute.md)，以便深入追蹤要求和任何例外狀況，直到特定一行程式碼為止。  

為了更深入檢視您應用程式中使用的服務，您會新增 [SQL 分析解決方案](../log-analytics/log-analytics-azure-sql.md)，以將額外資料收集到 Log Analytics 中。 在一段時間之後，您會決定針對網站效能降到低於閾值的期間，進行根本原因調查。  您會使用 Log Analytics 來撰寫查詢，將 Application Insights 所收集的使用情況和效能資料與支援您應用程式之所有 Azure 資源的設定和效能資料相互關聯。


### <a name="monitoring-virtual-machines"></a>監視虛擬機器
您在 Azure 中執行的虛擬機器混合了 Windows 和 Linux 虛擬機器。  您會使用「Azure 監視器」來檢視[活動記錄](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)和[主機層級的計量](../monitoring-and-diagnostics/monitoring-overview-metrics.md)，然後將 [Azure 診斷延伸模組](../virtual-machines/linux/tutorial-monitoring.md#install-diagnostics-extension)新增至虛擬機器，以從客體作業系統收集計量。  接著，您會建立[警示規則](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)，以在基本計量 (例如處理器使用率和記憶體) 超出閾值時主動通知您。

為了收集有關執行商務應用程式之虛擬機器的更多詳細資料，您會在每部機器上[建立 Log Analytics 工作區並啟用 VM 延伸模組](../log-analytics/log-analytics-quick-collect-azurevm.md)。  您會為應用程式設定[不同資料來源的集合](../log-analytics/log-analytics-data-sources.md)，然後[建立檢視](../log-analytics/log-analytics-view-designer.md)來針對其每日的作業與效能提出報告。  接著，您會[建立警示規則](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md)，以在收到特定錯誤事件時通知您。  為了持續監視所安裝代理程式的健康情況，您會新增[代理程式健康情況管理解決方案](../operations-management-suite/oms-solution-agenthealth.md)。

為了進一步深入了解應用程式，您會[相依性代理程式](../operations-management-suite/operations-management-suite-service-map-configure.md)至虛擬機器，以將它們新增至[服務對應](../operations-management-suite/operations-management-suite-service-map.md)。  它會探索重要的程序，並識別機器與其他服務之間的連線。  在系統回報中斷之後，您會使用「服務對應」來執行鑑識調查，以識別發生問題的特定機器。  接著，您會[針對 Log Analytics 資料建立查詢](../log-analytics/log-analytics-log-search-new.md)以識別未來的問題，並建立警示規則以在偵測到該情況時主動通知您。



## <a name="next-steps"></a>後續步驟
深入了解

* [Ignite 2016 上的 Azure 監視器影片](https://myignite.microsoft.com/videos/4977)
* [開始使用 Azure 監視器](monitoring-get-started.md)
* [Azure 診斷](../azure-diagnostics.md)，如果您正在嘗試診斷雲端服務、虛擬機器、虛擬機器擴展集或 Service Fabric 應用程式中的問題。
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) ，如果您正在嘗試診斷 App Service Web 應用程式中的問題。
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 以分析所收集的監視資料。
