---
title: 使用 PowerShell 啟用 Azure Stack 備份 | Microsoft Docs
description: 使用 Windows PowerShell 啟用「基礎結構備份服務」，以便在發生失敗時，可以將 Azure Stack 還原。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: 773e600577b35019b8a3619c7eec3e93b77a4382
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085791"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>使用 PowerShell 啟用 Azure Stack 備份

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

使用 Windows PowerShell 啟用基礎結構備份服務，以定期備份：
 - 內部識別服務和根憑證
 - 使用者計劃、供應項目、訂用帳戶
 - 計算、儲存體和網路的使用者配額
 - 使用者金鑰保存庫密碼
 - 使用者 RBAC 角色和原則
 - 使用者儲存體帳戶

您可以存取 PowerShell Cmdlet 來啟用備份、開始備份，並透過作業員管理端點取得備份資訊。

## <a name="prepare-powershell-environment"></a>準備 PowerShell 環境

如需設定 PowerShell 環境的指示，請參閱[安裝適用於 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。 若要登入 Azure Stack，請參閱[設定 Azure Stack PowerShell 環境](azure-stack-powershell-configure-admin.md)。

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>提供備份共用、認證和加密金鑰可啟用備份

在相同的 PowerShell 工作階段中，新增您環境的變數來編輯下列 PowerShell 指令碼。 執行更新的指令碼，向基礎結構備份服務提供備份共用、認證和加密金鑰。

| 變數        | 說明   |
|---              |---                                        |
| $username       | 使用共用磁碟機位置的網域和使用者名稱 (需具有足夠存取權可讀取和寫入檔案)，來輸入**使用者名稱**。 例如： `Contoso\backupshareuser`。 |
| $password       | 輸入使用者的**密碼**。 |
| $sharepath      | 輸入**備份儲存位置**的路徑。 針對裝載在不同裝置的檔案共用路徑，您必須使用通用命名慣例 (UNC) 字串。 UNC 字串會指定資源的位置，例如共用的檔案或裝置。 若要確保備份資料的可用性，裝置應該位於不同的位置。 |
| $frequencyInHours | 頻率 (小時) 可決定建立備份的頻率。 預設值為 12。 排程器所支援的最大值為 12，最小值為 4。|
| $retentionPeriodInDays | 保留期間 (天) 可決定在外部位置保留多少天的備份。 預設值為 7。 排程器所支援的最大值為 14，最小值為 2。 備份如果比保留期間舊，系統就會自動從外部位置刪除該備份。|
| $encryptioncertpath | 適用於 1901 和更新版本。  在 Azure Stack 模組 1.7 版和更新版本中可使用參數。 加密憑證路徑中會指定 .CER 檔案的檔案路徑，其中包含用於資料加密的公開金鑰。 |
| $encryptionkey | 適用於組建 1811 或更早版本。 在 Azure Stack 模組 1.6 版或更早版本中可使用參數。 加密金鑰用於資料加密。 請使用 [New-AzsEncryptionKeyBase64](https://docs.microsoft.com/en-us/powershell/module/azs.backup.admin/new-azsencryptionkeybase64) Cmdlet 產生新的金鑰。 |
|     |     |

### <a name="enable-backup-on-1901-and-beyond-using-certificate"></a>使用憑證在 1901 和更新版本上啟用備份
```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $cert = New-SelfSignedCertificate `
        -DnsName "www.contoso.com" `
        -CertStoreLocation "cert:\LocalMachine\My" 

    New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 

    #make sure to export the PFX format of the certificate with the public and private keys and then delete the certifcate from the local certificate store of the machine where you created the certificate
    
    Export-Certificate `
        -Cert $cert `
        -FilePath c:\certs\AzSIBCCert.cer 

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionCertPath "c:\temp\cert.cer"
```
### <a name="enable-backup-on-1811-or-earlier-using-certificate"></a>使用憑證在 1811 或更早版本上啟用備份
```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $key = New-AzsEncryptionKeyBase64
    $Securekey = ConvertTo-SecureString -String ($key) -AsPlainText -Force

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $Securekey
```

   
##  <a name="confirm-backup-settings"></a>確認備份設定

在相同的 PowerShell 工作階段中，執行下列命令：

   ```powershell
    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
   ```

結果應該會類似以下範例輸出：

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>更新備份設定
您可以在同一個 PowerShell 工作階段中，更新備份保留期間和頻率的預設值。 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupConfiguration -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays

    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

結果應該會類似以下範例輸出：

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

### <a name="azure-stack-powershell"></a>Azure Stack PowerShell 
用於設定基礎結構備份的 PowerShell Cmdlet 是 Set-AzsBackupConfiguration。 在舊版中，此 Cmdlet 是 Set-AzsBackupShare。 此 Cmdlet 需要提供憑證。 如果基礎結構備份使用加密金鑰進行設定，則您無法更新加密金鑰或檢視屬性。 您必須使用 1.6 版的 Admin PowerShell。 

如果更新到 1901 之前已設定基礎結構備份，您可以使用 1.6 版的 Admin PowerShell 來設定和檢視加密金鑰。 1.6 版不允許您從加密金鑰更新憑證檔案。
如需更多安裝正確版本模組的相關資訊，請參閱[安裝 Azure Stack PowerShell](azure-stack-powershell-install.md)。 


## <a name="next-steps"></a>後續步驟

若要了解如何執行備份，請參閱[備份 Azure Stack](azure-stack-backup-back-up-azure-stack.md)

若要了解如何確認您的備份已執行，請參閱[在系統管理入口網站中確認已完成的備份](azure-stack-backup-back-up-azure-stack.md)
