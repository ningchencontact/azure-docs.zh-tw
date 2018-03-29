---
title: Azure Service Fabric 監視和診斷概觀 | Microsoft Docs
description: 了解如何對 Azure Service Fabric 叢集、應用程式和服務進行監視和診斷。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: dekapur
ms.openlocfilehash: f784576547f0d85a825ad9dd107c6c84cd261092
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>對 Azure Service Fabric 進行監視和診斷

本文提供針對在 Service Fabric 叢集中執行的應用程式設定監視和診斷的概觀。 不論任何雲端環境，針對工作負載的開發、測試及部署進行監視和診斷都極為重要。 監視可讓您追蹤應用程式的使用方式、資源使用率，以及叢集的整體健康情況。 您可以使用這些資訊來診斷並修正叢集中的任何問題，以及協助防止未來發生問題。 

監視和診斷的主要目標是︰
* 偵測並診斷基礎結構問題
* 偵測您的應用程式問題
* 了解資源耗用量
* 追蹤應用程式、服務及基礎結構效能

在 Service Fabric 叢集中，監視和診斷資料來自三個層級：應用程式、平台 (叢集)，以及基礎結構。 
* [應用程式層級](service-fabric-diagnostics-event-generation-app.md)包含應用程式效能的相關資料，以及您加入的任何額外自訂記錄。 應用程式監視資料最後會從應用程式本身到達 Application Insights (AI)。 這項資料可以透過 AI SDK、EventFlow 或您選擇的另一個管線而來。
* [平台層級](service-fabric-diagnostics-event-generation-infra.md)包含叢集上正在採取之動作的事件，這些事件都與叢集和叢集上執行之應用程式的管理有關。 平台監視資料應該傳送到 OMS Log Analytics，利用 Service Fabric 分析解決方案協助將內送事件視覺化。 這項資料通常會透過 Windows 或 Linux 的 Azure 診斷擴充傳送到儲存體帳戶，然後 OMS Log Analytics 再從該處存取資料。 
* 基礎結構層級著重於[效能監視](service-fabric-diagnostics-event-generation-perf.md)，查看關鍵計量資訊和效能計數器以判斷資源使用率和負載。 效能監視可藉由使用代理程式達成。我們建議使用 OMS (Microsoft Monitoring) 代理程式，讓您的效能資料最後都會到達相同的位置成為平台事件，這會讓您在將跨叢集的變更相互關聯時獲得更好的診斷體驗。 

![診斷概觀圖表](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="monitoring-scenarios"></a>監視案例

本節討論監視 Service Fabric 叢集的主要案例 - 應用程式、叢集、效能和健康情況監視。 每個案例都會討論監視的意圖和整體方法。 如需這些資訊和 Azure 資源其他一般監視建議的更多詳細資料，請參閱[最佳做法 - 監視和診斷](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)。 

這些案例也可以大致對應到如上面所討論的三個監視和診斷資料層級，也就是說，如果要適當地處理叢集中的每個案例，您要以對應的層級取得監視和診斷資料。 健康情況監視案例是一種例外情況，因為它的範圍涵蓋叢集和在上面執行的所有項目。

## <a name="application-monitoring"></a>應用程式監視
應用程式監視會追蹤您建置的應用程式功能與元件如何使用。 您可以監視應用程式，以確定找出影響使用者的問題。 監視應用程式在下列情況可能會非常有用：
* 判斷應用程式負載和使用者流量 - 您需要調整服務以符合使用者需求，或解決應用程式中的潛在瓶頸嗎？
* 識別叢集中服務通訊與遠端的問題
* 找出您的使用者使用應用程式的行為 - 檢測您的應用程式可協助引導未來功能的開發，也更能診斷出應用程式錯誤

如果要在 Service Fabric 中以應用程式層級進行監視，我們建議您使用 Application Insights (AI)。 AI 與 Service Fabric 的整合包括 Visual Studio 和 Azure 入口網站的工具體驗，以及了解 Service Fabric 服務內容和 AI 儀表板與應用程式對應中的遠端功能，讓您獲得完整而立即可用的記錄體驗。 AI 雖然會自動為您建立和收集許多記錄，但我們還是建議您對應用程式加入更多自訂記錄，然後在 AI 中與 AI 提供的內容一起顯示，可產生更豐富的診斷體驗，以供將來處理問題。 在[使用 Application Insights 進行事件分析](service-fabric-diagnostics-event-analysis-appinsights.md)中可查看更多使用 AI 搭配 Service Fabric 的內容。 

若要開始檢測您的程式碼以監視應用程式，請前往[應用程式層級事件和記錄產生](service-fabric-diagnostics-event-generation-app.md)。 

### <a name="monitoring-application-availability"></a>監視應用程式可用性
叢集中的所有項目有可能如預期般執行並報告為狀況良好，但您的應用程式似乎無法存取或無法連線。 雖然不會發生太多這類案例，但是最好知道當發生時，要如何盡快減輕問題。 追蹤系統元件的可用性時，通常會考慮可用性監視，來了解系統的整體「執行時間」。 在叢集中，我們將重點放在應用程式觀點的可用性 - 平台會運作以確保應用程式生命週期管理狀況不會造成停機時間。 然而，叢集中的各種問題可能會影響您的應用程式執行時間，而在這些情況下，應用程式擁有者通常會想要立刻知道。 我們建議您在叢集中的其他所有應用程式之外，另外部署一個監視程式。 這類監視程式的目的是每隔一段時間檢查適當的應用程式端點，然後回報這些端點都可存取。 您也可以使用可偵測端點的外部服務，並以指定的時間間隔回報來達到目的。

## <a name="cluster-monitoring"></a>叢集監視
如果要確保平台和在上面執行的所有工作負載都如預期般執行，監視 Service Fabric 叢集就非常重要。 Service Fabric 的其中一個目標是讓應用程式在硬體失敗時也能夠繼續執行。 這可以透過 平台的系統服務偵測基礎結構問題，並快速地將工作負載容錯移轉到叢集中的其他節點來達成。 但是在這個特殊情況下，如果是系統服務本身有問題，會發生什麼情況？ 或如果在嘗試移動工作負載時違反了設置服務的規則，會發生麼情況？ 監視叢集可讓您隨時掌握叢集中正在進行的活動，有助於診斷問題並有效地修正。 您要設法找出的一些重點包括：
* 以設置應用程式和平衡叢集的工作而言，Service Fabric 是否依照您預期的方式運作？ 
* 修改叢集設定而採取的動作是否受到認可且如預期般運作？ 這在調整叢集規模時尤其重要。
* Service Fabric 是否在叢集內正確處理您的資料和服務間通訊？

Service Fabric 透過操作通道和資料與傳訊通道，提供一組完整而立即可用的事件。 在 Windows 中，這些事件是以單一的 ETW 提供者形式，搭配一組相關的 `logLevelKeywordFilters`，用來挑選不同的通道。 在 Linux 上，所有平台事件都會經過 LTTng 並放入一個表格，可以視需要從這個表格篩選事件。 

這些通道包含經過策劃、結構化的事件，可用來進一步了解您的叢集狀態。 叢集建立時預設會啟用「診斷」，這會為您設定一個 Azure 儲存體表格，來自這些通道的事件會傳送到這個表格供您將來查詢之用。 您可以在[平台層級事件和記錄產生](service-fabric-diagnostics-event-generation-infra.md)閱讀更多監視叢集的詳細資訊。 我們建議您使用 OMS Log Analytics 監視您的叢集。 OMS Log Analytics 提供一個 Service Fabric 專屬的解決方案「Service Fabric 分析」，它提供一個監視 Service Fabric 叢集的自訂儀表板，可讓您查詢叢集的事件和設定警示。 您可以在[使用 OMS 進行事件分析](service-fabric-diagnostics-event-analysis-oms.md)閱讀更多資訊。 

### <a name="watchdogs"></a>監視程式
一般而言，監視程式是一個單獨的服務，可以觀察服務之間的健康情況和負載、偵測端點，並回報叢集中任何項目的健康情況。 如此可避免在單一服務檢視中無法偵測到的錯誤。 監視程式也非常適合裝載能夠執行修復動作的程式碼，而無需使用者互動 (例如在特定時間間隔，清除儲存體中的記錄檔)。 您可以在[這裡](https://github.com/Azure-Samples/service-fabric-watchdog-service)找到範例監視程式服務實作。

## <a name="performance-monitoring"></a>效能監視
監視根本的基礎結構是了解叢集狀態和資源使用率的關鍵部分。 測量系統效能取決於許多因素，每個因素通常都可透過關鍵效能指標 (KPI) 測量。 Service Fabric 相關的 KPI 可以對應至可從叢集中節點收集的計量，作為效能計數器。
這些 KPI 可協助：
* 了解資源使用率和負載 - 作為調整叢集規模，或最佳化服務流程之用
* 預測基礎結構問題 - 許多問題在發生之前，效能會有突然的變化 (降低)，因此您可以使用如網路 I/O 和 CPU 使用率等 KPI 來預測和診斷基礎結構的問題

您可以在[效能計量](service-fabric-diagnostics-event-generation-perf.md)找到在基礎結構層級應該收集的效能計數器清單。 

對於應用程式層級的效能監視，Service Fabric 會提供一組效能計數器，供 Reliable Services 和動作項目程式設計模型使用。 如果您使用上述其中一種模型，這些效能計數器可以提供一些 KPI，協助確保您的動作項目正確向上和向下微調，或者您的可靠服務要求處理的速度夠快。 請參閱[可靠服務遠端的監視](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters)和 [Reliable Actors 的效能監視](service-fabric-reliable-actors-diagnostics.md#performance-counters)以取得這些內容的詳細資訊。 除此之外，如果您的應用程式經過設定，Application Insights 也會收集一組效能計量。

請使用 OMS 代理程式收集適當的效能計數器，並在 OMS Log Analytics 中檢視這些 KPI。 您也可以使用 Azure 診斷代理程式擴充 (或任何其他類似的代理程式) 來收集並儲存計量以供分析。 

## <a name="health-monitoring"></a>健康狀況監視
Service Fabric 平台包括健康情況模型，針對叢集中的實體狀態提供可延伸的健康情況報告。 每個節點、應用程式、服務、分割區、複本或執行個體，都有可持續更新的健康情況狀態。 健康情況狀態可以是「良好」、「警告」或「錯誤」。 健康情況狀態會根據叢集中的問題，透過針對每個實體所發出的健康情況報告變更。 實體的健康情況狀態可以隨時在 Service Fabric Explorer (SFX) 中加以檢查 (如下所示)，或透過平台的健康情況 API 查詢。 您也可以新增您自己的健康情況報告或使用健康情況 API，來自訂健康情況報告和修改實體的健康情況狀態。 在 [Service Fabric 健康情況監視簡介](service-fabric-health-introduction.md)可以找到健康情況模型的更多詳細資料。

![SFX 健康情況儀表板](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

在 SFX 中除了能看到最新的健康情況報告，每個報告也可當成一個事件。 健康情況事件可透過操作通道收集 (請參閱[使用 Azure 診斷進行事件彙總](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations))，並儲存在 OMS Log Analytics 中供未來警示和查詢之用。 這有助於偵測出可能會影響您應用程式可用性的問題，所以我們建議您針對適當的失敗案例設定警示 (透過 OMS 設定自訂警示)。

## <a name="monitoring-workflow"></a>監視工作流程 

監視與診斷的整體工作流程包含三個步驟：

1. **事件產生**：這包含基礎結構、平台 (叢集) 和應用程式層級的事件 (記錄、追蹤、自訂事件)
2. **事件彙總**：這個階段實際上是讓您的事件最後到達某個您可以分析事件之工具的管線，包括 Azure 診斷擴充或 EventFlow
3. **分析**：事件在工具中必須可以存取以供分析 (視覺效果、查詢、警示等)

市面上很多產品已涵蓋這三個領域，因此您可以針對每個領域選擇不同的技術。 請務必確認各項技術能夠互相配合，以為叢集提供端對端的監視解決方案。

## <a name="event-generation"></a>事件產生

監視和診斷工作流程的第一個步驟是設定事件和記錄資料的建立與產生。 這些事件、記錄和效能計數器會在所有三個層級發出：應用程式層級 (對部署至叢集的應用程式和服務新增的任何檢測)、平台 (根據 Service Fabric 的運作從叢集發出的事件) 和基礎結構層級 (來自每個節點的效能計量)。 雖然 Service Fabric 會啟用一些預設的檢測，但是在每個這些層級收集的診斷資料都可加以自訂。 

深入了解[平台層級事件](service-fabric-diagnostics-event-generation-infra.md)和[應用程式層級事件](service-fabric-diagnostics-event-generation-app.md)，以了解提供項目，以及如何新增其他的檢測。 您在這裡最主要要做的決定是您要如何檢測您的應用程式。 如果是 .NET 應用程式，我們建議您使用 ILogger，但您也可以探索 EventSource、Serilog 和其他類似的程式庫。 如果是 Java，我們建議使用 Log4j。 除了這些，還有其他幾種根據應用程式本質可做的選擇。 請盡情研究最適合您的特定使用案例，或選取您最偏好使用的一種。 

決定好要使用的記錄提供者之後，您必須確認系統能正確彙總記錄檔並加以儲存。

## <a name="event-aggregation"></a>事件彙總

若要收集應用程式與叢集所產生的記錄檔和事件，我們通常建議使用 [Azure 診斷擴充](service-fabric-diagnostics-event-aggregation-wad.md) (類似於代理程式型記錄收集) 或 [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) (內含式記錄收集)。 如果您使用 Application Insights，而且正以 .NET 或 Java 開發，則建議使用 Application Insights SDK 取代 EventFlow。

雖然您可以使用其中一種完成類似工作，但是在大部分情況下，結合 Azure 診斷擴充代理程式和內含式收集管線 (AI SDK 或 EventFlow) 可以產生更可靠而全面的監視工作流程。 您可以選擇針對平台層級事件使用 Azure 診斷擴充代理程式，同時針對應用程式層級記錄使用 AI SDK 或 EventFlow (內含式收集)。 

如果您只想要使用其中一種，以下是要記住的一些重點。
* 對 Service Fabric 服務來說，如果記錄來源與目的地的組合不常變更，而且來源與其目的地之間有直接的對應，則使用 Azure 診斷擴充來收集應用程式記錄是非常適合的選項。 這是因為 Azure 診斷會在資源管理員層進行設定，因此若有大幅設定變更時需更新整個叢集。 這表示，比起採用 AI SDK 或 EventFlow，效率通常較低。
* 使用 EventFlow 時，您可以讓服務直接將記錄傳送給分析和視覺化平台，及/或傳送給儲存體。 ILogger、Serilog 等其他程式庫或許也可以用於相同的目的，但 EventFlow 的優點在於它是專門針對內含式記錄收集而設計，並且支援 Service Fabric 服務。 這通常有幾項優點，可以簡化設定和部署，並提供更多的彈性來支援不同的記錄程式庫和分析工具。 

## <a name="event-analysis"></a>事件分析

市面上有多個優異的平台，可進行監視和診斷資料的分析和視覺化作業。 我們建議的兩個平台是 [OMS](service-fabric-diagnostics-event-analysis-oms.md) 和 [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)，因為它們整合 Service Fabric。 您也應該研究 [Elastic Stack](https://www.elastic.co/products) (尤其是當您考慮在離線環境中執行叢集時)、[Splunk](https://www.splunk.com/)，或您偏好的任何其他平台。 

不論選擇何種平台，重點在於您是否喜歡其中的使用者介面和查詢選項、視覺化資料與建立一目了然之儀表板的功能，以及是否提供可強化監視的其他工具，例如自動化警示。

除了選擇的平台之外，在將 Service Fabric 叢集設為 Azure 資源時，您也可以為您的機器存取 Azure 立即可用的效能監視功能。

### <a name="azure-monitor"></a>Azure 監視器

您可以使用 [Azure 監視器](../monitoring-and-diagnostics/monitoring-overview.md)，監視構成 Service Fabric 叢集的許多 Azure 資源。 對於[虛擬機器擴展集](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)和個別的[虛擬機器](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines)，Azure 入口網站會自動收集並顯示一組計量。 若要檢視收集的資訊，請在 Azure 入口網站中，選取包含 Service Fabric 叢集的資源群組。 接著，選取您想要檢視的虛擬機器擴展集。 在 [監視] 區段 (左側導覽上)，選取 [計量]，以檢視值的圖表。

![已收集計量資訊的 Azure 入口網站檢視](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

若要自訂圖表，請依照 [Microsoft Azure 中的計量](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)中的指示。 您也可以根據這些計量建立警示，如[在 Azure 監視器中建立 Azure 服務的警示](../monitoring-and-diagnostics/monitoring-overview-alerts.md)所述。 

## <a name="next-steps"></a>後續步驟

* 在[平台層級事件和記錄產生](service-fabric-diagnostics-event-generation-infra.md)深入了解監視平台，以及監視 Service Fabric 提供給您的事件
* 若要開始檢測您的應用程式，請參閱[應用程式層級事件和記錄產生](service-fabric-diagnostics-event-generation-app.md)
* 透過[監視和診斷 Service Fabric 上的 ASP.NET Core 應用程式](service-fabric-tutorial-monitoring-aspnet.md)，針對您的應用程式逐步執行設定 AI 的步驟
* 了解如何設定 OMS Log Analytics 以監視容器：[監視和診斷 Azure Service Fabric 中的 Windows 容器](service-fabric-tutorial-monitoring-wincontainers.md)

