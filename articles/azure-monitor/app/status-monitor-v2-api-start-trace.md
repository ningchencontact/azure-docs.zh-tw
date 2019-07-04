---
title: Azure 狀態監視器 v2 API 參考：啟動追蹤 |Microsoft Docs
description: 狀態監視器 v2 API 參考。 開始追蹤。 狀態監視器與 Application Insights SDK 收集 ETW 記錄檔。
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
ms.openlocfilehash: 186324e6a93f90fd04323074e5dc883e3e271725
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478988"
---
# <a name="status-monitor-v2-api-start-applicationinsightsmonitoringtrace-v031-alpha"></a>狀態監視器 v2 API:Start-ApplicationInsightsMonitoringTrace (v0.3.1-alpha)

本文說明的 cmdlet 時的成員[Az.ApplicationMonitor PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)。

> [!IMPORTANT]
> 狀態監視器 v2 目前處於公開預覽狀態。
> 此預覽版不提供的服務等級協定，且我們不建議用於生產工作負載。 可能不支援某些功能，以及一些可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="description"></a>描述

會收集[ETW 事件](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal)無程式碼從連結執行階段。 這個指令程式是執行替代[PerfView](https://github.com/microsoft/perfview)。

收集的事件將列印到主控台中，以即時和已儲存到 ETL 檔案。 輸出 ETL 檔案可以開啟[PerfView](https://github.com/microsoft/perfview)以便進一步調查。

這個 cmdlet 會執行，直到達到逾時持續期間 （預設為 5 分鐘） 或手動停止為止 (`Ctrl + C`)。

> [!IMPORTANT] 
> 此 cmdlet 需要系統管理員權限的 PowerShell 工作階段。

## <a name="examples"></a>範例

### <a name="how-to-collect-events"></a>如何收集事件

通常我們會要求您收集事件，以調查為何未檢測您的應用程式。

無程式碼附加的執行階段和 IIS 啟動時啟動您的應用程式時，就會發出 ETW 事件。

若要收集這些事件：
1. 以系統管理員權限的 cmd 主控台，在執行`iisreset /stop`關閉 IIS 與所有 web 應用程式。
2. 執行這個指令程式
3. 以系統管理員權限的 cmd 主控台，在執行`iisreset /start`啟動 IIS。
4. 嘗試瀏覽至您的應用程式。
5. 您的應用程式完成載入之後，您可以手動停止它 (`Ctrl + C`) 或等候逾時。

### <a name="what-events-to-collect"></a>若要收集哪些事件

收集事件時，您會有三個選項：
1. 使用參數`-CollectSdkEvents`收集 Application Insights SDK 所發出的事件。
2. 使用參數`-CollectRedfieldEvents`收集狀態監視器和 Redfield 執行階段所發出的事件。 這些記錄是很有幫助診斷 IIS 時，應用程式啟動。
3. 您可以使用這兩個參數來收集這兩種事件類型。
4. 根據預設，如果未指定參數將會收集這兩種事件類型。


## <a name="parameters"></a>參數

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**選用。** 您可以使用這個參數來設定此指令碼應該收集事件的時間長度。 預設值為 5 分鐘。

### <a name="-logdirectory"></a>-LogDirectory
**選用。** 您可以使用這個參數來設定 ETL 檔案的輸出目錄。 根據預設，將 PowerShell 模組目錄中建立這個檔案。 指令碼執行期間，將會顯示完整的路徑。


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**選用。** 您可以使用此參數可以收集 Application Insights SDK 的事件。

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**選用。** 收集事件狀態監視器與 Redfield 執行階段使用此參數。

### <a name="-verbose"></a>-Verbose
**常見的參數。** 您可以使用這個參數，輸出的詳細的記錄。



## <a name="output"></a>Output


### <a name="example-of-application-startup-logs"></a>應用程式啟動記錄檔的範例
```
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```


## <a name="next-steps"></a>後續步驟

其他的疑難排解：

- 檢閱其他疑難排解的步驟： https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- 檢閱[API 參考](status-monitor-v2-overview.md#powershell-api-reference)若要了解您可能已漏掉的參數。
- 如果您需要其他協助，您就可以與我們連絡上[GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)。



 進一步運用狀態監視器 v2:
 - 使用我們的指南，來[疑難排解](status-monitor-v2-troubleshoot.md)狀態監視器 v2。
 - [取得設定](status-monitor-v2-api-get-config.md)以確認您的設定未正確地記錄。
 - [取得狀態](status-monitor-v2-api-get-status.md)檢查監視。
