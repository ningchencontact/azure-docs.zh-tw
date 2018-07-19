---
title: 使用 PowerShell 啟用 Azure Stack 備份 | Microsoft Docs
description: 使用 Windows PowerShell 啟用「基礎結構備份服務」，以便在發生失敗時，可以將 Azure Stack 還原。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 4fb40904e59e78e416d4598472a6adeb498e49f4
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968879"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>使用 PowerShell 啟用 Azure Stack 備份

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

使用 Windows PowerShell 啟用基礎結構備份服務，以定期備份：
 - 內部識別服務和根憑證
 - 使用者計劃、供應項目、訂用帳戶
 - Keyvault 祕密
 - 使用者 RBAC 角色和原則

您可以存取 PowerShell Cmdlet 來啟用備份、開始備份，並透過作業員管理端點取得備份資訊。

## <a name="prepare-powershell-environment"></a>準備 PowerShell 環境

如需設定 PowerShell 環境的指示，請參閱[安裝 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。 若要登入 Azure Stack，請參閱[設定 Azure Stack PowerShell 環境](azure-stack-powershell-configure-admin.md)。

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>提供備份共用、認證和加密金鑰可啟用備份

在相同的 PowerShell 工作階段中，新增您環境的變數來編輯下列 PowerShell 指令碼。 執行更新的指令碼，向基礎結構備份服務提供備份共用、認證和加密金鑰。

| 變數        | 說明   |
|---              |---                                        |
| $username       | 使用共用磁碟機位置的網域和使用者名稱 (需具有足夠存取權可讀取和寫入檔案)，來輸入**使用者名稱**。 例如： `Contoso\backupshareuser`。 |
| $key            | 鍵入用來為各個備份加密的**加密金鑰**。 |
| $password       | 輸入使用者的**密碼**。 |
| $sharepath      | 輸入**備份儲存位置**的路徑。 針對裝載在不同裝置的檔案共用路徑，您必須使用通用命名慣例 (UNC) 字串。 UNC 字串會指定資源的位置，例如共用的檔案或裝置。 若要確保備份資料的可用性，裝置應該位於不同的位置。 |

   ```powershell
    $username = "domain\backupadmin"
   
    $Secure = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    $Encrypted = ConvertFrom-SecureString -SecureString $Secure
    $password = ConvertTo-SecureString -String $Encrypted
    
    $BackupEncryptionKeyBase64 = ""
    $tempEncryptionKeyString = ""
    foreach($i in 1..64) { $tempEncryptionKeyString += -join ((65..90) + (97..122) | Get-Random | % {[char]$_}) }
    $tempEncryptionKeyBytes = [System.Text.Encoding]::UTF8.GetBytes($tempEncryptionKeyString)
    $BackupEncryptionKeyBase64 = [System.Convert]::ToBase64String($tempEncryptionKeyBytes)
    $BackupEncryptionKeyBase64
    
    $Securekey = ConvertTo-SecureString -String $BackupEncryptionKeyBase64 -AsPlainText -Force
    $Encryptedkey = ConvertFrom-SecureString -SecureString $Securekey
    $key = ConvertTo-SecureString -String $Encryptedkey
    
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    Set-AzSBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>確認備份設定

在相同的 PowerShell 工作階段中，執行下列命令：

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity
   ```

成果應會類似於以下輸出結果：

   ```powershell
    Path                        : \\serverIP\AzSBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
   ```

## <a name="next-steps"></a>後續步驟

 - 若要了解如何執行備份，請參閱[備份 Azure Stack](azure-stack-backup-back-up-azure-stack.md )。  
 - 若要了解如何確認您的備份已執行，請參閱[在系統管理入口網站中確認已完成的備份](azure-stack-backup-back-up-azure-stack.md )。
