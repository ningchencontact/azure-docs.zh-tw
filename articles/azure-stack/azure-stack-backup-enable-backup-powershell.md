---
title: 使用 PowerShell 啟用 Azure Stack 備份 | Microsoft Docs
description: 使用 Windows PowerShell 啟用「基礎結構備份服務」，以便在發生失敗時，可以將 Azure Stack 還原。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 7DFEFEBE-D6B7-4BE0-ADC1-1C01FB7E81A6
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: d21bb919686e318b1caf7267b3115dae20938884
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/19/2018
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>使用 PowerShell 啟用 Azure Stack 備份

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

使用 Windows PowerShell 啟用「基礎結構備份服務」，以便在發生失敗時，可以將 Azure Stack 還原。 您可以存取 PowerShell Cmdlet 來啟用備份、開始備份，並透過作業員管理端點取得備份資訊。

## <a name="download-azure-stack-tools"></a>下載 Azure Stack 工具

安裝和設定適用於 Azure Stack 的 PowerShell 和 Azure Stack 工具。 請參閱[在 Azure Stack 使用 PowerShell 啟動和執行](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart)。

##  <a name="load-the-connect-and-infrastructure-modules"></a>載入連線和基礎結構模組

使用提升權限提示字元開啟 Windows PowerShell，並執行下列命令：

   ```powershell
    cd C:\tools\AzureStack-Tools-master\Connect
    Import-Module .\AzureStack.Connect.psm1
    
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
    
   ```

##  <a name="setup-rm-environment-and-log-into-the-operator-management-endpoint"></a>安裝 RM 環境並登入作業員管理端點

在相同的 PowerShell 工作階段中，新增您環境的變數來編輯下列 PowerShell 指令碼。 執行更新的指令碼來安裝 RM 環境，並登入作業員管理端點。

| 變數    | 說明 |
|---          |---          |
| $TenantName | Azure Active Directory 租用戶名稱。 |
| 作業員帳戶名稱        | 您的 Azure Stack 作業員帳戶名稱。 |
| Azure Resource Manager 端點 | Azure Resource Manager 的 URL。 |

   ```powershell
   # Specify Azure Active Directory tenant name
    $TenantName = "contoso.onmicrosoft.com"
    
    # Set the module repository and the execution policy
    Set-PSRepository `
      -Name "PSGallery" `
      -InstallationPolicy Trusted
    
    Set-ExecutionPolicy RemoteSigned `
      -force
    
    # Configure the Azure Stack operator’s PowerShell environment.
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint "https://adminmanagement.seattle.contoso.com"
    
    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience "https://graph.windows.net/"
    
    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName $TenantName `
      -EnvironmentName AzureStackAdmin
    
    # Sign-in to the operator's console.
    Connect-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID 
    
   ```
## <a name="generate-a-new-encryption-key"></a>產生新的加密金鑰

在相同的 PowerShell 工作階段中，執行下列命令：

   ```powershell
   $encryptionkey = New-EncryptionKeyBase64
   ```

> [!Warning]  
> 您必須使用 AzureStack-Tools 來產生金鑰。

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>提供備份共用、認證和加密金鑰可啟用備份

在相同的 PowerShell 工作階段中，新增您環境的變數來編輯下列 PowerShell 指令碼。 執行更新的指令碼，向基礎結構備份服務提供備份共用、認證和加密金鑰。

| 變數        | 說明   |
|---              |---                                        |
| $username       | 使用共用磁碟機位置的網域和使用者名稱來輸入**使用者名稱**。 例如： `Contoso\administrator`。 |
| $password       | 輸入使用者的**密碼**。 |
| $sharepath      | 輸入**備份儲存位置**的路徑。 針對裝載在不同裝置的檔案共用路徑，您必須使用通用命名慣例 (UNC) 字串。 UNC 字串會指定資源的位置，例如共用的檔案或裝置。 若要確保備份資料的可用性，裝置應該位於不同的位置。 |

   ```powershell
    $username = "domain\backupoadmin"
    $password = "password"
    $credential = New-Object System.Management.Automation.PSCredential($username, ($password| ConvertTo-SecureString -asPlainText -Force))  
    $location = Get-AzsLocation
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
    
    Set-AzSBackupShare -Location $location.Name -Path $sharepath -UserName $credential.UserName -Password $credential.GetNetworkCredential().password -EncryptionKey $encryptionkey
   ```
   
##  <a name="confirm-backup-settings"></a>確認備份設定

在相同的 PowerShell 工作階段中，執行下列命令：

   ```powershell
   Get-AzsBackupLocation | Select-Object -Property Path, UserName, Password | ConvertTo-Json 
   ```

結果應該看起來像下列的 JSON 輸出：

   ```json
      {
    "ExternalStoreDefault":  {
        "Path":  "\\\\serverIP\\AzSBackupStore\\contoso.com\\seattle",
        "UserName":  "domain\backupoadmin",
        "Password":  null
       }
   } 
   ```

## <a name="next-steps"></a>後續步驟

 - 若要了解如何執行備份，請參閱[備份 Azure Stack](azure-stack-backup-back-up-azure-stack.md )。  
- 若要了解如何確認您的備份已執行，請參閱[在系統管理入口網站中確認已完成的備份](azure-stack-backup-back-up-azure-stack.md )。
