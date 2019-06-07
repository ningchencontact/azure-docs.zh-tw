---
title: Azure 狀態監視器 v2 API 參考：啟用檢測引擎 |Microsoft Docs
description: 狀態監視器 v2 API 參考。 啟用 InstrumentationEngine。 監視網站效能，而不必重新部署網站。 使用 ASP.NET web 應用程式裝載於內部，在 Vm，或在 Azure 上。
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
ms.openlocfilehash: 1e30490dbd51f541afd0b7036769cfc638a75877
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514373"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>狀態監視器 v2 API:啟用 InstrumentationEngine (v0.2.1-alpha)

本文說明的 cmdlet 時的成員[Az.ApplicationMonitor PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)。

> [!IMPORTANT]
> 狀態監視器 v2 目前處於公開預覽狀態。
> 此預覽版不提供的服務等級協定，且我們不建議用於生產工作負載。 可能不支援某些功能，以及一些可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="description"></a>描述

藉由設定某些登錄機碼，可讓檢測引擎。
重新啟動 IIS，變更才會生效。

檢測引擎可以補充.NET Sdk 所收集的資料。
它會收集事件和描述執行受管理的處理序的訊息。 這些事件和訊息包括相依性的結果碼、 HTTP 動詞命令，以及 SQL 命令文字。

如果，請啟用檢測引擎：
- 您已啟用監視啟用 cmdlet 與，但未啟用檢測引擎。
- 您已手動檢測您的應用程式使用.NET Sdk，而且想要收集的其他遙測。

> [!IMPORTANT] 
> 此 cmdlet 需要系統管理員權限的 PowerShell 工作階段。

> [!NOTE] 
> - 此 cmdlet 需要您檢閱並接受我們的授權及隱私權聲明陳述式。
> - 檢測引擎會增加額外負荷，並預設為關閉。

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
 - [探索度量](../../azure-monitor/app/metrics-explorer.md)來監視效能和使用方式。
- [搜尋事件和記錄](../../azure-monitor/app/diagnostic-search.md)來診斷問題。
- 使用[analytics](../../azure-monitor/app/analytics.md)以進行進階的查詢。
- [建立儀表板](../../azure-monitor/app/overview-dashboard.md)。
 
 新增更多遙測：
 - [建立 web 測試](monitor-web-app-availability.md)藉此確定您的網站保持即時狀態。
- [新增 web 用戶端遙測](../../azure-monitor/app/javascript.md)以查看網頁程式碼中的例外狀況，並啟用追蹤呼叫。
- [將 Application Insights SDK 新增至您的程式碼](../../azure-monitor/app/asp-net.md)讓您插入追蹤和記錄呼叫。
 
 進一步運用狀態監視器 v2:
 - 使用我們的指南，來[疑難排解](status-monitor-v2-troubleshoot.md)狀態監視器 v2。
 - [取得設定](status-monitor-v2-api-get-config.md)以確認您的設定未正確地記錄。
 - [取得狀態](status-monitor-v2-api-get-status.md)檢查監視。
