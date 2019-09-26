---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/27/2019
ms.author: cynthn
ms.openlocfilehash: 11c9b2ea3ea054415f25f864651df28288aa0025
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266835"
---
您可以利用許多機會，藉由收集、檢視及分析診斷和記錄資料來監視 VM。 若要執行簡單的 VM [監視](../articles/azure-monitor/overview.md)，您可以在 Azure 入口網站中使用 VM 的 [概觀] 畫面。 您可以使用[擴充功能](../articles/virtual-machines/windows/extensions-features.md)來設定 VM 診斷，以收集其他計量資料。 您也可以使用更進階的監視選項，例如 [Application Insights](../articles/azure-monitor/app/app-insights-overview.md) 和 [Log Analytics](../articles/azure-monitor/log-query/log-query-overview.md)。

## <a name="diagnostics-and-metrics"></a>診斷和計量 

您可以在 Azure 入口網站、Azure CLI、Azure PowerShell 和應用程式設計介面 (API) 中使用[計量](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md)，設定和監視[診斷資料](https://docs.microsoft.com/cli/azure/vm/diagnostics)的收集。 例如，您可以：

- **觀察 VM 的基本計量。** 在 Azure 入口網站的 [概觀] 畫面上，顯示的基本計量包括 CPU 使用量、網路使用量、磁碟位元組總計，以及每秒的磁碟作業。

- **啟用開機診斷的收集，並使用 Azure 入口網站進行檢視。** 將自己的映像送至 Azure 或甚至啟動其中一個平台映像時，VM 進入不可開機狀態的原因有很多。 在 [設定] 畫面的 [監視] 區段之下按一下 [啟用] 開機診斷，即可在建立 VM 時輕鬆地啟用開機診斷。

    當 VM 開機，開機診斷代理程式會擷取開機輸出，並儲存在 Azure 儲存體。 這項資料可以用於 VM 開機問題的疑難排解。 當您從命令列工具建立 VM 時，不會自動啟用開機診斷。 啟用開機診斷之前，需要建立儲存體帳戶用來儲存開機記錄。 如果您在 Azure 入口網站中啟用開機診斷，系統會自動為您建立儲存體帳戶。

    如果您未在建立 VM 時啟用開機診斷，您稍後一律可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics)、[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic) 或 [Azure Resource Manager 範本](../articles/virtual-machines/windows/extensions-diagnostics-template.md)來啟用它。

- **啟用客體 OS 診斷資料的收集。** 當您建立 VM 時，您有機會在 [設定] 畫面上啟用客體 OS 診斷。 當您啟用診斷資料的收集時，[適用於 Linux 的 IaaSDiagnostics 擴充功能](../articles/virtual-machines/linux/diagnostic-extension.md)或[適用於 Windows 的 IaaSDiagnostics 擴充功能](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)會新增至 VM，以便您收集其他磁碟、CPU 和記憶體資料。

    使用所收集的診斷資料，即可設定 VM 的自動調整。 您也可以設定記錄來儲存資料及設定警示，讓您知道效能何時不太理想。

## <a name="alerts"></a>警示

您可以根據特定效能計量來建立[警示](../articles/azure-monitor/platform/alerts-overview.md)。 舉例來說，當平均 CPU 使用量超過特定臨界值，或可用的磁碟空間低於特定數量時，您即可接獲問題的警示。 您可以使用 [Azure PowerShell](../articles/azure-monitor/platform/alerts-classic-portal.md#with-powershell) 或 [Azure CLI](../articles/azure-monitor/platform/alerts-classic-portal.md#with-azure-cli)，在 [Azure 入口網站](../articles/azure-monitor/platform/alerts-classic-portal.md)中設定警示。

## <a name="azure-service-health"></a>Azure 服務健康狀態

[Azure 服務健康情況](../articles/service-health/service-health-overview.md)提供個人化的指導方針並在 Azure 服務問題影響到您時提供支援，同時協助您準備即將進行的已規劃維護。 「Azure 服務健康情況」會使用有目標且有彈性的通知來警示您和您的小組。

## <a name="azure-resource-health"></a>Azure 資源健康狀態

[Azure 資源健康情況](../articles/service-health/resource-health-overview.md)可協助您進行診斷，並在 Azure 問題影響您的資源時取得支援。 它會通知您資源的目前及過去的健康狀態，並協助您解決問題。 資源健康狀態會在您需要解決 Azure 服務問題時提供技術支援。

## <a name="azure-activity-log"></a>Azure 活動記錄檔

[Azure 活動記錄](../articles/azure-monitor/platform/activity-logs-overview.md)是訂用帳戶記錄，可讓您深入探索 Azure 中發生的訂用帳戶層級事件。 此記錄包含的資料範圍，從 Azure Resource Manager 作業資料到「服務健康情況」事件的更新。 您可以在 Azure 入口網站中按一下活動記錄，以檢視 VM 的記錄。

您可以利用活動記錄進行的事項包括：

- [根據活動記錄事件建立警示](../articles/azure-monitor/platform/activity-logs-overview.md)。
- [將活動記錄串流至事件中樞](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md)，以供第三方服務或自訂的分析解決方案 (如 PowerBI) 擷取。
- 使用 [PowerBI 內容套件](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)在 PowerBI 中進行分析。
- [將活動記錄儲存到儲存體帳戶](../articles/azure-monitor/platform/archive-activity-log.md)，以供封存或手動檢查。 您可以使用記錄設定檔來指定保留時間 (以天為單位)。

您也可以使用 [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/)、[Azure CLI](https://docs.microsoft.com/cli/azure/monitor) 或[監視器 REST API](https://docs.microsoft.com/rest/api/monitor/)，存取活動記錄資料。

[Azure 資源記錄](../articles/azure-monitor/platform/resource-logs-overview.md)是您的 VM 所發出的記錄，可提供有關其作業的豐富、經常性資料。 資源記錄與活動記錄的差異，在於提供在 VM 內執行之作業的深入解析。

您可以利用診斷記錄進行的事項包括：

- [將診斷記錄儲存到儲存體帳戶](../articles/azure-monitor/platform/archive-diagnostic-logs.md)以利稽核或手動檢查。 您可以使用 [資源診斷設定] 來指定保留時間 (以天為單位)。
- [將診斷記錄串流至事件中樞](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md)，以供第三方服務或自訂的分析解決方案 (如 PowerBI) 擷取。
- 使用 [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md) 分析診斷記錄。

## <a name="advanced-monitoring"></a>進階監視

- [Azure 監視器](../articles/azure-monitor/overview.md)是一項服務，可監視您的雲端和內部部署環境，以維護其可用性和效能。 它可提供全方位解決方案，以便收集、分析及處理來自雲端和內部部署環境的遙測資料。 它可協助您了解您的應用程式表現如何，並主動識別影響它們的問題以及它們所依賴的資源。 您可以在安裝 Log Analytics 代理程式的 [Linux VM](../articles/virtual-machines/linux/extensions-oms.md) 或 [Windows VM](../articles/virtual-machines/windows/extensions-oms.md) 上安裝擴充功能，以收集記錄資料並儲存在 Log Analytics 工作區中。

    針對 Windows 和 Linux VM，收集記錄的建議方式是安裝 Log Analytics 代理程式。 在 VM 上安裝 Log Analytics 代理程式的最簡單方式是透過 [Log Analytics VM 擴充功能](../articles/log-analytics/log-analytics-azure-vm-extension.md)。 使用擴充可以簡化安裝程序，並自動設定代理程式將資料傳送到您指定的 Log Analytics 工作區。 代理程式也會自動升級，以確保您擁有最新的功能和修正程式。

- [網路監看員](../articles/network-watcher/network-watcher-monitoring-overview.md)可讓您監視 VM 及其相關聯的資源，因為這些資源與其所在的網路相關。 您可以將網路監看員代理程式擴充功能安裝於 [Linux VM](../articles/virtual-machines/linux/extensions-nwa.md) 或 [Windows VM](../articles/virtual-machines/windows/extensions-nwa.md) 上。

- [適用於 VM 的 Azure 監視器](../articles/azure-monitor/insights/vminsights-overview.md)可藉由分析 Windows 和 Linux VM 的效能和健康情況 (包括其不同的程序以及其他資源和外部程序的互連相依性)，大規模監視 Azure 虛擬機器 (VM)。 

## <a name="next-steps"></a>後續步驟
- 逐步執行[使用 Azure PowerShell 監視 Windows 虛擬機器](../articles/virtual-machines/windows/tutorial-monitoring.md)或[使用 Azure CLI 監視 Linux 虛擬機器](../articles/virtual-machines/linux/tutorial-monitoring.md)中的步驟。
- 深入了解[監視和診斷](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)的最佳做法。
