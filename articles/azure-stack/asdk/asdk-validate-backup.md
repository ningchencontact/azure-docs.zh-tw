---
title: 使用 ASDK 驗證 Azure Stack 備份 | Microsoft Docs
description: 如何使用 ASDK 驗證 Azure Stack 整合式系統備份。
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 12d5c2448bc6a5241f6aa4e283acdf9c5a0f602a
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265646"
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



### <a name="cloud-recovery-prerequisites"></a>雲端復原必要條件
開始 ASDK 的雲端復原部署之前，請確定您具有下列資訊：

|必要條件|說明|
|-----|-----|
|備份共用路徑。|最新 Azure Stack 備份的 UNC 檔案共用路徑將用來復原 Azure Stack 基礎結構資訊。 在雲端復原部署過程中會建立此本機共用。|
|備份加密金鑰。|此加密金鑰用來排程要使用 Azure Stack 管理入口網站執行的基礎結構備份。|
|要還原的備份識別碼。|備份識別碼 (採用 "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" 的英數字元形式)，可識別在雲端復原期間還原的備份。|
|時間伺服器 IP。|Azure Stack 部署需要有效的時間伺服器 IP (例如 132.163.97.2)。|
|外部憑證密碼。|Azure Stack 所用外部憑證的密碼。 CA 備份包含需要使用此密碼還原的外部憑證。|
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

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>在雲端復原模式部署 ASDK
**InstallAzureStackPOC.ps1** 指令碼用來起始雲端復原。 

> [!IMPORTANT]
> ASDK 安裝支援正好一個網路介面卡 (NIC) 進行網路運作。 如果您有多個 NIC，在執行部署指令碼之前，請先確定僅啟用一個 NIC (並停用其他所有 NIC)。

針對您的環境修改下列 PowerShell 命令，加以執行以在雲端復原模式中部署 ASDK：

```powershell
cd C:\CloudDeployment\Setup     
$adminPass = Get-Credential Administrator
$key = ConvertTo-SecureString "<Your backup encryption key>" -AsPlainText -Force ` 
$certPass = Read-Host -AsSecureString  

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
-BackupEncryptionKeyBase64 $key -BackupStoreCredential $adminPass -BackupId "<Backup ID to restore>" `
-TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="restore-infrastructure-data-from-backup"></a>從備份還原基礎結構資料
雲端復原部署成功之後，您必須使用 **Restore-AzureStack** Cmdlet 完成還原。 

以 Azure Stack 操作員身分登入之後，[安裝 Azure Stack PowerShell](asdk-post-deploy.md#install-azure-stack-powershell)，然後以您的備份識別碼取代 `Name` 參數，執行下列命令：

```powershell
Restore-AzsBackup -Name "<BackupID>"
```
在呼叫這個 Cmdlet 之後等候 60 分鐘，開始驗證雲端復原 ASDK 上的備份資料。

## <a name="next-steps"></a>後續步驟
[註冊 Azure Stack](asdk-register.md)

