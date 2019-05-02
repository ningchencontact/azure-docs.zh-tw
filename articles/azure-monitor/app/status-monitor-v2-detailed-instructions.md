---
title: Azure 狀態監視器第 2 版的詳細指示 |Microsoft Docs
description: 開始使用狀態監視器 v2 的詳細的指示。 監視網站效能，而不必重新部署網站。 使用裝載於內部部署、VM 中或 Azure 上的 ASP.NET Web 應用程式。
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
ms.openlocfilehash: 62c7c6b2f78ea90999f72597291e8347ddc14029
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870428"
---
# <a name="status-monitor-v2-detailed-instructions"></a>狀態監視器 v2 詳細指示

此文件詳細資料時，如何上架到 PowerShell 資源庫並下載 ApplicationMonitor 模組。 我們已記載開始所需的最常見參數。
我們也包含了手動指令，網際網路存取無法使用。

> [!IMPORTANT]
> 狀態監視器 v2 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱[補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="instrumentation-key"></a>檢測金鑰

若要開始，您必須使用檢測金鑰。 如需詳細資訊，請參閱[建立 Application Insights 資源](create-new-resource.md#copy-the-instrumentation-key)。

## <a name="run-powershell-as-administrator-with-an-elevated-execution-policy"></a>使用提升權限的執行原則的系統管理員身分執行 PowerShell

**系統管理員身分執行**: 
- Description:PowerShell 會需要變更您的電腦的系統管理員層級權限。

**執行原則**:
- Description:根據預設，執行 PowerShell 指令碼將會停用。 我們建議您針對目前的範圍只允許 RemoteSigned 指令碼。
- 參考：[關於執行原則](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)和[Set-executionpolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)
- Cmd: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`
- 選擇性參數：
    - `-Force` 這會略過確認提示。

**錯誤的範例：**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>PowerShell 的必要條件

稽核您目前的版本 PowerShell 執行命令： `$PSVersionTable`。
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

這些指示所撰寫，並與上面所列的版本的 Windows 10 電腦上進行測試。

## <a name="prerequisites-for-powershell-gallery"></a>PowerShell 資源庫的必要條件

這些步驟會準備您的伺服器從 「 PowerShell 資源庫下載模組。

> [!NOTE] 
> Windows 10、windows Server 2016 及 PowerShell 6 中包含的 PowerShell 資源庫支援。 如需較舊版本中，檢閱這份文件：[安裝的 PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)


1. 以系統管理員身分執行 PowerShell，使用提升權限的執行原則。
2. NuGet 套件提供者 
    - Description:此提供者，才能與 NuGet 型存放庫，例如 powershell 資源庫互動
    - 參考：[Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6)
    - Cmd: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`
    - 選擇性參數：
        - `-Proxy` 指定 proxy 伺服器要求。
        - `-Force` 這會略過確認提示。 
    
    如果未設定 NuGet，您會收到這項提示：
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. 受信任的存放庫
    - Description:根據預設，powershell 資源庫會是不受信任的存放庫。
    - 參考：[Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6)
    - Cmd: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`
    - 選擇性參數：
        - `-Proxy` 指定 proxy 伺服器要求。

    如果 PowerShell 資源庫不受信任，您會收到這項提示：

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    - 可以確認這項變更，並執行 cmd 稽核所有 PSRepositories: `Get-PSRepository`

4. PowerShellGet 版本 
    - Description:此模組包含用來取得其他模組，從 PowerShell 資源庫的工具。 v1.0.0.1 隨附於 Windows 10 和 Windows Server。 所需的最小版本即為 v1.6.0。 若要稽核所安裝的版本，執行命令： `Get-Command -Module PowerShellGet`
    - 參考：[安裝的 PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)
    - Cmd: `Install-Module -Name PowerShellGet`
    - 選擇性參數：
        - `-Proxy` 指定 proxy 伺服器要求。
        - `-Force` 這將會忽略 「 已安裝 」 警告，並安裝最新版本。

    如果您不使用最新版本的 PowerShellGet，您會收到此錯誤：
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. 重新啟動 PowerShell。 您無法在目前的工作階段中載入新的版本。 任何新的 Powershell 工作階段會有最新的 PowerShellGet 載入。

## <a name="download--install-via-powershell-gallery"></a>下載並安裝透過 PowerShell 資源庫

這些步驟會從 PowerShell 資源庫下載 Az.ApplicationMonitor 模組。

1. PowerShell 資源庫的必要條件是必要的。
2. 以系統管理員身分執行 PowerShell，使用提升權限的執行原則。
3. 安裝 Az.ApplicationMonitor 模組
    - 參考：[Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6)
    - Cmd: `Install-Module -Name Az.ApplicationMonitor`
    - 選擇性參數：
        - `-Proxy` 指定 proxy 伺服器要求。
        - `-AllowPrerelease` 這可讓安裝 alpha 和 beta 版的版本。
        - `-AcceptLicense` 這將會略過 「 接受授權 」 提示
        - `-Force` 這將會忽略 「 不受信任的存放庫 」 警告

## <a name="download--install-manually-offline-option"></a>下載並手動安裝 （offline 選項）

如果因為任何原因，您無法連線到 PowerShell 模組，您可以手動下載並安裝 Az.ApplicationMonitor 模組。

### <a name="manually-download-the-latest-nupkg"></a>手動下載最新的 nupkg

1. 瀏覽至： https://www.powershellgallery.com/packages/Az.ApplicationMonitor
2. 從版本歷程記錄中，選取最新版本。
3. 尋找 [安裝選項] 並選取 [手動下載]。

### <a name="option-1-install-into-powershell-modules-directory"></a>選項 1： 安裝 PowerShell 模組目錄
安裝 PowerShell 目錄手動下載的 PowerShell 模組，因此它可以是可探索的 PowerShell 工作階段。
如需詳細資訊，請參閱[安裝 PowerShell 模組](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)


#### <a name="unzip-nupkg-as-zip-using-expand-archive-v1010"></a>為使用 Expand-archive (v1.0.1.0) 的 zip 解壓縮 nupkg

- Description:Microsoft.PowerShell.Archive (v1.0.1.0) 的基底版本不能將 nupkg 檔案解壓縮。 「.zip 」 延伸模組的檔案重新命名。
- 參考：[Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)
- Cmd: 

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-using-expand-archive-v1100"></a>將解壓縮 nupkg 使用 Expand-archive (v1.1.0.0)。

- Description:使用新版展開封存解壓縮中而不需要重新命名的延伸模組。 
- 參考：[展開封存](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)和[Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0)
- Cmd:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-manually"></a>選項 2： 解壓縮，並手動匯入
安裝 PowerShell 目錄手動下載的 PowerShell 模組，因此它可以是可探索的 PowerShell 工作階段。
如需詳細資訊，請參閱[安裝 PowerShell 模組](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)

如果安裝到任何其他目錄，您必須手動匯入模組使用[匯入模組](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)

> [!IMPORTANT] 
> 安裝將會透過相對路徑中安裝 Dll。 將此封裝的內容儲存至您想要的執行階段的目錄，並確認 存取權限允許讀取但是不能寫入。

1. 將副檔名變更為 「.zip 」，並將封裝的內容解壓縮到您想要的安裝的目錄。
2. 尋找 「 Az.ApplicationMonitor.psd1"的檔案路徑。
3. 以系統管理員身分執行 PowerShell，使用提升權限的執行原則。 
4. 載入的模組，透過命令： `Import-Module Az.ApplicationMonitor.psd1`。
    

## <a name="proxy"></a>Proxy

當監視的私人內部網路上的機器，它必須將 http 流量，透過 proxy 路由傳送。

支援的 PowerShell 命令，下載並從 PowerShell Gallery 安裝 Az.ApplicationMonitor`-Proxy`參數。
當檢閱上述指示撰寫您的安裝指令碼。

Application Insights SDK 需要將您的應用程式遙測資料傳送給 Microsoft。 我們建議您 web.config 中設定您的應用程式的 proxy 設定。請參閱[Application Insights 常見問題集：Proxy 傳遞](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough)如需詳細資訊。


## <a name="enable-monitoring"></a>啟用監視 

Cmd: `Enable-ApplicationInsightsMonitoring`

檢閱我們[API 參考](status-monitor-v2-api-enable-monitoring.md)如如何使用此 cmdlet 的詳細描述。 
