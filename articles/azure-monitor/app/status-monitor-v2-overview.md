---
title: Azure 狀態監視器 v2 概觀 |Microsoft Docs
description: 狀態監視器 v2 概觀。 監視網站效能，而不必重新部署網站。 使用 ASP.NET web 應用程式裝載於內部，在 Vm，或在 Azure 上。
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
ms.openlocfilehash: 2126408222433e6339723dc2da0d2611bb234fe8
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734151"
---
# <a name="status-monitor-v2"></a>狀態監視器 v2

狀態監視器 v2 是模組發行至 PowerShell [PowerShell 資源庫](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)。
它會取代[狀態監視器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)。
模組提供 iis 裝載的.NET web 應用程式的無程式碼的檢測。
遙測會傳送至 Azure 入口網站中，您可以在其中[監視器](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)您的應用程式。

> [!IMPORTANT]
> 狀態監視器 v2 目前處於公開預覽狀態。
> 此預覽版不提供的服務等級協定，且我們不建議用於生產工作負載。 可能不支援某些功能，以及一些可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="powershell-gallery"></a>PowerShell 資源庫

「 PowerShell 資源庫位於此處： https://www.powershellgallery.com/packages/Az.ApplicationMonitor。


## <a name="instructions"></a>範例的指示
- 請參閱[快速入門指示](status-monitor-v2-get-started.md)開始以簡潔的程式碼範例。
- 請參閱[的詳細指示](status-monitor-v2-detailed-instructions.md)深入了解如何開始使用。

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

  *是*。 有多種方式可用來下載狀態監視器 v2。 如果您的電腦可以存取網際網路，您可以上架至 PowerShell 資源庫使用`-Proxy`參數。
您可以手動下載模組和其中一個在電腦上安裝，或直接使用它。
每個選項所述[的詳細指示](status-monitor-v2-detailed-instructions.md)。
  
- 如何確認啟用成功？

   沒有任何 cmdlet，以確認啟用成功。
我們建議您改用[即時計量](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)來快速判斷您的應用程式會傳送遙測。

   您也可以使用[Log Analytics](../log-query/get-started-portal.md)若要列出目前正在傳送遙測的所有雲端角色：
   ```Kusto
   union * | summarize count() by cloud_RoleName, cloud_RoleInstance
   ```

## <a name="next-steps"></a>後續步驟

檢視遙測：

* [探索度量](../../azure-monitor/app/metrics-explorer.md)來監視效能和使用方式。
* [搜尋事件和記錄](../../azure-monitor/app/diagnostic-search.md)來診斷問題。
* [使用分析](../../azure-monitor/app/analytics.md)以進行進階的查詢。
* [建立儀表板](../../azure-monitor/app/overview-dashboard.md)。

新增更多遙測：

* [建立 web 測試](monitor-web-app-availability.md)藉此確定您的網站保持即時狀態。
* [新增 web 用戶端遙測](../../azure-monitor/app/javascript.md)以查看網頁程式碼中的例外狀況，並啟用追蹤呼叫。
* [將 Application Insights SDK 新增至您的程式碼](../../azure-monitor/app/asp-net.md)讓您插入追蹤和記錄呼叫。

