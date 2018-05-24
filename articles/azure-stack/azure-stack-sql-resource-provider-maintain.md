---
title: 在 Azure Stack 上使用 SQL 資料庫 | Microsoft Docs
description: 了解如何在 Azure Stack 上部署 SQL 資料庫即服務，並了解部署 SQL Server 資源提供者配接器的快速步驟。
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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 53436d131672622ae1a72a1bb84d5aa83fdbdc0c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2018
ms.locfileid: "33207930"
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

## <a name="update-the-virtual-machine-operating-system"></a>更新虛擬機器作業系統
有數種方式可以更新 Windows Server VM：
* 使用目前已修補的 Windows Server 2016 Core 映像來安裝最新的資源提供者套件
* 在安裝或更新 RP 時安裝 Windows Update 套件

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>更新虛擬機器 Windows Defender 定義
請依照下列步驟來更新 Defender 定義：

1. 從 [Windows Defender 定義](https://www.microsoft.com/en-us/wdsi/definitions) \(英文\) 下載 Windows Defender 定義更新

    在該頁面上的 “Manually download and install the definitions” (手動下載及安裝定義) 底下，下載 “Windows Defender Antivirus for Windows 10 and Windows 8.1” 64 位元檔案。 
    
    直接連結：https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. 建立連至 SQL RP 配接器虛擬機器維護端點的 PowerShell 工作階段
3. 使用維護端點工作階段將定義更新檔案複製到 DB 配接器電腦
4. 在維護 PowerShell 工作階段上，叫用 _Update-DBAdapterWindowsDefenderDefinitions_ 命令
5. 安裝之後，建議移除所使用的定義更新檔案。 您可以在維護工作階段上使用 _Remove-ItemOnUserDrive)_ 命令來移除它。


以下是一個更新 Defender 定義的範例指令碼 (請以實際值取代虛擬機器的位址或名稱)：

```powershell
# Set credentials for the diagnostic user
$diagPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$diagCreds = New-Object System.Management.Automation.PSCredential `
    ("dbadapterdiag", $vmLocalAdminPass)$diagCreds = Get-Credential

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "XX.XX.XX.XX"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64 `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $diagCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\mpam-fe.exe"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinitions -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```


## <a name="collect-diagnostic-logs"></a>收集診斷記錄
SQL 資源提供者是一部鎖定的虛擬機器。 如果從虛擬機器收集記錄變成必要，就會提供 PowerShell Just Enough Administration (JEA) 端點 _DBAdapterDiagnostics_ 來因應該目的。 透過這個端點，有兩個命令可用：

* Get-AzsDBAdapterLog - 準備一個包含 RP 診斷記錄的 zip 套件，然後將它放在工作階段使用者磁碟機上。 呼叫此命令時，可以不搭配任何參數，並且會收集過去四小時的記錄。
* Remove-AzsDBAdapterLog - 清除資源提供者 VM 上現有的記錄套件

在部署或更新 RP 以連線至診斷端點來擷取 RP 記錄的期間，會建立一個名為 _dbadapterdiag_ 的使用者帳戶。 此帳戶的密碼與在部署/更新期間為本機系統管理員帳戶提供的密碼相同。

若要使用這些命令，您將必須建立一個連至資源提供者虛擬機器的遠端 PowerShell 工作階段，然後叫用命令。 您可以視需要提供 FromDate 和 ToDate 參數。 如果您未指定其中一個參數或兩者都未指定，FromDate 將會是目前時間之前的四小時，而 ToDate 則會是目前的時間。

以下範例指令碼示範如何使用這些命令：

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<see above>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, $diagnosticsUserPassword)
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
