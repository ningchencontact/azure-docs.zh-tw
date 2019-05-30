---
title: Azure 狀態監視器 v2 API 參考：啟用檢測引擎 |Microsoft Docs
description: 狀態監視器 v2 API 參考啟用-InstrumentationEngine。 監視網站效能，而不必重新部署網站。 使用裝載於內部部署、VM 中或 Azure 上的 ASP.NET Web 應用程式。
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
ms.openlocfilehash: e993378634262de25449975431c0a9e3145ca9fb
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255253"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>狀態監視器 v2 API:啟用 InstrumentationEngine (v0.2.1-alpha)

本文件說明 cmdlet 所隨附的成員身分[Az.ApplicationMonitor PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)。

> [!IMPORTANT]
> 狀態監視器 v2 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱[補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>描述

此 cmdlet 會藉由設定某些登錄機碼啟用檢測引擎。
重新啟動 IIS，這些變更才會生效。

檢測引擎可以補充.NET Sdk 所收集的資料。
事件和訊息將會收集描述 managed 處理序執行。 其中包括但不是限於相依性的結果碼、 HTTP 指令動詞，與 SQL 命令文字。 

如果，請啟用檢測引擎：
- 您已啟用監視使用啟用指令程式，但未啟用 InstrumentationEngine。
- 您已手動檢測您的應用程式使用.NET Sdk，而且想要收集的其他遙測。

> [!IMPORTANT] 
> 此 cmdlet 需要系統管理員權限的 PowerShell 工作階段。

> [!NOTE] 
> 此 cmdlet 會要求您檢閱並接受我們的授權及隱私權聲明陳述式。

> [!NOTE] 
> 檢測引擎會增加額外負荷，並預設為關閉。

## <a name="examples"></a>範例

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>參數 

### <a name="-acceptlicense"></a>-AcceptLicense
**選用。** 您可以使用此參數可以接受授權及隱私權聲明中的陳述式無周邊的安裝。

### <a name="-verbose"></a>-Verbose
**常見的參數。** 您可以使用這個參數，輸出的詳細的記錄。

## <a name="output"></a>輸出


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>已成功啟用檢測引擎的範例輸出

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>後續步驟

  檢視遙測：
 - [探索計量](../../azure-monitor/app/metrics-explorer.md)以監視效能和使用量
- [搜尋事件和記錄](../../azure-monitor/app/diagnostic-search.md)來診斷問題
- 更多進階查詢的[分析](../../azure-monitor/app/analytics.md)
- [建立儀表板](../../azure-monitor/app/overview-dashboard.md)
 
 新增更多遙測：
 - [建立 web 測試](monitor-web-app-availability.md)藉此確定您的網站保持即時狀態。
- [新增 web 用戶端遙測](../../azure-monitor/app/javascript.md)以查看網頁程式碼中的例外狀況，並讓您插入追蹤呼叫。
- [將 Application Insights SDK 新增至您的程式碼](../../azure-monitor/app/asp-net.md)，讓您插入追蹤和記錄呼叫
 
 進一步運用狀態監視器 v2:
 - 使用我們的指南，來[疑難排解](status-monitor-v2-troubleshoot.md)狀態監視器 v2。
 - [取得設定](status-monitor-v2-api-get-config.md)以確認您的設定未正確地記錄。
 - [取得狀態](status-monitor-v2-api-get-status.md)檢查監視。
