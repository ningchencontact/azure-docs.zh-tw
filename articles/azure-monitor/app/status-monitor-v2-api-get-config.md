---
title: Azure 狀態監視器 v2 API 參考:取得設定 |Microsoft Docs
description: 狀態監視器 v2 API 參考。 Get-ApplicationInsightsMonitoringConfig. 在不重新部署網站的情況下監視網站效能。 適用于內部部署、Vm 或 Azure 上裝載的 ASP.NET web 應用程式。
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
ms.openlocfilehash: 9e1b3242a96bfaadfe6f791e0ca9bf25262065e2
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326337"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringconfig"></a>狀態監視器 v2 API:ApplicationInsightsMonitoringConfig

本文說明的 Cmdlet 是[ApplicationMonitor PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成員。

## <a name="description"></a>描述

取得設定檔, 並將值列印到主控台。

> [!IMPORTANT] 
> 此 Cmdlet 需要具有系統管理員許可權的 PowerShell 會話。

## <a name="examples"></a>範例

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>參數

不需要任何參數。

## <a name="output"></a>Output


#### <a name="example-output-from-reading-the-config-file"></a>讀取設定檔案的輸出範例

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>後續步驟

  檢視遙測：
 - [探索計量](../../azure-monitor/app/metrics-explorer.md)以監視效能和使用量。
- [搜尋事件和記錄](../../azure-monitor/app/diagnostic-search.md)以診斷問題。
- 使用[分析](../../azure-monitor/app/analytics.md)進行更先進的查詢。
- [建立儀表板](../../azure-monitor/app/overview-dashboard.md)。
 
 新增更多遙測：
 - [建立 web 測試](monitor-web-app-availability.md), 以確保您的網站保持上線。
- [新增 web 用戶端遙測](../../azure-monitor/app/javascript.md), 以查看來自網頁程式碼的例外狀況, 並啟用追蹤呼叫。
- [將 APPLICATION INSIGHTS SDK 新增至您的程式碼](../../azure-monitor/app/asp-net.md), 讓您可以插入追蹤和記錄呼叫。
 
 使用狀態監視器 v2 來執行更多動作:
 - 使用我們的指南進行狀態監視器 v2 的[疑難排解](status-monitor-v2-troubleshoot.md)。
 - 使用[Set config](status-monitor-v2-api-set-config.md) Cmdlet 對設定進行變更。
