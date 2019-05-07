---
title: Azure 狀態監視器 v2 概觀 |Microsoft Docs
description: 狀態監視器 v2 概觀。 監視網站效能，而不必重新部署網站。 使用裝載於內部部署、VM 中或 Azure 上的 ASP.NET Web 應用程式。
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 77c2cd9e0eac6717d91a73a6fc033dcaa2390444
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145035"
---
# <a name="status-monitor-v2"></a>狀態監視器 v2

狀態監視器 v2 是發行至 PowerShell 模組[powershell 資源庫](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)會取代並[狀態監視器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)。 此模組會提供與 IIS 裝載的.NET web 應用程式的無程式碼的檢測。
遙測會傳送至 Azure 入口網站，您可以在[監視器](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)您的應用程式。

> [!IMPORTANT]
> 狀態監視器 v2 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱[補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="powershell-gallery"></a>PowerShell 資源庫

https://www.powershellgallery.com/packages/Az.ApplicationMonitor


## <a name="instructions"></a>範例的指示
- 檢閱我們[快速入門指示](status-monitor-v2-get-started.md)立即開始使用簡潔的程式碼範例。
- 檢閱我們[的詳細指示](status-monitor-v2-detailed-instructions.md)深入了解如何開始使用。

## <a name="powershell-api-reference"></a>PowerShell API 參考
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)

## <a name="troubleshooting"></a>疑難排解
- [疑難排解](status-monitor-v2-troubleshoot.md)
- [已知問題](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>常見問題集

- 狀態監視器 v2 是否支援 proxy 安裝？

  **是**。 您有多個選項，若要下載狀態監視器 v2。 如果您的電腦具有網際網路存取，即可將產品上架到 PowerShell 資源庫時使用`-Proxy`參數。 您可以手動下載此模組，並請將它安裝在電腦上或直接使用的模組。 中所述的每個選項我們[的詳細指示](status-monitor-v2-detailed-instructions.md)。
  
- 如何確認啟用成功？

   我們不需要的 cmdlet 以確認該啟用成功。 我們建議您使用[即時計量](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)來快速地觀察 是否您的應用程式會傳送給我們的遙測。

## <a name="next-steps"></a>後續步驟

檢視遙測：

* [探索計量](../../azure-monitor/app/metrics-explorer.md)以監視效能和使用量
* [搜尋事件和記錄](../../azure-monitor/app/diagnostic-search.md)來診斷問題
* 更多進階查詢的[分析](../../azure-monitor/app/analytics.md)
* [建立儀表板](../../azure-monitor/app/app-insights-dashboards.md)

新增更多遙測：

* [建立 web 測試](monitor-web-app-availability.md)藉此確定您的網站保持即時狀態。
* [新增 web 用戶端遙測](../../azure-monitor/app/javascript.md)以查看網頁程式碼中的例外狀況，並讓您插入追蹤呼叫。
* [將 Application Insights SDK 新增至您的程式碼](../../azure-monitor/app/asp-net.md)，讓您插入追蹤和記錄呼叫

