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
ms.devlang: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: magoedte
ms.openlocfilehash: 4255b70746eb1d307617a66714301fa7f8609a77
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634263"
---
# <a name="overview-of-the-azure-monitoring-agents"></a>Azure 監視代理程式概觀 
Microsoft Azure 提供了多種方法，可從裝載於 Azure、資料中心或其他雲端提供者中，且執行 Microsoft Windows 和 Linux 的虛擬機器收集不同類型的資料。 三種可用來監視 VM 的代理程式類型為：

* Azure 診斷擴充功能
* 適用於 Linux 和 Windows 的 Log Analytics 代理程式
* 相依性代理程式

本文說明這些代理程式的差異和功能，以便您判斷哪個代理程式可支援您的 IT 服務管理或一般監視需求。  

## <a name="azure-diagnostic-extension"></a>Azure 診斷擴充功能
[Azure 診斷擴充功能](../../monitoring-and-diagnostics/azure-diagnostics.md)這個代理程式先前稱為 Windows Azure Diagnostic (WAD) 或 Linux Azure Diagnostic (LAD) 擴充功能 (於 2010 年 Azure 雲端服務正式推出後提供)，可供從 Azure 計算資源 (例如 VM) 簡單收集診斷資料，並將它保存到 Azure 儲存體。 放入儲存體後，您便選擇使用其中一個可用工具進行檢視，例如 [Visual Studio 中的伺服器總管](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)和 [Azure 儲存體總管](../../vs-azure-tools-storage-manage-with-storage-explorer.md)。

您可以選擇收集：

* 一組預先定義的作業系統效能計數器和事件記錄，也您可以指定要收集的項目。 
* IIS Web 伺服器的所有要求和/或失敗要求
* .NET 應用程式追蹤輸出記錄
* Windows 事件追蹤 (ETW) 事件 
* 從 syslog 收集事件  
* 損毀傾印 

當您想要執行下列作業時，即應使用 Azure 診斷代理程式：

* 將記錄和計量封存到 Azure 儲存體
* 整合監視資料與第三方工具。 這些工具可使用多種不同的方法，包括查詢儲存體帳戶、轉送給[事件中樞](../../event-hubs/event-hubs-about.md)，或使用 [Azure 監視 REST API](../../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md) 進行查詢
* 將資料上傳至 Azure 監視器以在 Azure 入口網站中建立計量圖表，或建立幾近即時的[計量警示](../../monitoring-and-diagnostics/alert-metric-overview.md)。 
* 根據客體 OS 計量自動調整虛擬機器擴展集和傳統雲端服務。
* 使用[開機診斷](../../virtual-machines/troubleshooting/boot-diagnostics.md)調查 VM 的開機問題。
* 使用 [Application Insights](../../azure-monitor/overview.md) 了解您的應用程式表現如何，並主動識別對其造成影響的問題。
* 設定 Log Analytics，以匯入從儲存在 Azure 儲存體帳戶中的雲端服務、傳統 VM 和 Service Fabric 節點收集到的計量和記錄資料。

## <a name="log-analytics-agent"></a>Log Analytics 代理程式
若要進行不只需要收集計量和一部分記錄的進階監視，一定要有適用於 Windows 和 Linux 的 Log Analytics 代理程式。 Log Analytics 代理程式的開發目的，是為了要全方位管理內部部署的實體和虛擬機器、System Center Operations Manager 所監視的電腦，和裝載在其他雲端中的 VM。 Windows 和 Linux 代理程式會連線到 Log Analytics 工作區，以收集以監視解決方案為基礎的資料，和您所設定的自訂資料來源。

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
相依性代理程式經開發後成為服務對應解決方案的一部分，而後者原本是在 Microsoft 外部開發的。 [服務對應](../insights/service-map.md)和[適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md)在 Windows 和 Linux 上必須要有相依性代理程式，此代理程式會與 Log Analytics 代理程式整合，以收集虛擬機器上所執行程序的相關探索資料，以及外部程序相依性。 Dependency Agent 會將此資料儲存在 Log Analytics 中，並以視覺化方式呈現探索到的互連元件。

您可能需要適當組合這些代理程式，以監視您的 VM。 代理程式可作為 Azure 擴充功能並存地安裝，不過在 Linux 上，Log Analytics 代理程式*必須*先行安裝，否則安裝將會失敗。 

## <a name="next-steps"></a>後續步驟

- 請參閱 [Log Analytics 代理程式概觀](../../azure-monitor/platform/log-analytics-agent.md)，以檢閱將代理程式部署至 Auzre、資料中心或其他雲端環境所裝載的機器時需符合哪些要求，以及支援的方法。

