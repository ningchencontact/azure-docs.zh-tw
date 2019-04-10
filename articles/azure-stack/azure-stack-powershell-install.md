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
ms.date: 02/08/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 7e631281405b173405f28c134432e870c757b3da
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648397"
---
# <a name="install-powershell-for-azure-stack"></a>安裝適用於 Azure Stack 的 PowerShell

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

若要使用您的雲端，您必須安裝與 Azure Stack 相容的 PowerShell 模組。 啟用相容性時，會透過名為「API 設定檔」的功能來啟用。

API 設定檔提供一個管理 Azure 與 Azure Stack 之間版本差異的方式。 API 版本設定檔是一組具有特定 API 版本的 Azure Resource Manager PowerShell 模組。 每個雲端平台都有一組支援的 API 版本設定檔。 例如，Azure Stack 支援特定的設定檔版本，像是 **2018-03-01-hybrid**。 當您安裝設定檔時，會安裝與所指定設定檔對應的 Azure Resource Manager PowerShell 模組。

您可以在已連線至網際網路、部分連線或已中斷連線的情況下，安裝與 Azure Stack 相容的 PowerShell 模組。 本文將逐步解說針對這些情況安裝適用於 Azure Stack 之 PowerShell 的詳細指示。

## <a name="1-verify-your-prerequisites"></a>1.驗證您的必要條件

在開始使用 Azure Stack 與 PowerShell 之前，您必須具備下列必要條件：

- **PowerShell 版本 5.0** 若要檢查您的版本，請執行 **$PSVersionTable.PSVersion** 並比較**主要**版本。 如果您沒有 PowerShell 5.0，請遵循[安裝 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)。

  > [!Note]
  > PowerShell 5.0 需要 Windows 機器。

- **在已提高權限的命令提示字元中執行 PowerShell**。
  您必須以系統管理權限執行 PowerShell。

- **PowerShell 資源庫存取權** 您需要存取 [PowerShell 資源庫](https://www.powershellgallery.com)。 此資源庫是 PowerShell 內容的中央存放庫。 **PowerShellGet** 模組包含用於探索、安裝、更新及發佈 PowerShell 成品 (例如來自「PowerShell 資源庫」和其他私人存放庫的模組、DSC 資源、角色功能及指令碼) 的 Cmdlet。 如果您是在已中斷連線的情況下使用 PowerShell，您必須從可連線至網際網路的電腦擷取資源，然後將它們儲存在已中斷連線之電腦可存取的位置中。

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2.驗證 PowerShell 資源庫協助工具

驗證 PSGallery 是否已註冊為存放褲。

> [!Note]  
> 此步驟需要存取網際網路。

開啟已提升權限的 PowerShell 提示字元，然後執行下列 Cmdlet：

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

如果存放庫未註冊，請開啟提升權限的 PowerShell 工作階段，並執行下列命令：

```powershell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3.解除安裝現有的 Azure Stack PowerShell 模組版本

在安裝必要版本之前，請確定解除安裝任何先前安裝的 Azure Stack AzureRM PowerShell 模組。 您可以使用下列兩種方法之一來解除安裝它們：

1. 若要將現有的 AzureRM PowerShell 模組解除安裝，請關閉所有作用中的 PowerShell 工作階段，然後執行下列 Cmdlet：

    ```powershell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```

    若您遇到「模組已在使用中」之類的錯誤，請關閉正在使用模組的 PowerShell 工作階段，然後重新執行上面的指令碼。

2. 從 `C:\Program Files\WindowsPowerShell\Modules` 和 `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` 資料夾中，刪除開頭為 `Azure` 或 `Azs.`的所有資料夾。 刪除這些資料夾會移除任何現有的 PowerShell 模組。

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4.已連線：在具備網際網路連線能力的情況下安裝適用於 Azure Stack 的 PowerShell

Azure Stack 需要 Azure Stack 版本 1808 或更新版本的 **2018-03-01-hybrid** API 版本設定檔。 透過安裝 **AzureRM.BootStrapper** 模組可以使用該設定檔。 除了 AzureRM 模組外，您也應該安裝 Azure Stack 特有的 Azure PowerShell 模組。 您所需的 API 版本設定檔和 Azure Stack PowerShell 模組，將取決於您正在執行的 Azure Stack 版本。

安裝有三個步驟：

1. 安裝 Azure Stack PowerShell 取決於 Azure Stack 的版本而定
2. 啟用其他儲存體功能
3. 確認 PowerShell 的安裝

### <a name="install-azure-stack-powershell"></a>安裝 Azure Stack PowerShell

執行下列 PowerShell 指令碼，在開發工作站上安裝這些模組：

- Azure Stack 1901 或更新版本：

    ```powershell
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.

    Install-Module AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > Azure Stack 模組 1.7.1 版是重大變更版本。 若要從 Azure Stack 1.6.0 移轉，請參閱[移轉指南](https://aka.ms/azspshmigration171)。
    > AzureRm 模組 2.4.0 版附有 Cmdlet Remove-AzureRmStorageAccount 的重大變更。 此 Cmdlet 預期應指定 -Force 參數，以直接移除儲存體帳戶而無須確認。

- Azure Stack 1811：

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

- Azure Stack 1810 或先前版本：

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

> [!Note]  
> 若要將 Azure PowerShell 從 **2017-03-09-profile** 升級到 **2018-03-01-hybrid**，請參閱[移轉指南](https://github.com/azure/azure-powershell/blob/AzureRM/documentation/migration-guides/Stack/migration-guide.2.3.0.md)。

### <a name="enable-additional-storage-features"></a>啟用其他儲存體功能

若要利用額外的儲存功能 (＜已連線＞一節中所提及的功能)，請一併下載並安裝下列套件。

```powershell
# Install the Azure.Storage module version 4.5.0
Install-Module -Name Azure.Storage -RequiredVersion 4.5.0 -Force -AllowClobber

# Install the AzureRm.Storage module version 5.0.4
Install-Module -Name AzureRM.Storage -RequiredVersion 5.0.4 -Force -AllowClobber

# Remove incompatible storage module installed by AzureRM.Storage
Uninstall-Module Azure.Storage -RequiredVersion 4.6.1 -Force

# Load the modules explicitly specifying the versions
Import-Module -Name Azure.Storage -RequiredVersion 4.5.0
Import-Module -Name AzureRM.Storage -RequiredVersion 5.0.4
```

### <a name="confirm-the-installation-of-powershell"></a>確認 PowerShell 的安裝

執行下列命令來確認安裝：

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

如果安裝成功，輸出中會顯示 AzureRM 和 AzureStack 模組。

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5.已中斷連線：在沒有網際網路連線的情況下安裝 PowerShell

在中斷連線的情況中，您必須先將 PowerShell 模組下載至具有網際網路連線的電腦，然後將其傳送至 Azure Stack 開發套件進行安裝。

登入具有網際網路連線能力的電腦，然後根據您的 Azure Stack 版本，使用下列指令碼下載 Azure Resource Manager 和 Azure Stack 套件。

安裝有四個步驟：

1. 將 Azure Stack PowerShell 安裝至已連線的機器
2. 啟用其他儲存體功能
3. 將 PowerShell 套件傳輸到已中斷連線的工作站
4. 確認 PowerShell 的安裝

### <a name="install-azure-stack-powershell"></a>安裝 Azure Stack PowerShell

- Azure Stack 1901 或更新版本。

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.4.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > Azure Stack 模組版本 1.7.1 是一個重大變更。 若要從 Azure Stack 1.6.0 移轉，請參閱[移轉指南](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack)。

  - Azure Stack 1811 或先前版本。

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
    ```

  - Azure Stack 1809 或先前版本。

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ```

    > [!NOTE]
    > 在沒有網際網路連線的電腦上，建議您執行下列 Cmdlet 以停用遙測資料收集。 若未停用遙測資料收集，可能會發生 Cmldet 效能降低的狀況。 不具網際網路連線的電腦才有可能發生此狀況
    > ```powershell
    > Disable-AzureRmDataCollection
    > ```

### <a name="enable-additional-storage-features"></a>啟用其他儲存體功能

若要利用額外的儲存功能 (＜已連線＞一節中所提及的功能)，請一併下載並安裝下列套件。

```powershell
$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azure.Storage -Path $Path -Force -RequiredVersion 4.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRm.Storage -Path $Path -Force -RequiredVersion 5.0.4
```

### <a name="add-your-packages-to-your-workstation"></a>將您的套件新增至您的工作站

1. 將下載的套件複製到 USB 裝置。

2. 登入已中斷連線的工作站，並將套件從 USB 裝置複製到工作站上的位置。

3. 現在註冊此位置作為預設存放庫，並從此存放庫安裝 AzureRM 與 AzureStack 模組：

   ```powershell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module -Name AzureRM -Repository $RepoName

   Install-Module -Name AzureStack -Repository $RepoName
   ```

### <a name="confirm-the-installation-of-powershell"></a>確認 PowerShell 的安裝

執行下列命令來確認安裝：

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6.設定 PowerShell 使用 Proxy 伺服器

在需要 Proxy 伺服器才能存取網際網路的情況下，您必須先將 PowerShell 設定成使用現有的 Proxy 伺服器：

1. 開啟提升權限的 PowerShell 提示字元。
2. 執行下列命令：

   ```powershell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="next-steps"></a>後續步驟

- [從 GitHub 下載 Azure Stack 工具](azure-stack-powershell-download.md)
- [設定 Azure Stack 使用者的 PowerShell 環境](user/azure-stack-powershell-configure-user.md)
- [設定 Azure Stack 操作員的 PowerShell 環境](azure-stack-powershell-configure-admin.md)
- [在 Azure Stack 中管理 API 版本設定檔](user/azure-stack-version-profiles.md)
