---
title: Azure 狀態監視器第 2 版開始使用 |Microsoft Docs
description: 狀態監視器 v2 的快速入門指南。 監視網站效能，而不必重新部署網站。 使用裝載於內部部署、VM 中或 Azure 上的 ASP.NET Web 應用程式。
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
ms.openlocfilehash: c20cbf50129247fa01f10f12a99c558bc86492b8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145167"
---
# <a name="getting-started-with-status-monitor-v2"></a>開始使用狀態監視器 v2

本文件包含適用於大部分的環境中預期的快速入門命令。 這些指示取決於散佈更新 「 PowerShell 資源庫。 這些命令支援 PowerShell`-Proxy`參數。

檢閱我們[的詳細指示](status-monitor-v2-detailed-instructions.md)如需這些命令、 有關如何進行自訂，以及如何進行疑難排解的說明頁面。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。 

> [!IMPORTANT]
> 狀態監視器 v2 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱[補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="download--install-via-powershell-gallery"></a>下載並安裝透過 PowerShell 資源庫

### <a name="install-prerequisites"></a>安裝必要條件
以系統管理員身分執行 PowerShell
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
結束 PowerShell

### <a name="install-status-monitor-v2"></a>安裝狀態監視 v2
以系統管理員身分執行 PowerShell
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>啟用監視
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download--install-manually-offline-option"></a>下載並手動安裝 （offline 選項）
### <a name="manual-download"></a>手動下載
手動下載最新版的模組： https://www.powershellgallery.com/packages/Az.ApplicationMonitor

### <a name="unzip-and-install-status-monitor-v2"></a>解壓縮並安裝狀態監視器 v2
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.2.1-alpha.nupkg"
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

- [探索計量](../../azure-monitor/app/metrics-explorer.md)以監視效能和使用量
- [搜尋事件和記錄](../../azure-monitor/app/diagnostic-search.md)來診斷問題
- 更多進階查詢的[分析](../../azure-monitor/app/analytics.md)
- [建立儀表板](../../azure-monitor/app/app-insights-dashboards.md)

 新增更多遙測：

- [建立 web 測試](monitor-web-app-availability.md)藉此確定您的網站保持即時狀態。
- [新增 web 用戶端遙測](../../azure-monitor/app/javascript.md)以查看網頁程式碼中的例外狀況，並讓您插入追蹤呼叫。
- [將 Application Insights SDK 新增至您的程式碼](../../azure-monitor/app/asp-net.md)，讓您插入追蹤和記錄呼叫

進一步運用狀態監視器 v2:

- 檢閱[的詳細指示](status-monitor-v2-detailed-instructions.md)如本指南中的命令的說明。
- 使用我們的指南，來[疑難排解](status-monitor-v2-troubleshoot.md)狀態監視器 v2。
