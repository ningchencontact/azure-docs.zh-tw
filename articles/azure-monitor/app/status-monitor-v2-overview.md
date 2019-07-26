---
title: Azure 狀態監視器 v2 總覽 |Microsoft Docs
description: 狀態監視器 v2 的總覽。 在不重新部署網站的情況下監視網站效能。 適用于內部部署、Vm 或 Azure 上裝載的 ASP.NET web 應用程式。
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
ms.openlocfilehash: 0264cf3a972c35edb3ad6dc600ca39bdaa076dfd
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333697"
---
# <a name="status-monitor-v2"></a>狀態監視器 v2

狀態監視器 v2 是發佈至[PowerShell 資源庫](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)的 PowerShell 模組。
它會取代[狀態監視器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)。
此模組提供裝載于 IIS 之 .NET web 應用程式的無程式碼檢測。
遙測會傳送至 Azure 入口網站, 您可以在其中[監視](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)您的應用程式。

## <a name="powershell-gallery"></a>PowerShell 資源庫

狀態監視器 v2 位於此處: https://www.powershellgallery.com/packages/Az.ApplicationMonitor 。

![PowerShell 資源庫](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>指示
- 請參閱開始使用的[指示](status-monitor-v2-get-started.md), 以取得簡單的程式碼範例。
- 如需深入瞭解如何開始使用, 請參閱[詳細指示](status-monitor-v2-detailed-instructions.md)。

## <a name="powershell-api-reference"></a>PowerShell API 參考
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [開始-ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>疑難排解
- [疑難排解](status-monitor-v2-troubleshoot.md)
- [已知問題](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>常見問題集

- 狀態監視器 v2 是否支援 proxy 安裝？

  *是*。 有多種方式可以下載狀態監視器 v2。 如果您的電腦可存取網際網路, 您可以使用`-Proxy`參數上架到 PowerShell 資源庫。
您也可以手動下載模組, 並將它安裝在您的電腦上, 或直接使用它。
每個選項都會在[詳細指示](status-monitor-v2-detailed-instructions.md)中加以說明。
  
- 如何? 確認啟用成功嗎？

  - [ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)指令程式可以用來驗證啟用是否成功。
  - 建議您使用[即時計量](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)來快速判斷您的應用程式是否正在傳送遙測。

  - 您也可以使用[Log Analytics](../log-query/get-started-portal.md)來列出目前正在傳送遙測的所有雲端角色:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>後續步驟

檢視遙測：

* [探索計量](../../azure-monitor/app/metrics-explorer.md)以監視效能和使用量。
* [搜尋事件和記錄](../../azure-monitor/app/diagnostic-search.md)以診斷問題。
* [流量分析](../../azure-monitor/app/analytics.md)進行更先進的查詢。
* [建立儀表板](../../azure-monitor/app/overview-dashboard.md)。

新增更多遙測：

* [建立 web 測試](monitor-web-app-availability.md), 以確保您的網站保持上線。
* [新增 web 用戶端遙測](../../azure-monitor/app/javascript.md), 以查看來自網頁程式碼的例外狀況, 並啟用追蹤呼叫。
* [將 APPLICATION INSIGHTS SDK 新增至您的程式碼](../../azure-monitor/app/asp-net.md), 讓您可以插入追蹤和記錄呼叫。

