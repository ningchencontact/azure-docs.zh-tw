---
title: Azure 狀態監視器 v2 疑難排解和已知的問題 |Microsoft Docs
description: 狀態監視器 v2 和疑難排解範例的已知的問題。 監視網站效能，而不必重新部署網站。 使用 ASP.NET web 應用程式裝載於內部，在 Vm，或在 Azure 上。
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
ms.openlocfilehash: df59766ce38ac81568570cd6544ee28808ff8249
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807019"
---
# <a name="troubleshooting-status-monitor-v2"></a>疑難排解狀態監視 v2

當您啟用監視時，您可能會遇到問題，讓資料收集。
這篇文章會列出所有已知的問題，並提供疑難排解的範例。
如果您遇到此處未列出的問題時，您就可以與我們連絡上[GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)。


> [!IMPORTANT]
> 狀態監視器 v2 目前處於公開預覽狀態。
> 此預覽版不提供的服務等級協定，且我們不建議用於生產工作負載。 可能不支援某些功能，以及一些可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="known-issues"></a>已知問題

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>應用程式的 bin 目錄中有衝突的 Dll

如果所有這些 Dll 的 bin 目錄中存在時，監視可能會失敗：

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

部分這些 Dll 包含在 Visual Studio 預設應用程式範本中，即使您的應用程式不會使用它們。
若要查看根源的行為，您可以使用疑難排解工具：

- PerfView:
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset 和應用程式載入 （不含遙測）。 調查使用 Sysinternals （Handle.exe 和 ListDLLs.exe）：
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>使用 IIS 共用組態衝突

如果您有 web 伺服器叢集，您也可以使用[共用的設定](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)。
HttpModule 無法插入此共用的設定。
每一部伺服器的 gac 中安裝 DLL 的每個 web 伺服器上執行 [啟用] 命令。

執行 [啟用] 命令之後，請完成下列步驟：
1. 移至共用的設定目錄，並尋找 applicationHost.config 檔案。
2. 您組態的模組區段中加入這一行：
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
## <a name="troubleshooting"></a>疑難排解
    
### <a name="troubleshooting-powershell"></a>PowerShell 疑難排解

#### <a name="determine-which-modules-are-available"></a>判斷哪些模組可供
您可以使用`Get-Module -ListAvailable`命令來判斷已安裝哪些模組。

#### <a name="import-a-module-into-the-current-session"></a>將模組匯入目前工作階段
如果尚未載入模組的 PowerShell 工作階段，您可以手動載入它使用`Import-Module <path to psd1>`命令。


### <a name="troubleshooting-the-status-monitor-v2-module"></a>疑難排解狀態監視器 v2 模組

#### <a name="list-the-commands-available-in-the-status-monitor-v2-module"></a>列出狀態監視器 v2 模組中可用的命令
執行命令`Get-Command -Module Az.ApplicationMonitor`以取得可用的命令：

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-status-monitor-v2-module"></a>判斷目前的狀態監視器 v2 模組版本
執行`Get-ApplicationInsightsMonitoringStatus`命令，以顯示下列模組的相關資訊：
   - PowerShell 模組版本
   - Application Insights SDK 版本
   - PowerShell 模組的檔案路徑
    
檢閱[API 參考](status-monitor-v2-api-get-status.md)如如何使用此 cmdlet 的詳細描述。


### <a name="troubleshooting-running-processes"></a>疑難排解執行中處理序

您可以檢查以判斷是否所有的 Dll 會載入已經過檢測的電腦上的處理程序。
監視正常運作，如果應該載入至少 12 個 Dll。

使用`Get-ApplicationInsightsMonitoringStatus -InspectProcess`命令來檢查 Dll。

檢閱[API 參考](status-monitor-v2-api-get-status.md)如如何使用此 cmdlet 的詳細描述。


### <a name="collect-etw-logs-by-using-perfview"></a>使用 PerfView 來收集 ETW 記錄檔

#### <a name="setup"></a>安裝程式

1. 下載 PerfView.exe 和從 PerfView64.exe [GitHub](https://github.com/Microsoft/perfview/releases)。
2. 啟動 PerfView64.exe。
3. 依序展開**進階選項**。
4. 清除這些核取方塊：
    - **Zip**
    - **合併式**
    - **.NET 符號集合**
5. 設定這些**額外的提供者**: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>收集記錄檔

1. 在命令主控台中使用系統管理權限執行`iisreset /stop`命令來關閉 IIS 和所有 web 應用程式。
2. 在 PerfView 中選取**開始收集**。
3. 在命令主控台中使用系統管理權限執行`iisreset /start`命令來啟動 IIS。
4. 嘗試瀏覽至您的應用程式。
5. 您的應用程式載入後，返回 PerfView，然後選取**停止收集**。



## <a name="next-steps"></a>後續步驟

- 檢閱[API 參考](status-monitor-v2-overview.md#powershell-api-reference)若要了解您可能已漏掉的參數。
- 如果您遇到此處未列出的問題時，您就可以與我們連絡上[GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)。
