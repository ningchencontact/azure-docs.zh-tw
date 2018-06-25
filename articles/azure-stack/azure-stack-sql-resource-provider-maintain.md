---
title: 在 Azure Stack 上維護 SQL 資源提供者 | Microsoft Docs
description: 了解如何在 Azure Stack 上維護 SQL 資源提供者服務。
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e7ddbe1235b3957a1e0cb7693ee728bfdbf9db6b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295654"
---
# <a name="maintenance-operations"></a>維護作業 
SQL 資源提供者是一部鎖定的虛擬機器。 若要更新資源提供者虛擬機器的安全性，可以透過 PowerShell Just Enough Administration (JEA) 端點 _DBAdapterMaintenance_ 來完成。 RP 安裝套件隨附一個指令碼，可協助執行這些作業。

## <a name="patching-and-updating"></a>修補和更新
SQL 資源提供者不會隨著 Azure Stack 提供，因為它是附加元件。 Microsoft 將視需要為 SQL 資源提供者提供更新。 SQL 資源提供者會在預設提供者訂用帳戶底下的 [使用者] 虛擬機器上具現化。 因此，就必須提供 Windows 修補程式、防毒特徵標記等等。隨著修補和更新週期提供的 Windows 更新套件可用來將更新套用至 Windows VM。 釋出更新的配接器時，將提供指令碼來套用更新。 此指令碼會建立新的 RP VM，並移轉您已經具有的任何狀態。

 ## <a name="backuprestoredisaster-recovery"></a>備份/還原/災害復原
 SQL 資源提供者不會隨著 Azure Stack BC-DR 處理序進行備份，因為它是附加元件。 會提供指令碼，以便：
- 備份必要的狀態資訊 (儲存在 Azure Stack 儲存體帳戶中)
- 在完整的堆疊復原變得必要時還原 RP。
必須先復原資料庫伺服器 (如有必要)，才能復原資源提供者。

## <a name="updating-sql-credentials"></a>更新 SQL 認證
您需負責建立及維護 SQL 伺服器上的系統管理員帳戶。 資源提供者需要具有這些權限的帳戶，才能代表使用者管理資料庫 - 不需要存取那些資料庫中的資料。 如果需要更新 SQL Server 的 SA 密碼，您可以使用資源提供者的系統管理員介面的更新功能，來變更資源提供者所使用的已儲存密碼。 這些密碼會儲存在 Azure Stack 執行個體上的金鑰保存庫中。

若要修改設定，請按一下 [瀏覽] &gt; [管理資源] &gt; [SQL 主控伺服器] &gt; [SQL 登入] 並選取登入名稱。 必須先在 SQL 執行個體上 (必要時還需在任何複本上) 進行變更。 在 [設定] 面板中，按一下 [密碼]。

![更新管理員密碼](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>祕密輪替 
這些指示只適用於 Azure Stack 整合系統 1804 版或更新版本。請勿在早於 1804 版本的 Azure Stack 上嘗試使用秘密輪替。

搭配使用 SQL 和 MySQL 資源提供者與 Azure Stack 整合系統時，您可以輪替下列基礎結構 (部署) 祕密：
- [部署期間所提供的](azure-stack-pki-certs.md)外部 SSL 憑證。
- 部署期間所提供的資源提供者 VM 本機系統管理員帳戶密碼。
- 資源提供者診斷使用者 (dbadapterdiag) 密碼。

### <a name="powershell-examples-for-rotating-secrets"></a>輪替祕密的 PowerShell 範例

**同時變更所有祕密**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  `
    -VMLocalCredential $localCreds
```

**只變更診斷使用者密碼**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd 
```

**變更 VM 本機系統管理員帳戶密碼**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**變更 SSL 憑證**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd 
```

### <a name="secretrotationsqlproviderps1-parameters"></a>SecretRotationSQLProvider.ps1 參數

|參數|說明|
|-----|-----|
|AzCredential|Azure Stack 服務系統管理員帳戶認證。|
|CloudAdminCredential|Azure Stack 雲端管理網域帳戶認證。|
|PrivilegedEndpoint|用來存取 Get-AzureStackStampInformation 的特殊權限端點。|
|DiagnosticsUserPassword|診斷使用者密碼。|
|VMLocalCredential|MySQLAdapter VM 的本機系統管理員帳戶。|
|DefaultSSLCertificatePassword|預設 SSL 憑證 (*pfx) 密碼。|
|DependencyFilesLocalPath|相依性檔案本機路徑。|
|     |     |

### <a name="known-issues"></a>已知問題
**問題**：如果祕密輪替自訂指令碼在執行時失敗，就不會自動收集祕密輪替記錄。

**因應措施**：使用 Get-AzsDBAdapterLogs Cmdlet 來收集所有資源提供者記錄，包括 C:\Logs 底下的 AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log。

## <a name="update-the-virtual-machine-operating-system"></a>更新虛擬機器作業系統
有數種方式可以更新 Windows Server VM：
- 使用目前已修補的 Windows Server 2016 Core 映像來安裝最新的資源提供者套件
- 在安裝或更新 RP 時安裝 Windows Update 套件

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>更新虛擬機器 Windows Defender 定義
請依照下列步驟來更新 Defender 定義：

1. 從 [Windows Defender 定義](https://www.microsoft.com/en-us/wdsi/definitions) \(英文\) 下載 Windows Defender 定義更新。

    在該頁面上的 “Manually download and install the definitions” (手動下載及安裝定義) 底下，下載 “Windows Defender Antivirus for Windows 10 and Windows 8.1” 64 位元檔案。 
    
    直接連結：https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64。

2. 建立連至 SQL RP 配接器虛擬機器維護端點的 PowerShell 工作階段
3. 使用維護端點工作階段將定義更新檔案複製到 DB 配接器電腦
4. 在維護 PowerShell 工作階段上，叫用 _Update-DBAdapterWindowsDefenderDefinitions_ 命令
5. 安裝之後，建議移除所使用的定義更新檔案。 您可以在維護工作階段上使用 _Remove-ItemOnUserDrive)_ 命令來移除它。


以下是一個更新 Defender 定義的範例指令碼 (請以實際值取代虛擬機器的位址或名稱)：

```powershell
# Set credentials for the RP VM local admin user
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession 
```


## <a name="collect-diagnostic-logs"></a>收集診斷記錄
SQL 資源提供者是一部鎖定的虛擬機器。 如果從虛擬機器收集記錄變成必要，就會提供 PowerShell Just Enough Administration (JEA) 端點 _DBAdapterDiagnostics_ 來因應該目的。 透過這個端點，有兩個命令可用：

- **Get-AzsDBAdapterLog**。 準備一個包含 RP 診斷記錄的 zip 套件，然後將它放在工作階段使用者磁碟機上。 呼叫此命令時，可以不搭配任何參數，並且會收集過去四小時的記錄。
- **Remove-AzsDBAdapterLog**。 清除資源提供者 VM 上現有的記錄套件

在部署或更新 RP 以連線至診斷端點來擷取 RP 記錄的期間，會建立一個名為 **dbadapterdiag** 的使用者帳戶。 此帳戶的密碼與在部署/更新期間為本機系統管理員帳戶提供的密碼相同。

若要使用這些命令，您將必須建立一個連至資源提供者虛擬機器的遠端 PowerShell 工作階段，然後叫用命令。 您可以視需要提供 FromDate 和 ToDate 參數。 如果您未指定其中一個參數或兩者都未指定，FromDate 將會是目前時間之前的四小時，而 ToDate 則會是目前的時間。

以下範例指令碼示範如何使用這些命令：

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="next-steps"></a>後續步驟
[新增 SQL Server 主控伺服器](azure-stack-sql-resource-provider-hosting-servers.md)
