---
title: 使用 ASDK 驗證 Azure Stack 備份 | Microsoft Docs
description: 如何使用 ASDK 驗證 Azure Stack 整合式系統備份。
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 02/15/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 02/15/2019
ms.openlocfilehash: 31c5d068c8fcd0b6edea7cff63098131d848a14e
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416373"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>使用 ASDK 來驗證 Azure Stack 備份
部署 Azure Stack 及佈建使用者資源 (例如供應項目、方案、配額和訂用帳戶) 之後，您應該[啟用 Azure Stack 基礎結構備份](../azure-stack-backup-enable-backup-console.md)。 排程及執行定期基礎結構備份，可確保在發生重大硬體或服務失敗時不會遺失基礎結構管理資料。

> [!TIP]
> 我們建議您先[執行隨選備份](../azure-stack-backup-back-up-azure-stack.md)，再開始此程序，以確保您有一份最新的基礎結構資料可用。 務必在備份順利完成後，擷取備份識別碼。 雲端復原期間需要此識別碼。 

Azure Stack 基礎結構備份包含可以在 Azure Stack 重新部署期間還原的雲端相關重要資料。 您可以使用 ASDK 來驗證這些備份，而不會影響生產雲端。 

下列案例支援在 ASDK 上驗證備份：

|案例|目的|
|-----|-----|
|從整合式解決方案驗證基礎結構備份。|短期驗證備份中的資料是有效的。|
|了解端對端復原工作流程。|使用 ASDK 來驗證整個備份和還原體驗。|
|     |     |

在 ASDK 上驗證備份時，**不**支援下列案例：

|案例|目的|
|-----|-----|
|ASDK 組建對組建備份和還原。|將備份資料從舊版 ASDK 還原到較新的版本。|
|     |     |


## <a name="cloud-recovery-deployment"></a>雲端復原部署
執行 ASDK 的雲端復原部署，可以驗證整合式系統部署中的基礎結構備份。 在這類部署中，於主機電腦上安裝 ASDK 後，就會從備份還原特定服務資料。

### <a name="prereqs"></a>雲端復原先決條件
開始 ASDK 的雲端復原部署之前，請確定您具有下列資訊：

**UI 安裝程式需求**

*目前的 UI 安裝程式僅支援加密金鑰*

|必要條件|說明|
|-----|-----|
|備份共用路徑|最新 Azure Stack 備份的 UNC 檔案共用路徑將用來復原 Azure Stack 基礎結構資訊。 在雲端復原部署過程中會建立此本機共用。|
|要還原的備份識別碼|備份識別碼 (採用 "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" 的英數字元形式)，可識別在雲端復原期間還原的備份。|
|時間伺服器 IP|Azure Stack 部署需要有效的時間伺服器 IP (例如 132.163.97.2)。|
|外部憑證密碼|Azure Stack 所用外部憑證的密碼。 CA 備份包含需要使用此密碼還原的外部憑證。|
|備份加密金鑰|如果您已升級至 Azure Stack 1901 版或更新的版本，且仍以加密金鑰設定備份設定，則為必要。 加密金鑰自 1901 版起已被取代。 安裝程式將支援至少 3 個版本回溯相容性模式中的加密金鑰。 一旦您更新備份設定以使用憑證，請參閱下一個表格以取得所需的資訊。|

|     |     | 

**PowerShell 安裝程式需求**

*目前的 PowerShell 安裝程式支援加密金鑰或解密憑證*

|必要條件|說明|
|-----|-----|
|備份共用路徑|最新 Azure Stack 備份的 UNC 檔案共用路徑將用來復原 Azure Stack 基礎結構資訊。 在雲端復原部署過程中會建立此本機共用。|
|要還原的備份識別碼|備份識別碼 (採用 "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" 的英數字元形式)，可識別在雲端復原期間還原的備份。|
|時間伺服器 IP|Azure Stack 部署需要有效的時間伺服器 IP (例如 132.163.97.2)。|
|外部憑證密碼|Azure Stack 所用外部憑證的密碼。 CA 備份包含需要使用此密碼還原的外部憑證。|
|解密憑證密碼|選用。 只有在使用憑證加密備份時才為必要。 密碼適用於自我簽署憑證 (.pfx)，其包含解密備份資料所需的私密金鑰。|
|備份加密金鑰|選用。 如果您已升級至 Azure Stack 1901 版或更新的版本，且仍以加密金鑰設定備份設定，則為必要。 安裝程式將支援至少 3 個版本回溯相容性模式中的加密金鑰。 一旦您更新備份設定以使用憑證，您必須提供解密憑證的密碼。|
|     |     | 

## <a name="prepare-the-host-computer"></a>準備主機電腦 
如同在一般 ASDK 部署中，必須備妥 ASDK 主機系統環境以便安裝。 當開發套件主機電腦做好準備時，它會從 CloudBuilder.vhdx 虛擬機器硬碟開機，以開始進行 ASDK 部署。

在 ASDK 主機電腦上，下載新的 cloudbuilder.vhdx (其對應至已備份的相同 Azure Stack 版本)，並遵循[準備 ASDK 主機電腦](asdk-prepare-host.md)的指示。

從 cloudbuilder.vhdx 重新啟動主機伺服器之後，您必須建立檔案共用並將您的備份資料複製到其中。 執行安裝的帳戶應可存取此檔案共用，下列範例 PowerShell 命令中的系統管理員： 

```powershell
$shares = New-Item -Path "c:\" -Name "Shares" -ItemType "directory"
$azsbackupshare = New-Item -Path $shares.FullName -Name "AzSBackups" -ItemType "directory"
New-SmbShare -Path $azsbackupshare.FullName -FullAccess ($env:computername + "\Administrator")  -Name "AzSBackups"
```

接下來，將最新的 Azure Stack 備份檔案複製到新建立的共用。 在共用內的資料夾結構應該是：`\\<ComputerName>\AzSBackups\MASBackup\<BackupID>\`。

最後，將解密憑證 (.pfx) 複製到憑證目錄：`C:\CloudDeployment\Setup\Certificates\`，並將檔案重新命名為 `BackupDecryptionCert.pfx`。

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>在雲端復原模式部署 ASDK

> [!IMPORTANT]
> 1. 目前的安裝程式 UI 僅支援加密金鑰。 您只能從持續使用加密金鑰的系統驗證備份。 如果已使用憑證在整合系統或 ASDK 加密備份，您必須使用 PowerShell 安裝程式 (**InstallAzureStackPOC.ps1**)。 
> 2. PowerShell 安裝程式 (**InstallAzureStackPOC.ps1**) 支援加密金鑰或憑證。
> 3. ASDK 安裝支援正好一個網路介面卡 (NIC) 進行網路運作。 如果您有多個 NIC，在執行部署指令碼之前，請先確定僅啟用一個 NIC (並停用其他所有 NIC)。

### <a name="use-the-installer-ui-to-deploy-the-asdk-in-recovery-mode"></a>使用安裝程式 UI 在復原模式中部署 ASDK
此節中的步驟說明如何使用透過下載並執行 **asdk-installer.ps1** PowerShell 指令碼所提供的圖形化使用者介面 (GUI) 來部署 ASDK。

> [!NOTE]
> Azure Stack 開發套件的安裝程式使用者介面是以 WCF 和 PowerShell 為基礎的開放來源指令碼。

> [!IMPORTANT]
> 目前的安裝程式 UI 僅支援加密金鑰。

1. 在主機電腦成功開機進入 CloudBuilder.vhdx 映像之後，使用您在[準備 ASDK 安裝的開發套件主機電腦](asdk-prepare-host.md)時所指定的系統管理員認證進行登入。 這應該與開發套件主機本機系統管理員認證相同。
2. 開啟提升權限的 PowerShell 主控台，然後執行 **&lt;磁碟機代號>\AzureStack_Installer\asdk-installer.ps1** PowerShell 指令碼。 在 CloudBuilder.vhdx 映像中，此指令碼現在可能位於 C:\ 以外的其他磁碟機上。 按一下 [復原] 。

    ![ASDK 安裝程式指令碼](media/asdk-validate-backup/1.PNG) 

3. 在身分識別提供者和認證頁面上，輸入您的 Azure AD 目錄資訊 (選擇性) 和 ASDK 主機電腦的本機系統管理員密碼。 按 [下一步] 。

    ![身分識別與認證頁面](media/asdk-validate-backup/2.PNG) 

4. 選取要供 ASDK 主機使用的網路介面卡，然後按一下 [下一步]。 在 ASDK 安裝期間，將會停用所有其他網路介面。 

    ![網路介面卡介面](media/asdk-validate-backup/3.PNG) 

5. 在 [網路設定] 頁面上，提供有效的時間伺服器和 DNS 轉寄站 IP 位址。 按 [下一步] 。

    ![[網路設定] 頁面](media/asdk-validate-backup/4.PNG) 

6. 驗證網路介面卡內容後，按一下 [下一步]。 

    ![網路卡設定驗證](media/asdk-validate-backup/5.PNG) 

7. 提供稍早 [備份設定] 頁面上[先決條件](#prereqs)一節中所述的必要資訊，以及用於存取共用的使用者名稱與密碼。 按一下 [下一步]： 

   ![[備份設定] 頁面](media/asdk-validate-backup/6.PNG) 

8. 在 [摘要] 頁面上檢閱用於部署 ASDK 的部署指令碼。 按一下 [部署] 以開始部署。 

    ![摘要頁面](media/asdk-validate-backup/7.PNG) 


### <a name="use-powershell-to-deploy-the-asdk-in-recovery-mode"></a>使用 PowerShell 在復原模式中部署 ASDK

針對您的環境修改下列 PowerShell 命令，加以執行以在雲端復原模式中部署 ASDK：

**使用 InstallAzureStackPOC.ps1 指令碼來透過加密金鑰起始雲端復原。**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$key = Read-Host -AsSecureString -Prompt "Your backup encryption key"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupEncryptionKeyBase64 $key `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

**使用 InstallAzureStackPOC.ps1 指令碼來透過解密憑證起始雲端復原。**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$decryptioncertpassword  = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupDecryptionCertPassword $decryptioncertpassword `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="complete-cloud-recovery"></a>完成雲端復原 
雲端復原部署成功之後，您必須使用 **Restore-AzureStack** Cmdlet 完成還原。 

以 Azure Stack 操作員身分登入、[安裝 Azure Stack PowerShell](asdk-post-deploy.md#install-azure-stack-powershell)，並執行下列命令，以指定當從備份復原時要使用的憑證和密碼：

**具有憑證檔案的復原模式**

> [!NOTE] 
> 基於安全理由，Azure Stack 部署不會保存解密憑證。 您必須再次提供解密憑證和相關聯的密碼。

```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>" `
 -DecryptionCertPath "<path to decryption certificate with file name (.pfx)>" `
 -DecryptionCertPassword $decryptioncertpassword
```

**具有加密金鑰的復原模式**
```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>"
```

在呼叫這個 Cmdlet 之後等候 60 分鐘，開始驗證雲端復原 ASDK 上的備份資料。

## <a name="next-steps"></a>後續步驟
[註冊 Azure Stack](asdk-register.md)

