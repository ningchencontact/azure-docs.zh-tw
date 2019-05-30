---
title: Azure 監視器中資料的來源 | Microsoft Docs
description: 描述可用於監視 Azure 資源 (以及在資源上執行的應用程式) 健康情況和效能的資料。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: bwren
ms.openlocfilehash: b77fb3ab5651147c59b9f0afd22a2d6d0159c90e
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357425"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Azure 監視器中監視資料的來源
Azure 監視器為基礎[常見的監視資料平台](data-platform.md)包含[記錄檔](data-platform-logs.md)並[計量](data-platform-metrics.md)。 資料收集到此平台，可讓多個資源來使用 Azure 監視器中一組常用的工具一起進行分析的資料。 監視資料可能也會傳送至其他位置來支援特定案例中，並將某些資源可能寫入其他位置才可以收集到記錄或度量資訊。

本文說明不同的監視 Azure 資源所建立的監視資料除了 Azure 監視器所收集的資料來源。 這項資料收集到不同的位置所需的組態的詳細資訊，會提供連結。

## <a name="application-tiers"></a>應用程式層

監視 Azure 應用程式的資料的來源可以分成層、 最高的三層分別為您的應用程式本身及較低的層級正在 Azure 平台的元件。 從每一層存取資料的方法而異。 下表摘要說明應用程式層，並監視每個層中的資料來源會出現在下列各節。 請參閱[監視在 Azure 中的資料位置](data-locations.md)如需每個資料位置以及如何存取其資料的描述。


![監視的層級](../media/overview/overview.png)


### <a name="azure"></a>Azure
下表簡短描述專屬於 Azure 的應用程式層。 在下列各節中的每個詳細資料進一步遵循的連結。

| 層 | 描述 | 收集方法 |
|:---|:---|:---|
| [Azure Tenant](#azure-tenant) | 租用戶層級 Azure 服務的作業相關資料，例如 Azure Active Directory。 | 在入口網站中檢視 AAD 資料或設定要使用租用戶的診斷設定的 Azure 監視的集合。 |
| [Azure 訂用帳戶](#azure-subscription) | 健全狀況和管理 Resource Manager 和服務健全狀況等您 Azure 訂用帳戶中的跨資源服務相關的資料。 | 在入口網站中檢視或設定要使用記錄設定檔的 Azure 監視的集合。 |
| [Azure 資源](#azure-resources) |  每個 Azure 資源的效能與作業相關的資料。 | 計量瀏覽器中自動執行，檢視收集的計量。<br>設定診斷以收集 Azure 監視器中的記錄檔的設定。<br>監視解決方案和適用於特定的資源類型的詳細監視的深入解析。 |

### <a name="azure-other-cloud-or-on-premises"></a>Azure、 其他雲端或內部部署 
下表簡短描述可能會在 Azure、 其他雲端或內部部署的應用程式層。 在下列各節中的每個詳細資料進一步遵循的連結。

| 層 | 描述 | 收集方法 |
|:---|:---|:---|
| [作業系統 （客體）](#operating-system-guest) | 計算資源上的作業系統相關資料。 | 安裝 Log Analytics 代理程式到 Azure 監視器和相依性代理程式，以收集相依性適用於 Vm 支援 Azure 監視器收集用戶端資料來源。<br>針對 Azure 虛擬機器，安裝 Azure 診斷擴充功能至 「 Azure 監視器收集記錄和計量。 |
| [應用程式程式碼](#application-code) | 實際的應用程式和程式碼，包括效能追蹤、 應用程式記錄檔，以及使用者遙測的功能與效能相關的資料。 | 檢測您的程式碼到 Application Insights 收集資料。 |
| [自訂來源](#custom-sources) | 從外部服務或其他元件或裝置的資料。 | 收集到 Azure 監視器的記錄或計量資料，從任何 REST 用戶端。 |

## <a name="azure-tenant"></a>Azure 租用戶
與您 Azure 租用戶相關的遙測，會從整個租用戶服務 (例如 Azure Active Directory) 收集。

![Azure 租用戶集合](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory 稽核記錄
[Azure Active Directory 報告](../../active-directory/reports-monitoring/overview-reports.md)包含在特定租用戶內所進行登入活動的歷程記錄，以及所做變更的稽核線索。 

| 目的地 | 描述 | 參考 |
|:---|:---|:---|
| Azure 監視器記錄檔 | 設定要收集透過其他監視資料來分析的 Azure 監視器中的 Azure AD 記錄檔。 | [整合 Azure 監視器記錄檔 （預覽） 中的 Azure AD 記錄檔](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure 儲存體 | Azure AD 將記錄匯出至 Azure 儲存體進行封存。 | [教學課程：封存到 Azure 儲存體帳戶 （預覽） 的 Azure AD 記錄檔](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| 事件中樞 | Stream 使用事件中樞的其他位置的 Azure AD 記錄檔。 | [教學課程：Stream 到 Azure 事件中樞 （預覽） 的 Azure Active Directory 記錄](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。 |



## <a name="azure-subscription"></a>Azure 訂用帳戶
健全狀況和操作您的 Azure 訂用帳戶相關的遙測。

![Azure 訂用帳戶](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure 活動記錄 
[Azure 活動記錄檔](activity-logs-overview.md)包含服務健全狀況記錄，以及在您的 Azure 訂用帳戶中的資源所做的組態變更的記錄。 所有 Azure 資源都會有活動記錄，也代表了資源的_外部_檢視。

| 目的地 | 描述 | 參考 |
|:---|:---|
| 活動記錄檔 | 活動記錄檔會收集到自己的資料存放區，您可以檢視從 Azure 監視器 功能表，或使用建立活動記錄警示。 | [查詢活動記錄，以在 Azure 入口網站](activity-log-view.md#azure-portal) |
| Azure 監視器記錄檔 | 設定 Azure 監視器的記錄檔收集活動記錄分析與其他監視資料。 | [收集並分析 Azure 活動記錄中 Azure 監視器中的 Log Analytics 工作區](activity-log-collect.md) |
| Azure 儲存體 | 封存活動記錄匯出至 Azure 儲存體。 | [封存活動記錄檔](activity-log-export.md#archive-activity-log)  |
| 事件中樞 | Stream 活動記錄使用事件中樞的其他位置 | [Stream 活動記錄到事件中樞](activity-log-export.md#stream-activity-log-to-event-hub)。 |

### <a name="azure-service-health"></a>Azure 服務健康狀態
[Azure 服務健康狀態](../../service-health/service-health-overview.md)提供您有關應用程式和資源依賴的訂用帳戶中，Azure 服務健康狀態的資訊。

| 目的地 | 描述 | 參考 |
|:---|:---|:---|
| 活動記錄檔<br>Azure 監視器記錄檔 | 服務健全狀況記錄會儲存在 Azure 活動記錄，因此您可以在 Azure 入口網站中檢視它們，或執行任何其他您可以使用活動記錄檔執行的活動。 | [使用 Azure 入口網站檢視服務健康情況通知](service-notifications.md) |


## <a name="azure-resources"></a>Azure 資源
計量和資源層級診斷記錄會提供 Azure 資源_內部_作業的相關資訊。 這些是適用於大部分的 Azure 服務，並監視解決方案和 insights 收集的特定服務的其他資料。

![Azure 資源集合](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>平台計量 
大部分的 Azure 服務會傳送[平台計量](data-platform-metrics.md)以反映其效能和直接與計量資料庫的作業。 特定[計量會因為各種類型的資源而異](metrics-supported.md)。 

| 目的地 | 描述 | 參考 |
|:---|:---|:---|
| Azure 監視器計量 | 平台計量會寫入至 Azure 監視器計量資料庫進行任何設定。 從計量瀏覽器的存取平台計量。  | [開始使用 Azure 計量瀏覽器](metrics-getting-started.md)<br>[支援 Azure 監視器的計量](metrics-supported.md) |
| Azure 監視器記錄檔 | 使用 Log Analytics 的趨勢和其他分析，複製記錄檔的平台計量。 | [Azure 診斷直達 Log Analytics](collect-azure-metrics-logs.md#azure-diagnostics-direct-to-log-analytics) |
| 事件中樞 | Stream 度量，以使用事件中樞的其他位置。 |[將 Azure 監視資料串流至事件中樞以供外部工具取用](stream-monitoring-data-event-hubs.md) |

### <a name="diagnostic-logs"></a>診斷記錄
[診斷記錄](diagnostic-logs-overview.md)深入了解_內部_Azure 資源的作業。  根據預設，不會啟用診斷記錄檔。 您必須啟用它們，並指定每個資源的目的地。 

設定需求與診斷記錄的內容會因資源類型，並不是所有服務尚未都建立診斷記錄檔。 請參閱[Azure 診斷記錄支援服務、 結構描述和類別](diagnostic-logs-schema.md)如需有關每個服務以及詳細的設定程序的連結。 如果服務未列在本文中，然後該服務目前不寫入診斷記錄檔。

| 目的地 | 描述 | 參考 |
|:---|:---|:---|
| Azure 監視器記錄檔 | 傳送至 Azure 監視器記錄檔的診斷記錄與其他收集的記錄資料的分析。 某些資源可以撰寫直接與 Azure 監視器，而其他項目寫入至儲存體帳戶之前要匯入 Log Analytics 工作區。 | [Azure 監視器中的 Log Analytics 工作區 Stream Azure 診斷記錄](diagnostic-logs-stream-log-store.md)<br>[使用 Azure 入口網站，以從 Azure 儲存體收集記錄檔](azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage)  |
| 儲存體 | 傳送診斷記錄封存至 Azure 儲存體。 | [封存 Azure 診斷記錄](archive-diagnostic-logs.md) |
| 事件中樞 | Stream 診斷記錄使用事件中樞的其他位置。 |[Stream Azure 診斷記錄至事件中樞](diagnostic-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>作業系統 （客體）
Azure、其他雲端和內部部署中的計算資源具有要監視的客體作業系統。 一或多個代理程式安裝，您可以從客體收集遙測資料至 「 Azure 監視器來分析與 Azure 服務本身的監視工具。

![Azure 計算資源集合](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Azure 診斷擴充功能
針對 Azure 虛擬機器可讓您啟用 Azure 診斷擴充功能來收集記錄和計量從客體作業系統的 Azure 計算資源，包括 Azure 雲端服務 （傳統） 的 Web 和背景工作角色，虛擬機器的虛擬機器擴展集和 Service Fabric。

| 目的地 | 描述 | 參考 |
|:---|:---|:---|
| 儲存體 | 當您啟用診斷擴充功能時，它會寫入預設儲存體帳戶。 | [在 Azure 儲存體中儲存和檢視診斷資料](diagnostics-extension-to-storage.md) |
| Azure 監視器計量 | 當您設定診斷延伸模組，以收集效能計數器時，它們會寫入 Azure 監視器計量資料庫。 | [傳送至 Azure 監視器計量的客體 OS 度量儲存為 Windows 虛擬機器中使用 Resource Manager 範本](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Application Insights 記錄 | 從收集記錄和效能計數器支援您的應用程式分析與其他應用程式資料的計算資源。 | [雲端服務、 虛擬機器或 Service Fabric 診斷資料傳送至 Application Insights](diagnostics-extension-to-application-insights.md) |
| 事件中樞 | 設定診斷擴充功能來串流處理到其他位置使用事件中樞的資料。  | [使用事件中樞串流最忙碌路徑中的 Azure 診斷資料](diagnostics-extension-stream-event-hubs.md) |

### <a name="log-analytics-agent"></a>Log Analytics 代理程式 
安裝完整的監視和管理您的 Windows 或 Linux 虛擬機器的 Log Analytics 代理程式。 虛擬機器可以在 Azure、其他雲端或內部部署中執行。

| 目的地 | 描述 | 參考 |
|:---|:---|:---|
| Azure 監視器記錄檔 | Log Analytics 代理程式會連線到 Azure 直接或透過 System Center Operations Manager 監視，並可讓您從您設定的資料來源，或從監視解決方案，以提供額外的深入了解應用程式收集資料在虛擬機器上執行。 | [Azure 監視器中的代理程式資料來源](agent-data-sources.md)<br>[Operations Manager 連線到 Azure 監視器](om-agents.md) |


### <a name="azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器 
[適用於 Vm 的 azure 監視器](../insights/vminsights-overview.md)提供自訂的監視體驗的虛擬機器提供核心 Azure 監視器功能，包括服務狀態和 VM 健康狀態以外的功能。 它需要整合與 Log Analytics 代理程式以收集虛擬機器和外部處理序的相依性上執行的處理序的相關探索到的資料相依性代理程式在 Windows 和 Linux 虛擬機器上。

| 目的地 | 描述 | 參考 |
|:---|:---|:---|
| Azure 監視器記錄檔 | 儲存資料的相關程序和相依性代理程式上。 | [使用 Azure 監視器的 Vm （預覽） 了解應用程式元件的對應](../insights/vminsights-maps.md) |
| VM 儲存體 | 適用於 Vm 的 azure 監視器會將健全狀況狀態資訊儲存在自訂位置。 這是只能夠在 Azure 入口網站中除了適用於 Vm 的 Azure 監視器[Azure 資源健康狀態 REST API](/rest/api/resourcehealth/)。 | [了解 Azure 虛擬機器的健全狀況](../insights/vminsights-health.md)<br>[Azure 資源健康狀態 REST API](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>應用程式程式碼
Azure 監視器中監視的詳細的應用程式是使用[Application Insights](https://docs.microsoft.com/azure/application-insights/)以從各種平台上執行的應用程式收集資料。 應用程式可以在 Azure、其他雲端或內部部署中執行。

![應用程式資料收集](media/data-sources/applications.png)


### <a name="application-data"></a>應用程式資料
當您藉由安裝檢測套件，針對應用程式啟用 Application Insights 時，它會收集與應用程式效能及作業相關的計量和記錄。 Application Insights 會將所收集的資料儲存在其他資料來源所使用的相同 Azure 監視器資料平台。 它包含廣泛的工具來分析此資料，但您也可以分析它與使用計量瀏覽器和 Log Analytics 等工具的其他來源的資料。

| 目的地 | 描述 | 參考 |
|:---|:---|:---|
| Azure 監視器記錄檔 | 操作包括頁面檢視、 應用程式的要求、 例外狀況，以及追蹤您的應用程式的詳細資料。 | [分析 Azure 監視器中的記錄資料](../log-query/log-query-overview.md) |
|                    | 遙測相互關聯來支援應用程式對應的應用程式元件之間的相依性資訊。 | [在 Application Insights 的遙測相互關聯](../app/correlation.md) <br> [應用程式對應](../app/app-map.md) |
|            | 測試的可用性和回應從公用網際網路上的不同位置的應用程式的可用性測試的結果。 | [監視任何網站的可用性和回應性](../app/monitor-web-app-availability.md) |
| Azure 監視器計量 | Application Insights 會收集描述應用程式，除了您在您的應用程式到 Azure 監視器計量資料庫中定義的自訂度量的作業與效能的計量。 | [Application Insights 中的記錄檔為基礎和以預先彙總計量](../app/pre-aggregated-metrics-log-metrics.md)<br>[自訂事件和度量的 application Insights API](../app/api-custom-events-metrics.md) |
| Azure 儲存體 | 應用程式將資料傳送至 Azure 儲存體進行封存。 | [從 Application Insights 匯出遙測](../app/export-telemetry.md) |
|            | 可用性測試的詳細資料會儲存在 Azure 儲存體。 使用 Azure 入口網站中的 Application Insights，若要下載本機分析。 可用性測試的結果會儲存在 Azure 監視器記錄檔中。 | [監視任何網站的可用性和回應性](../app/monitor-web-app-availability.md) |
|            | Profiler 追蹤資料會儲存在 Azure 儲存體。 使用 Azure 入口網站中的 Application Insights，若要下載本機分析。  | [分析生產環境應用程式在 Azure 中使用 Application Insights](../app/profiler-overview.md) 
|            | 偵錯例外狀況的子集所擷取的資料會儲存在 Azure 儲存體的快照集。 使用 Azure 入口網站中的 Application Insights，若要下載本機分析。  | [快照集運作方式](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>監視解決方案和深入解析
[監視解決方案](../insights/solutions.md)並[Insights](../insights/insights-overview.md)收集資料，以提供額外的深入了解特定服務或應用程式的作業。 它們可以處理不同的應用程式層和更多個層中的資源。

### <a name="monitoring-solutions"></a>監視解決方案

| 目的地 | 描述 | 參考
|:---|:---|:---|
| Azure 監視器記錄檔 | 監視解決方案收集資料到 Azure 監視器記錄檔，它可能使用的查詢語言分析或[檢視](view-designer.md)一般包含在方案中。 | [資料收集詳細資料監視在 Azure 中的解決方案](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>適用於容器的 azure 監視器
[適用於容器的 azure 監視器](../insights/container-insights-overview.md)提供自訂的監視體驗，如[Azure Kubernetes Service (AKS)](/azure/aks/)。 它會收集有關下表中說明這些資源的其他資料。

| 目的地 | 描述 | 參考 |
|:---|:---|:---|
| Azure 監視器記錄檔 | AKS 包括清查、 記錄和事件的監視資料存放區。 以充分利用其分析功能，在入口網站中，度量資料也會儲存在記錄檔中。 | [使用適用於容器的 Azure 監視器來了解 AKS 叢集效能](../insights/container-insights-analyze.md) |
| Azure 監視器計量 | 計量資料會儲存在磁碟機的視覺效果和警示的計量資料庫。 | [在 計量瀏覽器中檢視容器計量](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | 為了以幾近即時體驗，適用於容器的 Azure 監視器會顯示在 Azure 入口網站中直接從 Azure Kubernetes 服務的資料。 | [如何使用適用於容器的 Azure 監視器 (預覽) 即時檢視容器記錄](../insights/container-insights-live-logs.md) |

### <a name="azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器
[適用於 Vm 的 azure 監視器](../insights/vminsights-overview.md)監視虛擬機器時，提供自訂的體驗。 Vm 的 Azure 監視器所收集資料的說明包含在[作業系統 （客體）](#operating-system-guest)上一節。

## <a name="custom-sources"></a>自訂來源
除了應用程式的標準層，您可能需要監視其他資源，這些資源具有無法與其他資料來源一起收集的遙測。 如需這些資源，此資料寫入計量或記錄檔使用 Azure 監視器 API。

![自訂集合](media/data-sources/custom.png)

| 目的地 | 方法 | 描述 | 參考 |
|:---|:---|:---|:---|
| Azure 監視器記錄檔 | 資料收集器 API | 收集記錄資料從任何 REST 用戶端並儲存在 Log Analytics 工作區。 | [將記錄資料傳送至 Azure 監視器與 「 HTTP 資料收集器 API （公開預覽）](data-collector-api.md) |
| Azure 監視器計量 | 自訂計量 API | 從任何 REST 用戶端和 Azure 監視器計量資料庫中的存放區中收集計量資料。 | [使用 REST API，將自訂度量以進行 Azure 資源傳送至 Azure 監視器計量存放區](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>其他服務
在 Azure 中的其他服務會將資料寫入 Azure 監視器的資料平台。 這可讓您分析資料，這些服務所收集的 Azure 監視器所收集的資料，並運用相同的分析和視覺效果工具。

| 服務 | 目的地 | 描述 | 參考 |
|:---|:---|:---|:---|
| [Azure 資訊安全中心](/azure/security-center/) | Azure 監視器記錄檔 | Azure 資訊安全中心會儲存在 Log Analytics 工作區可讓它與其他 Azure 監視器所收集的記錄資料進行分析所收集安全性資料。  | [Azure 資訊安全中心的資料收集](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](/azure/sentinel/) | Azure 監視器記錄檔 | Azure 的 Sentinel 儲存它會收集 Log Analytics 工作區可讓它與其他 Azure 監視器所收集的記錄資料進行分析的不同資料來源的資料。  | [連接資料來源](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 監視器所收集的監視資料類型](data-platform.md)，以及如何檢視及分析這項資料。
- 清單[Azure 資源在其中儲存資料的不同位置](data-locations.md)，以及存取它。 
