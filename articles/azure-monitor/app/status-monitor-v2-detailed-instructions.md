---
title: Azure 狀態監視器第 2 版的詳細指示 |Microsoft Docs
description: 開始使用狀態監視器 v2 的詳細的指示。 監視網站效能，而不必重新部署網站。 使用 ASP.NET web 應用程式裝載於內部，在 Vm，或在 Azure 上。
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
ms.openlocfilehash: d0960c749d74903acc778c0f21d5c49f380195ae
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734188"
---
# <a name="status-monitor-v2-detailed-instructions"></a>狀態監視器 v2:詳細指示

這篇文章說明如何上架到 PowerShell 資源庫並下載 ApplicationMonitor 模組。
它會描述您要開始使用最常用的參數。
它也包含手動的指示，如果您沒有網際網路存取權。

> [!IMPORTANT]
> 狀態監視器 v2 目前處於公開預覽狀態。
> 此預覽版不提供的服務等級協定，且我們不建議用於生產工作負載。 可能不支援某些功能，以及一些可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="get-an-instrumentation-key"></a>取得檢測金鑰

若要開始，您需要檢測金鑰。 如需詳細資訊，請參閱 <<c0> [ 建立 Application Insights 資源](create-new-resource.md#copy-the-instrumentation-key)。

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>系統管理員身分執行 PowerShell，使用提升權限的執行原則

**以系統管理員身分執行**

PowerShell 會需要變更您的電腦的系統管理員層級權限。

**執行原則**
- Description:根據預設，執行 PowerShell 指令碼已停用。 我們建議您允許 RemoteSigned 指令碼針對目前的範圍。
- 參考：[關於執行原則](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)並[Set-executionpolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)。
- 命令： `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`。
- 選擇性參數：
    - `-Force` 。 會略過確認提示。

**錯誤的範例**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>PowerShell 的必要條件

稽核您的 PowerShell 執行個體執行`$PSVersionTable`命令。
此命令會產生下列輸出：


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

這些指示所撰寫，並在執行 Windows 10 與上面所列版本的電腦上進行測試。

## <a name="prerequisites-for-powershell-gallery"></a>PowerShell 資源庫的必要條件

這些步驟會準備您的伺服器，從 PowerShell 資源庫下載模組。

> [!NOTE] 
> PowerShell 資源庫支援 Windows 10，Windows Server 2016 和 PowerShell 6。
> 如需較早的版本資訊，請參閱 <<c0> [ 安裝的 PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)。


1. 以管理員身分執行 PowerShell，使用提升權限的執行原則。
2. 安裝 NuGet 套件提供者。
    - Description:您需要此提供者與 NuGet 型存放庫，例如 PowerShell 資源庫互動。
    - 參考：[Install-packageprovider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6)。
    - 命令： `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`。
    - 選擇性參數：
        - `-Proxy` 。 指定 proxy 伺服器要求。
        - `-Force` 。 會略過確認提示。
    
    如果未設定 NuGet，您會收到這項提示：
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. 設定 PowerShell 資源庫做為受信任的存放庫。
    - Description:根據預設，PowerShell 資源庫會是不受信任的存放庫。
    - 參考：[Set-psrepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6)。
    - 命令： `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`。
    - 選擇性參數：
        - `-Proxy` 。 指定 proxy 伺服器要求。

    如果 PowerShell 資源庫不受信任，您會收到這項提示：

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    您可以確認這項變更，並藉由執行稽核所有 PSRepositories`Get-PSRepository`命令。

4. 安裝最新版的 PowerShellGet。
    - Description:此模組包含用來取得其他模組，從 PowerShell 資源庫的工具。 版本為 1.0.0.1，隨附於 Windows 10 和 Windows Server。 1.6.0 版或更高的需要。 若要判斷所安裝的版本，請執行`Get-Command -Module PowerShellGet`命令。
    - 參考：[安裝 PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)。
    - 命令： `Install-Module -Name PowerShellGet`。
    - 選擇性參數：
        - `-Proxy` 。 指定 proxy 伺服器要求。
        - `-Force` 。 會略過 「 已安裝 」 的警告，並安裝最新版本。

    如果您不使用 PowerShellGet 的最新版本，您會收到此錯誤：
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. 重新啟動 PowerShell。 您無法在目前的工作階段中載入新的版本。 新的 PowerShell 工作階段會載入最新版的 PowerShellGet。

## <a name="download-and-install-the-module-via-powershell-gallery"></a>下載並安裝模組，透過 PowerShell 資源庫

這些步驟會從 PowerShell 資源庫下載 Az.ApplicationMonitor 模組。

1. 請確定已符合所有必要條件，PowerShell 資源庫。
2. 以管理員身分執行 PowerShell，使用提升權限的執行原則。
3. 安裝 Az.ApplicationMonitor 模組。
    - 參考：[Install-module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6)。
    - 命令： `Install-Module -Name Az.ApplicationMonitor`。
    - 選擇性參數：
        - `-Proxy` 。 指定 proxy 伺服器要求。
        - `-AllowPrerelease` 。 允許的 alpha 和 beta 版本的安裝。
        - `-AcceptLicense` 。 會略過 「 接受授權 」 的提示字元
        - `-Force` 。 會略過 「 不受信任的存放庫 」 警告。

## <a name="download-and-install-the-module-manually-offline-option"></a>手動下載並安裝模組 （offline 選項）

如果因為任何原因，您無法連線到 PowerShell 模組，您可以手動下載並安裝 Az.ApplicationMonitor 模組。

### <a name="manually-download-the-latest-nupkg-file"></a>手動下載最新的 nupkg 檔案

1. 移至 https://www.powershellgallery.com/packages/Az.ApplicationMonitor。
2. 選取的檔案中的最新版本**版本歷程記錄**資料表。
3. 底下**安裝選項**，選取**手動下載**。

### <a name="option-1-install-into-a-powershell-modules-directory"></a>選項 1：安裝 PowerShell 模組目錄到
如此便能找到的 PowerShell 工作階段，請將手動下載的 PowerShell 模組安裝到 PowerShell 目錄中。
如需詳細資訊，請參閱 <<c0> [ 安裝 PowerShell 模組](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)。


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>為 zip 檔案解壓縮 nupkg，使用 Expand-archive (v1.0.1.0)

- Description:Microsoft.PowerShell.Archive (v1.0.1.0) 的基底版本不能將 nupkg 檔案解壓縮。 副檔名為.zip 檔案重新命名。
- 參考：[展開封存](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)。
- 命令：

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>解壓縮 nupkg 使用 Expand-archive (v1.1.0.0)

- Description:使用新版展開封存解壓縮 nupkg 檔案而不會變更延伸模組。
- 參考：[展開封存](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)並[Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0)。
- 命令：

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>選項 2：解壓縮，並手動匯入 nupkg
如此便能找到的 PowerShell 工作階段，請將手動下載的 PowerShell 模組安裝到 PowerShell 目錄中。
如需詳細資訊，請參閱 <<c0> [ 安裝 PowerShell 模組](https://docs.mircrosoft.com/powershell/developer/module/installing-a-powershell-module)。

如果您要安裝的模組到任何其他目錄，以手動方式使用匯入模組[Import-module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)。

> [!IMPORTANT] 
> Dll 將會安裝透過相對路徑。
> 在您想要的執行階段的目錄中儲存封裝的內容，並確認 存取權限允許讀取但是不能寫入。

1. 將副檔名變更為 「.zip 」，並將封裝的內容解壓縮到您想要的安裝目錄。
2. 尋找 Az.ApplicationMonitor.psd1 的檔案路徑。
3. 以管理員身分執行 PowerShell，使用提升權限的執行原則。
4. 使用載入模組`Import-Module Az.ApplicationMonitor.psd1`命令。
    

## <a name="route-traffic-through-a-proxy"></a>透過 proxy 路由傳送流量

當您在私人內部網路上監視的電腦時，您必須將 HTTP 流量，透過 proxy 路由傳送。

若要下載並安裝 Az.ApplicationMonitor 從 PowerShell 資源庫的 PowerShell 命令支援`-Proxy`參數。
當您撰寫您的安裝指令碼時，請檢閱先前的指示。

Application Insights SDK 需要將您的應用程式遙測資料傳送給 Microsoft。 我們建議您的應用程式設定 proxy 設定，在您的 web.config 檔案中。 如需詳細資訊，請參閱[Application Insights 常見問題集：Proxy 傳遞](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough)。


## <a name="enable-monitoring"></a>啟用監視

使用`Enable-ApplicationInsightsMonitoring`命令，以啟用監視。

請參閱[API 參考](status-monitor-v2-api-enable-monitoring.md)如如何使用此 cmdlet 的詳細描述。



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
