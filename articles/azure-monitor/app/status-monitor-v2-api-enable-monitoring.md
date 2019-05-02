---
title: Azure 狀態監視器 v2 API 參考：若要啟用監視 |Microsoft Docs
description: 狀態監視器 v2 API 參考啟用-ApplicationInsightsMonitoring。 監視網站效能，而不必重新部署網站。 使用裝載於內部部署、VM 中或 Azure 上的 ASP.NET Web 應用程式。
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
ms.openlocfilehash: e2d964316e83138711547fb18fc5d04c56b4002f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870518"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>狀態監視器 v2 API:啟用 ApplicationInsightsMonitoring (v0.2.1-alpha)

本文件說明 cmdlet 所隨附的成員身分[Az.ApplicationMonitor PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)。

> [!IMPORTANT]
> 狀態監視器 v2 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱[補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>描述

啟用程式碼較不附加的目標電腦上的 IIS 應用程式監視。
此 cmdlet 會修改 IIS applicationHost.config，並設定一些登錄機碼。
此 cmdlet 也會建立 applicationinsights.ikey.config，定義哪些應用程式所使用的檢測金鑰。
IIS 會載入在啟動時，這會是將 Application Insights SDK 插入應用程式，這些應用程式啟動 RedfieldModule。
重新啟動 IIS，您的變更才會生效。

啟用監視之後, 我們建議您使用[即時計量](live-stream.md)來快速地觀察 是否您的應用程式會傳送給我們的遙測。


> [!NOTE] 
> 若要開始，您必須使用檢測金鑰。 如需詳細資訊，請參閱[建立新的資源](create-new-resource.md#copy-the-instrumentation-key)。


> [!IMPORTANT] 
> 此 cmdlet 需要 PowerShell 工作階段與系統管理員權限和更高的執行原則。 讀取[此處](status-monitor-v2-detailed-instructions.md#run-powershell-as-administrator-with-an-elevated-execution-policy)如需詳細資訊。

> [!NOTE] 
> 此 cmdlet 會要求您檢閱並接受我們的授權及隱私權聲明陳述式。


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


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**選用。** 若要啟用檢測引擎來收集事件和訊息的發生什麼情況的受管理的程序執行期間使用此參數。 其中包括但不是限於相依性的結果碼、 HTTP 指令動詞，與 SQL 命令文字。 檢測引擎將會增加額外的負荷，並預設為關閉。

### <a name="-acceptlicense"></a>-AcceptLicense
**選用。** 您可以使用此參數可以接受授權及隱私權聲明中的陳述式無周邊的安裝。

### <a name="-verbose"></a>-Verbose
**常見的參數。** 您可以使用這個參數，輸出的詳細的記錄。

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

