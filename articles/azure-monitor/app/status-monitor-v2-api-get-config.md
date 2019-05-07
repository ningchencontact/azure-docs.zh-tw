---
title: Azure 狀態監視器 v2 API 參考：取得設定 |Microsoft Docs
description: 狀態監視器 v2 API 參考 Get-ApplicationInsightsMonitoringConfig。 監視網站效能，而不必重新部署網站。 使用裝載於內部部署、VM 中或 Azure 上的 ASP.NET Web 應用程式。
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
ms.openlocfilehash: 28893e0782a7fa928757bbbba42662e6d3fa272c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153568"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringconfig-v021-alpha"></a>狀態監視器 v2 API:Get-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

本文件說明 cmdlet 所隨附的成員身分[Az.ApplicationMonitor PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)。

> [!IMPORTANT]
> 狀態監視器 v2 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱[補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>描述

取得組態檔，並列印到主控台的值。

> [!IMPORTANT] 
> 此 cmdlet 需要系統管理員權限的 PowerShell 工作階段。

## <a name="examples"></a>範例

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>參數 

（沒有所需的參數）

## <a name="output"></a>輸出


#### <a name="example-output-from-reading-the-config-file"></a>讀取組態檔的範例輸出

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>後續步驟

  檢視遙測：
 - [探索計量](../../azure-monitor/app/metrics-explorer.md)以監視效能和使用量
- [搜尋事件和記錄](../../azure-monitor/app/diagnostic-search.md)來診斷問題
- 更多進階查詢的[分析](../../azure-monitor/app/analytics.md)
- [建立儀表板](../../azure-monitor/app/app-insights-dashboards.md)
 
 新增更多遙測：
 - [建立 web 測試](monitor-web-app-availability.md)藉此確定您的網站保持即時狀態。
- [新增 web 用戶端遙測](../../azure-monitor/app/javascript.md)以查看網頁程式碼中的例外狀況，並讓您插入追蹤呼叫。
- [將 Application Insights SDK 新增至您的程式碼](../../azure-monitor/app/asp-net.md)，讓您插入追蹤和記錄呼叫
 
 進一步運用狀態監視器 v2:
 - 使用我們的指南，來[疑難排解](status-monitor-v2-troubleshoot.md)狀態監視器 v2。
 - 若要使用此設定進行變更[設定組態](status-monitor-v2-api-set-config.md)cmdlet。
