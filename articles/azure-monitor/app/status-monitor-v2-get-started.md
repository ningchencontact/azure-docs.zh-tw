---
title: Azure 狀態監視器 v2-開始使用 |Microsoft Docs
description: 狀態監視器 v2 的快速入門手冊。 在不重新部署網站的情況下監視網站效能。 適用于內部部署、Vm 或 Azure 上裝載的 ASP.NET web 應用程式。
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: d9c354edac3cbd3faccaa261654e56f858befdf6
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058240"
---
# <a name="get-started-with-status-monitor-v2"></a>開始使用狀態監視器 v2

本文包含預期適用于大部分環境的快速入門命令。
這些指示取決於散發更新的 PowerShell 資源庫。
這些命令支援 PowerShell `-Proxy`參數。

如需這些命令的說明、自訂指示和疑難排解的相關資訊, 請參閱[詳細指示](status-monitor-v2-detailed-instructions.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="download-and-install-via-powershell-gallery"></a>透過 PowerShell 資源庫下載並安裝

### <a name="install-prerequisites"></a>安裝必要條件
以系統管理員身分執行 PowerShell。
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
關閉 PowerShell。

### <a name="install-status-monitor-v2"></a>安裝狀態監視器 v2
以系統管理員身分執行 PowerShell。
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>啟用監視
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>手動下載並安裝 (離線選項)
### <a name="download-the-module"></a>下載模組
從[PowerShell 資源庫](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)手動下載最新版本的模組。

### <a name="unzip-and-install-status-monitor-v2"></a>解壓縮並安裝狀態監視器 v2
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>啟用監視
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



## <a name="next-steps"></a>後續步驟

 檢視遙測：

- [探索計量](../../azure-monitor/app/metrics-explorer.md)以監視效能和使用量。
- [搜尋事件和記錄](../../azure-monitor/app/diagnostic-search.md)以診斷問題。
- [流量分析](../../azure-monitor/app/analytics.md)進行更先進的查詢。
- [建立儀表板](../../azure-monitor/app/overview-dashboard.md)。

 新增更多遙測：

- [建立 web 測試](monitor-web-app-availability.md), 以確保您的網站保持上線。
- [新增 web 用戶端遙測](../../azure-monitor/app/javascript.md), 以查看來自網頁程式碼的例外狀況, 並啟用追蹤呼叫。
- [將 APPLICATION INSIGHTS SDK 新增至您的程式碼](../../azure-monitor/app/asp-net.md), 讓您可以插入追蹤和記錄呼叫。

使用狀態監視器 v2 來執行更多動作:

- 如需相關命令的說明, 請參閱這裡的[詳細指示](status-monitor-v2-detailed-instructions.md)。
- 使用我們的指南進行狀態監視器 v2 的[疑難排解](status-monitor-v2-troubleshoot.md)。
