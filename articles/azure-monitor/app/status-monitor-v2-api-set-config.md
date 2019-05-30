---
title: Azure 狀態監視器 v2 API 參考：設定組態 |Microsoft Docs
description: 狀態監視器 v2 API 的參考集-ApplicationInsightsMonitoringConfig。 監視網站效能，而不必重新部署網站。 使用裝載於內部部署、VM 中或 Azure 上的 ASP.NET Web 應用程式。
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
ms.openlocfilehash: 971fea76a23859f32437a1698b6d3094113737a1
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255103"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>狀態監視器 v2 API:Set-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

本文件說明 cmdlet 所隨附的成員身分[Az.ApplicationMonitor PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)。

> [!IMPORTANT]
> 狀態監視器 v2 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱[補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>描述

設定組態檔，而不需要重複完整重新安裝。 重新啟動 IIS，您的變更才會生效。

> [!IMPORTANT] 
> 此 cmdlet 需要系統管理員權限的 PowerShell 工作階段。


## <a name="examples"></a>範例

### <a name="example-with-single-instrumentation-key"></a>使用單一的檢測金鑰的範例
在此範例中，目前的電腦上的所有應用程式將會指派單一檢測金鑰。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-instrumentation-key-map"></a>使用檢測索引鍵對應的範例
在此範例中， 
- `MachineFilter` 會比對目前的電腦使用`'.*'`萬用字元。
- `AppFilter='WebAppExclude'` 提供`null`InstrumentationKey。 此應用程式將不會進行檢測。
- `AppFilter='WebAppOne'` 將會指派此特定的應用程式的唯一檢測金鑰。
- `AppFilter='WebAppTwo'` 也會將指派此特定的應用程式的唯一檢測金鑰。
- 最後，`AppFilter`也會使用`'.*'`萬用字元來符合所有其他 web 應用程式不符合先前的規則，並指派預設的檢測金鑰。
- 只為了便於閱讀的空間。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>參數 

### <a name="-instrumentationkey"></a>-InstrumentationKey
**必要。** 您可以使用這個參數來提供在目標電腦上的所有應用程式使用單一 iKey。

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**必要。** 您可以使用這個參數來提供多個 ikey，以及要使用的 ikey 的應用程式對應。 您可以藉由設定 MachineFilter 建立多部電腦的單一安裝指令碼。 

> [!IMPORTANT] 
> 應用程式會比對規則，它們會提供的順序。 因此您應該先指定最特殊的規則，並持續最一般的規則。

#### <a name="schema"></a>結構描述
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter**是必要的 c# regex 的電腦或 vm 名稱。
    - '。 *' 會比對所有
    - 'ComputerName'，會比對只具有相同名稱的電腦。
- **AppFilter**是必要的 c# regex 的電腦或 vm 名稱。
    - '。 *' 會比對所有
    - 'ApplicationName' 會比對只具有相同名稱的 IIS 應用程式。
- **InstrumentationKey** ，才可啟用符合上述兩個篩選條件的應用程式的監視。
    - 將此值保留為 null，如果您想要定義規則，以排除監視


### <a name="-verbose"></a>-Verbose
**常見的參數。** 您可以使用這個參數，輸出的詳細的記錄。


## <a name="output"></a>輸出

依預設無輸出。

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>範例詳細資訊輸出設定-InstrumentationKey 透過組態檔

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>範例詳細資訊輸出設定-InstrumentationKeyMap 透過組態檔

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
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
