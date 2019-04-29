---
title: Azure 監視代理程式概觀 | Microsoft Docs
description: 本文將詳細說明可支援對裝載於 Azure 或混合式環境中的虛擬機器進行監視的 Azure 代理程式。
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: magoedte
ms.openlocfilehash: 89e73a4578134493a25ec3cbd2385433a2b36156
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775875"
---
# <a name="overview-of-the-azure-monitoring-agents"></a>Azure 監視代理程式概觀 
Microsoft Azure 提供了多種方法，可從裝載於 Azure、資料中心或其他雲端提供者中，且執行 Microsoft Windows 和 Linux 的虛擬機器收集不同類型的資料。 三種可用來監視 VM 的代理程式類型為：

* Azure 診斷擴充功能
* 適用於 Linux 和 Windows 的 Log Analytics 代理程式
* 相依性代理程式

本文說明這些代理程式的差異和功能，以便您判斷哪個代理程式可支援您的 IT 服務管理或一般監視需求。  

## <a name="azure-diagnostic-extension"></a>Azure 診斷擴充功能
[Azure 診斷擴充功能](../../azure-monitor/platform/diagnostics-extension-overview.md)這個代理程式先前稱為 Windows Azure Diagnostic (WAD) 或 Linux Azure Diagnostic (LAD) 擴充功能 (於 2010 年 Azure 雲端服務正式推出後提供)，可供從 Azure 計算資源 (例如 VM) 簡單收集診斷資料，並將它保存到 Azure 儲存體。 放入儲存體後，您便選擇使用其中一個可用工具進行檢視，例如 [Visual Studio 中的伺服器總管](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)和 [Azure 儲存體總管](../../vs-azure-tools-storage-manage-with-storage-explorer.md)。

您可以選擇收集：

* 一組預先定義的作業系統效能計數器和事件記錄，也您可以指定要收集的項目。 
* IIS Web 伺服器的所有要求和/或失敗要求
* .NET 應用程式追蹤輸出記錄
* Windows 事件追蹤 (ETW) 事件 
* 從 syslog 收集事件  
* 損毀傾印 

當您想要執行下列作業時，即應使用 Azure 診斷代理程式：

* 將記錄和計量封存到 Azure 儲存體
* 整合監視資料與第三方工具。 這些工具可使用多種不同的方法，包括查詢儲存體帳戶、轉送給[事件中樞](../../event-hubs/event-hubs-about.md)，或使用 [Azure 監視 REST API](../../azure-monitor/platform/rest-api-walkthrough.md) 進行查詢
* 將資料上傳至 Azure 監視器以在 Azure 入口網站中建立計量圖表，或建立幾近即時的[計量警示](../../azure-monitor/platform/alerts-metric-overview.md)。 
* 根據客體 OS 計量自動調整虛擬機器擴展集和傳統雲端服務。
* 使用[開機診斷](../../virtual-machines/troubleshooting/boot-diagnostics.md)調查 VM 的開機問題。
* 使用 [Application Insights](../../azure-monitor/overview.md) 了解您的應用程式表現如何，並主動識別對其造成影響的問題。
* 設定 Azure 監視器，以匯入的計量和記錄資料從雲端服務，傳統 Vm，收集並儲存在 Azure 儲存體帳戶的 Service Fabric 節點。

## <a name="log-analytics-agent"></a>Log Analytics 代理程式
進階監視您需要多個會收集計量和記錄的子集，（也稱為做為 Microsoft Monitoring Agent (MMA)） 的 Windows 和 Linux 的 Log Analytics 代理程式則是必要項目。 Log Analytics 代理程式的開發目的，是為了要全方位管理內部部署的實體和虛擬機器、System Center Operations Manager 所監視的電腦，和裝載在其他雲端中的 VM。 Windows 和 Linux 代理程式連接到 Azure 監視器來收集監視解決方案基礎的資料以及您所設定的自訂資料來源中的 Log Analytics 工作區。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

當您想要執行下列作業時，即應使用 Log Analytics 代理程式：

* 從 Azure、其他雲端提供者和內部部署資源的各種來源收集資料。 
* 使用其中一個 Azure 監視器監視解決方案，例如[適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md)、[適用於容器的 Azure 監視器](../insights/container-insights-overview.md)等等。  
* 使用其他 Azure 管理服務，例如 [Azure 資訊安全中心](../../security-center/security-center-intro.md)、[Azure 自動化](../../automation/automation-intro.md)等等。

過去已有數項 Azure 服務隨附於 *Operations Management Suite* 中，因此 Log Analytics 代理程式已可在一些服務間共用，包括 Azure 資訊安全中心和 Azure 自動化。  這融合了這些服務所提供的完整功能集，而讓您可在 Azure VM 的生命週期中對其進行全方位的管理。  其範例包括：

* 作業系統更新的 [Azure 自動化更新管理](../../automation/automation-update-management.md)。
* [Azure 自動化 Desired State Configuration](../../automation/automation-dsc-overview.md) 用以維護一致的組態狀態。
* 使用 [Azure 自動化變更追蹤與清查](../../automation/automation-change-tracking.md)來追蹤組態變更。
* [Application Insights](https://docs.microsoft.com/azure/application-insights/) 和 [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/)等 Azure 服務，這些服務會在原生狀態將其資料直接儲存在 Log Analytics 中。  

## <a name="dependency-agent"></a>相依性代理程式
服務對應解決方案，這不原本由 Microsoft 開發的一部分，被開發相依性代理程式。 [服務對應](../insights/service-map.md)並[Vm 的 Azure 監視器](../insights/vminsights-overview.md)需要相依性代理程式在 Windows 和 Linux 上的虛擬機器與它整合與 Log Analytics 代理程式以收集虛擬上執行的處理序的相關探索到的資料機器，且外部處理序的相依性。 它會將此資料儲存在 Log Analytics 工作區中，並以視覺化方式呈現探索到的互連的元件。

您可能需要適當組合這些代理程式，以監視您的 VM。 代理程式可作為 Azure 擴充功能並存地安裝，不過在 Linux 上，Log Analytics 代理程式*必須*先行安裝，否則安裝將會失敗。 

## <a name="next-steps"></a>後續步驟

- 請參閱 [Log Analytics 代理程式概觀](../../azure-monitor/platform/log-analytics-agent.md)，以檢閱將代理程式部署至 Azure、資料中心或其他雲端環境所裝載的機器時需符合哪些要求，以及支援的方法。

