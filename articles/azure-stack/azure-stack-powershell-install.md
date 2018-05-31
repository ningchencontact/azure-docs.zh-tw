---
title: 安裝 Azure Stack 的 PowerShell | Microsoft Docs
description: 瞭解如何安裝 Azure Stack 的 PowerShell。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 5/18/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: b3c09582f5135655640768bcbcbef91750827bfa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358885"
---
# <a name="install-powershell-for-azure-stack"></a>安裝適用於 Azure Stack 的 PowerShell

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

您需要與 Azure Stack 相容的 Azure PowerShell 模組，才能搭配 Azure Stack 使用。 在本指南中，我們引導您安裝 Azure Stack 的 PowerShell 所需要的步驟。

這篇文章有安裝 Azure Stack 的 PowerShell 的詳細指示。

> [!Note]
> 下列步驟需要 PowerShell 5.0。 若要檢查您的版本，請執行 $PSVersionTable.PSVersion 並比較**主要**版本。

Azure Stack 的 PowerShell 命令是透過 PowerShell 資源庫進行安裝。 您可以使用下列程序來驗證 PSGallery 是否已註冊為存放庫，請開啟提升權限的 PowerShell 工作階段，並執行下列命令：

```PowerShell  
Get-PSRepository -Name "PSGallery"
```

如果存放庫未註冊，請開啟提升權限的 PowerShell 工作階段，並執行下列命令：

```PowerShell  
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> 此步驟需要存取網際網路。 

## <a name="uninstall-existing-versions-of-powershell"></a>解除安裝現有的 PowerShell 版本

在安裝必要版本之前，請確定解除安裝任何先前安裝的 Azure Stack PowerShell 模組。 您可以使用下列兩種方法之一來解除安裝它們：

 - 若要解除安裝現有的 PowerShell 模組，請關閉所有作用中的 PowerShell 工作階段，然後執行下列命令：

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - 從 `C:\Program Files\WindowsPowerShell\Modules` 和 `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` 資料夾刪除以「Azure」開頭的所有資料夾。 刪除這些資料夾會移除任何現有的 PowerShell 模組。

下列各節說明安裝 Azure Stack 的 PowerShell 的必要步驟。 可在連線、部分連線或中斷連線案例中，於 Azure Stack 上安裝 PowerShell。

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>在已連線的情況 (具有網際網路連線能力) 安裝 PowerShell

透過 API 版本設定檔安裝 Azure Stack 相容的 AzureRM 模組。 Azure Stack 需要 **2017-03-09-profile** API 版本設定檔 (可藉由安裝 AzureRM.Bootstrapper 模組取得)。 若要深入了解 API 版本設定檔和其所提供的 cmdlet，請參閱[管理 API 版本設定檔](user/azure-stack-version-profiles.md)。 除了 AzureRM 模組外，您也應該安裝 Azure Stack 特有的 Azure PowerShell 模組。 執行下列 PowerShell 指令碼，在開發工作站上安裝這些模組：

  ```PowerShell  
# Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
Install-Module -Name AzureRm.BootStrapper 

# Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

# Install Module Version 1.3.0 if Azure Stack is running 1804 at a minimum 
Install-Module -Name AzureStack -RequiredVersion 1.3.0 

# Install Module Version 1.2.11 if Azure Stack is running a lower version than 1804 
Install-Module -Name AzureStack -RequiredVersion 1.2.11 
  ```

若要確認安裝，請執行下列命令：

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

如果安裝成功，輸出中會顯示 AzureRM 和 AzureStack 模組。

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>在中斷連線或部分連線的情況 (具有有限的網際網路連線能力) 安裝 PowerShell

在中斷連線的情況中，您必須先將 PowerShell 模組下載至具有網際網路連線的電腦，然後將其傳送至 Azure Stack 開發套件進行安裝。

> [!IMPORTANT]  
> Azure Stack 1.3.0 PowerShell 模組的發行版本隨附重大變更清單。 若要從 1.2.11 版升級，請參閱[移轉指南](https://aka.ms/azspowershellmigration)。

1. 登入具有網際網路連線的電腦並使用下列指令碼，將 AzureRM 及 AzureStack 套件下載到您的本機電腦：

   ```PowerShell  
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.3.0 
   ```

  > [!Important]  
  > 如果您未執行 Azure Stack Update 1804 或更新版本，請將 **requiredversion** 參數值變更為 `1.2.11`。 

2. 將下載的套件複製到 USB 裝置。

3. 登入工作站，並將套件從 USB 裝置複製到工作站上的位置。

4. 現在您必須註冊此位置作為預設存放庫，並從此存放庫安裝 AzureRM 與 AzureStack 模組：

   ```PowerShell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName 
   ```

## <a name="configure-powershell-to-use-a-proxy-server"></a>設定 PowerShell 使用 Proxy 伺服器

在需要 Proxy 伺服器存取網際網路的情況下，您必須先設定 PowerShell 使用現有的 Proxy 伺服器。

1. 開啟提升權限的 PowerShell 提示字元。
2. 執行下列命令：

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>後續步驟

 - [從 GitHub 下載 Azure Stack 工具](azure-stack-powershell-download.md)
 - [設定 Azure Stack 使用者的 PowerShell 環境](user/azure-stack-powershell-configure-user.md)  
 - [設定 Azure Stack 操作員的 PowerShell 環境](azure-stack-powershell-configure-admin.md) 
 - [在 Azure Stack 中管理 API 版本設定檔](user/azure-stack-version-profiles.md)  
