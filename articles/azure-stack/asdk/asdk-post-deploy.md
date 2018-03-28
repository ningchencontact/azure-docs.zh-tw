---
title: Azure Stack 開發套件 (ASDK) 的部署後設定| Microsoft Docs
description: 說明在安裝 Azure Stack 開發套件 (ASDK) 後建議的設定變更。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 2183576e87aa2fb31f8be8f676a5aee7d52f68df
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="post-asdk-installation-configuration-tasks"></a>ASDK 安裝後設定工作
[安裝 ASDK ](asdk-install.md)後，建議您進行一些安裝後設定變更。 

## <a name="install-azure-stack-powershell"></a>安裝 Azure Stack PowerShell 
您需要與 Azure Stack 相容的 Azure PowerShell 模組，才能搭配 Azure Stack 使用。

Azure Stack 的 PowerShell 命令是透過 PowerShell 資源庫進行安裝。 若要註冊 PSGallery 存放庫，請開啟提升權限的 PowerShell 工作階段，並執行下列命令：

``` Powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

 透過 API 版本設定檔安裝 Azure Stack 相容的 AzureRM 模組。 Azure Stack 需要 2017-03-09-profile API 版本設定檔 (可藉由安裝 AzureRM.Bootstrapper 模組取得)。 
 
 不論是否能透過網際網路連線至 ASDK 主機電腦，您都可以安裝 Azure Stack PowerShell：

- **具有來自 ASDK 主機電腦的網際網路連線**。 執行下列 PowerShell 指令碼，以在開發套件安裝上安裝這些模組：

  ``` PowerShell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.11
  ```
  如果安裝成功，輸出中會顯示 AzureRM 和 AzureStack 模組。

- **不具有來自 ASDK 主機電腦的網際網路連線**。 在中斷連線的案例中，您必須先使用下列 PowerShell 命令，將 PowerShell 模組下載至具有網際網路連線的機器：

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
    -RequiredVersion 1.2.11
  ```
  接著，將下載的套件複製到 ASDK 電腦，然後註冊該位置作為預設存放庫，並從此存放庫安裝 AzureRM 與 AzureStack 模組：

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

## <a name="download-the-azure-stack-tools"></a>下載 Azure Stack 工具
[AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) 是裝載 PowerShell 模組的 GitHub 存放庫，可用來管理資源並部署至 Azure Stack。 若要取得這些工具，請複製 GitHub 存放庫或下載 AzureStack-Tools 資料夾，方法是執行下列指令碼：

  ```PowerShell
  # Change directory to the root directory. 
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip `
    -DestinationPath . `
    -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>驗證 ASDK 安裝
若要確保 ASDK 部署成功，您可以依照下列步驟來使用 Test-AzureStack Cmdlet：

1. 以 AzureStack\CloudAdmin 身分登入 ASDK 主機電腦。
2. 以系統管理員身分開啟 PowerShell (非 PowerShell ISE)。
3. 執行： `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. 執行： `Test-AzureStack`

這些測試需要幾分鐘才能完成。 如果安裝成功，輸出看起來像這樣：

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

如果發生失敗，請依照疑難排解步驟來取得協助。

## <a name="activate-the-administrator-and-tenant-portals"></a>啟用系統管理員和租用戶入口網站
使用 Azure AD 部署之後，您必須啟用 Azure Stack 系統管理員和租用戶入口網站。 此啟用同意將所有目錄使用者的正確權限 (列在同意頁面上) 提供給 Azure Stack 入口網站和 Azure Resource Manager。

- 若是系統管理員入口網站，瀏覽至 https://adminportal.local.azurestack.external/guest/signup、閱讀資訊，然後按一下 [接受]。 接受之後，您即可新增同時不是目錄租用戶管理員的服務管理員。

- 若是租用戶入口網站，瀏覽至 https://portal.local.azurestack.external/guest/signup、閱讀資訊，然後按一下 [接受]。 接受之後，目錄中的使用者可以登入租用戶入口網站。 

> [!NOTE] 
> 如果未啟用入口網站，則只有目錄管理員可以登入並使用入口網站。 如果其他使用者登入，他們會看到一項錯誤，告知系統管理員尚未授與權限給其他使用者。 當系統管理員原本不屬於 Azure Stack 註冊至的目錄時，Azure Stack 目錄必須附加到啟用 URL。 例如，如果 Azure Stack 註冊至 fabrikam.onmicrosoft.com 且管理使用者是 admin@contoso.com，請瀏覽至 https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com 以啟動入口網站。 

## <a name="reset-the-password-expiration-policy"></a>重設密碼到期原則 
為確定開發套件主機的密碼不會在評估期間結束前到期，請在部署 ASDK 之後遵循下列步驟進行。

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>若要從 Powershell 變更密碼到期原則：
從已提升權限的 PowerShell 主控台，執行以下命令：

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>若要手動變更密碼到期原則：
1. 在開發套件主機上，開啟 [群組原則管理] (GPMC.MMC)，並瀏覽至 [群組原則管理] – [樹系：azurestack.local] – [網域] – [azurestack.local]。
2. 以滑鼠右鍵按一下 [預設網域原則]，然後按一下 [編輯]。
3. 在 [群組原則管理編輯器] 中，瀏覽至 [電腦設定] – [原則] – [Windows 設定] – [安全性設定] – [帳戶原則] – [密碼原則]。
4. 在右窗格中，按兩下 [密碼最長使用期限]。
5. 在 [密碼最長使用期限內容] 對話方塊中，將 [密碼到期日] 值變更為 **180**，然後按一下 [確定]。

![群組原則管理主控台](media/asdk-post-deploy/gpmc.png)


## <a name="next-steps"></a>後續步驟
[向 Azure 註冊 ASDK](asdk-register.md)
