---
title: Azure 狀態監視器 v2 API 參考：若要啟用監視 |Microsoft Docs
description: 狀態監視器 v2 API 參考。 啟用 ApplicationInsightsMonitoring。 監視網站效能，而不必重新部署網站。 使用 ASP.NET web 應用程式裝載於內部，在 Vm，或在 Azure 上。
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
ms.openlocfilehash: e87bfad11eee5b86d35e6b4f2846b094c467e0ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734174"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>狀態監視器 v2 API:啟用 ApplicationInsightsMonitoring (v0.2.1-alpha)

本文說明的 cmdlet 時的成員[Az.ApplicationMonitor PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)。

> [!IMPORTANT]
> 狀態監視器 v2 目前處於公開預覽狀態。
> 此預覽版不提供的服務等級協定，且我們不建議用於生產工作負載。 可能不支援某些功能，以及一些可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="description"></a>描述

啟用無程式碼會附加在目標電腦上的 IIS 應用程式的監視。

此 cmdlet 會修改 IIS applicationHost.config，並設定一些登錄機碼。
它也會建立一個 applicationinsights.ikey.config 檔案，定義每個應用程式所使用的檢測金鑰。
IIS 會載入在啟動時，這會是將 Application Insights SDK 插入應用程式，當應用程式開始 RedfieldModule。
重新啟動 IIS，您的變更才會生效。

啟用監視之後，我們建議您使用[即時計量](live-stream.md)快速檢查 如果您的應用程式會傳送給我們的遙測。


> [!NOTE] 
> - 若要開始，您需要檢測金鑰。 如需詳細資訊，請參閱 <<c0> [ 建立資源](create-new-resource.md#copy-the-instrumentation-key)。
> - 此 cmdlet 需要您檢閱並接受我們的授權及隱私權聲明陳述式。

> [!IMPORTANT] 
> 此 cmdlet 需要系統管理員權限的 PowerShell 工作階段和更高的執行原則。 如需詳細資訊，請參閱 <<c0> [ 提升權限的執行原則的系統管理員身分執行的 PowerShell](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)。

## <a name="examples"></a>範例

### <a name="example-with-a-single-instrumentation-key"></a>使用單一的檢測金鑰的範例
在此範例中，目前的電腦上的所有應用程式會指派單一檢測金鑰。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>使用檢測索引鍵對應的範例
在此範例中：
- `MachineFilter` 使用符合目前的電腦`'.*'`萬用字元。
- `AppFilter='WebAppExclude'` 提供`null`檢測金鑰。 指定的應用程式將不會進行檢測。
- `AppFilter='WebAppOne'` 將指定的應用程式指派唯一的檢測金鑰。
- `AppFilter='WebAppTwo'` 將指定的應用程式指派唯一的檢測金鑰。
- 最後，`AppFilter`也會使用`'.*'`萬用字元比對所有的 web 應用程式，不符合先前的規則，並指派預設的檢測金鑰。
- 加入空格，以提高可讀性。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>參數

### <a name="-instrumentationkey"></a>-InstrumentationKey
**必要。** 您可以使用這個參數來提供單一的檢測金鑰，供目標電腦上的所有應用程式。

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**必要。** 您可以使用這個參數來提供多個檢測金鑰和對應的每個應用程式所使用的檢測金鑰。
您可以建立多部電腦的單一安裝指令碼，藉由設定`MachineFilter`。

> [!IMPORTANT]
> 應用程式會比對規則提供規則的順序。 因此您應該先指定最特殊的規則，並持續最一般的規則。

#### <a name="schema"></a>結構描述
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter**需要C#的電腦或 VM 名稱的 regex。
    - '。 *' 會比對所有
    - 'ComputerName' 會使用指定的確切名稱符合的電腦。
- **AppFilter**需要C#的電腦或 VM 名稱的 regex。
    - '。 *' 會比對所有
    - 'ApplicationName' 會使用指定的確切名稱符合只為 IIS 應用程式。
- **InstrumentationKey** ，才可啟用監視的應用程式符合上述兩個篩選條件。
    - 將此值保留為 null，如果您想要定義規則，以排除監視。


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**選用。** 使用此參數來啟用檢測引擎來收集事件和相關的受管理的程序執行期間發生的訊息。 這些事件和訊息包括相依性的結果碼、 HTTP 動詞命令，以及 SQL 命令文字。

檢測引擎會增加額外負荷，並預設為關閉。

### <a name="-acceptlicense"></a>-AcceptLicense
**選用。** 您可以使用此參數可以接受授權及隱私權聲明中的陳述式無周邊的安裝。

### <a name="-verbose"></a>-Verbose
**常見的參數。** 使用此參數來顯示詳細的記錄檔。

### <a name="-whatif"></a>-WhatIf 
**常見的參數。** 測試並驗證您的輸入的參數，而不實際啟用 監視中使用此參數。

## <a name="output"></a>輸出


#### <a name="example-output-from-a-successful-enablement"></a>成功啟用的範例輸出

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="next-steps"></a>後續步驟

  檢視遙測：
 - [探索度量](../../azure-monitor/app/metrics-explorer.md)來監視效能和使用方式。
- [搜尋事件和記錄](../../azure-monitor/app/diagnostic-search.md)來診斷問題。
- [使用分析](../../azure-monitor/app/analytics.md)以進行進階的查詢。
- [建立儀表板](../../azure-monitor/app/overview-dashboard.md)。
 
 新增更多遙測：
 - [建立 web 測試](monitor-web-app-availability.md)藉此確定您的網站保持即時狀態。
- [新增 web 用戶端遙測](../../azure-monitor/app/javascript.md)以查看網頁程式碼中的例外狀況，並啟用追蹤呼叫。
- [將 Application Insights SDK 新增至您的程式碼](../../azure-monitor/app/asp-net.md)讓您插入追蹤和記錄呼叫。
 
 進一步運用狀態監視器 v2:
 - 使用我們的指南，來[疑難排解](status-monitor-v2-troubleshoot.md)狀態監視器 v2。
 - [取得設定](status-monitor-v2-api-get-config.md)以確認您的設定未正確地記錄。
 - [取得狀態](status-monitor-v2-api-get-status.md)檢查監視。
