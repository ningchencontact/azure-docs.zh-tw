---
title: 在 Azure Stack 上使用 SQL 資料庫 | Microsoft Docs
description: 了解如何在 Azure Stack 上部署 SQL 資料庫即服務，並了解部署 SQL Server 資源提供者配接器的快速步驟。
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: 4d2a00f04e5b07aeb3585fb3ab6c8966e0de7e19
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>在 Microsoft Azure Stack 上使用 SQL 資料庫

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

使用 SQL Server 資源提供者配接器，將 SQL 資料庫公開為 [Azure Stack](azure-stack-poc.md) 的服務。 安裝資源提供者並將它連接至一或多個 SQL Server 執行個體之後，您和您的使用者可以建立：
- 適用於雲端原生應用程式的資料庫。
- 以 SQL 為基礎的網站。
- 以 SQL 為基礎的工作負載。
您無須每次佈建裝載 SQL Server 的虛擬機器 (VM)。

資源提供者並未支援 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 的所有資料庫管理功能。 例如，不提供彈性資料庫集區及自動提升和降低資料庫效能的功能。 但是，資源提供者支援類似的建立、讀取、更新及刪除 (CRUD) 作業。 API 與 SQL Database 不相容。

## <a name="sql-resource-provider-adapter-architecture"></a>SQL 資源提供者配接器架構
資源提供者是由三個元件所組成：

- **SQL 資源提供者配接器 VM**這是執行提供者服務的 Windows 虛擬機器。
- **資源提供者本身**，它會處理佈建要求並公開資料庫資源。
- **裝載 SQL Server 的伺服器**：為稱為主控伺服器的資料庫提供容量。

您必須建立一個 (或多個) SQL Server 執行處理和/或提供對外部 SQL Server 執行個體的存取權。

> [!NOTE]
> 建立安裝在 Azure Stack 整合式系統上的主控伺服器時，必須從租用戶訂用帳戶建立。 無法從預設提供者訂用帳戶建立這些伺服器。 必須從租用戶入口網站或從具有適當登入的 PowerShell 工作階段來建立這些伺服器。 所有主控伺服器都是可計費的 VM，而且必須具有適當的授權。 服務管理員可以是租用戶訂用帳戶的擁有者。

## <a name="deploy-the-resource-provider"></a>部署資源提供者

1. 如果您尚未這樣做，請註冊您的開發套件並透過 Marketplace 管理下載下載 Windows Server 2016 Datacenter Core 映像。 您必須使用 Windows Server 2016 Core 映像。 您也可以使用指令碼建立 [Windows Server 2016 映像](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image) (\英文\)。 (請務必選取核心選項)。

2. 登入可存取具特殊權限端點 VM 的主機。

    - 在 Azure Stack 開發套件安裝上，登入實體主機。

    - 在多節點系統上，主機必須是可存取具特殊權限端點的系統。
    
    >[!NOTE]
    > 執行指令碼的系統必須為已安裝最新版 .NET 執行階段的 Windows 10 或 Windows Server 2016 系統。 否則，安裝會失敗。 Azure Stack SDK 主機便符合此準則。


3. 下載 SQL 資源提供者二進位檔。 然後執行自我解壓縮程式，以將內容解壓縮至暫存目錄。

    >[!NOTE] 
    > 資源提供者會有最低限度的相對應 Azure Stack 組建。 請務必下載適用於目前所執行 Azure Stack 版本的正確二進位檔。

    | Azure Stack 組建 | SQL 資源提供者安裝程式 |
    | --- | --- |
    | 1802：1.0.180302.1 | [SQL RP 版本 1.1.18.0](https://aka.ms/azurestacksqlrp1802) |
    | 1712：1.0.180102.3、1.0.180103.2 或 1.0.180106.1 (多節點) | [SQL RP 版本 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1711：1.0.171122.1 | [SQL RP 版本 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1710：1.0.171028.1 | [SQL RP 版本 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. Azure Stack 根憑證擷取自具特殊權限的端點。 針對 Azure Stack SDK，會在此程序的執行過程中建立自我簽署憑證。 針對整合式系統，您必須提供適當的憑證。

   若要提供自己的憑證，請依照下列方式，將 .pfx 檔案放在 **DependencyFilesLocalPath** 中：

    - \*.dbadapter.\<區域\>.\<外部 FQDN\> 的萬用字元憑證，或具有 sqladapter.dbadapter.\<區域\>.\<外部 FQDN\> 一般名稱的單一網站憑證。

    - 此憑證必須受到信任。 也就是說，信任鏈結必須存在，而不需要中繼憑證。

    - 在 DependencyFilesLocalPath 參數所指向的目錄中只能有一個憑證檔案。

    - 檔案名稱不得包含任何特殊字元。


5. 開啟**新的**已提升權限 (管理) 的 PowerShell 主控台，並變更至解壓縮檔案所在的目錄。 使用新視窗，以避免因系統上已載入不正確的 PowerShell 模組而可能發生的問題。

6. [安裝 Azure PowerShell 1.2.11 版](azure-stack-powershell-install.md)。

7. 執行 DeploySqlProvider.ps1 指令碼以執行下列步驟：

    - 將憑證和其他成品上傳到 Azure Stack 上的儲存體帳戶。
    - 發佈資源庫套件，以便讓您透過資源庫部署 SQL 資料庫。
    - 發佈用於部署主控伺服器的資源庫套件。
    - 使用在步驟 1 中建立的 Windows Server 2016 映像來部署 VM，然後安裝資源提供者。
    - 註冊與您資源提供者 VM 對應的本機 DNS 記錄。
    - 向本機 Azure Resource Manager 註冊您的資源提供者 (使用者和管理員)。
    - 依選擇在 RP 安裝期間安裝單一 Windows 更新

8. 建議您從 Marketplace Management 下載最新的 Windows Server 2016 Core 映像。 如果您需要安裝某個更新，您可以在本機相依性路徑中放置單一的 .MSU 套件。 如果找到多個 .MSU 檔案，指令碼將會失敗。


以下是您可以從 PowerShell 命令提示字元執行的範例。 (請務必視需要變更帳戶資訊和密碼)。

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 參數
您可以在命令列中指定這些參數。 如果您未指定參數，或任何參數驗證失敗，系統就會提示您提供所需的參數。

| 參數名稱 | 說明 | 註解或預設值 |
| --- | --- | --- |
| **CloudAdminCredential** | 雲端管理員的認證，這是存取具特殊權限端點所需的認證。 | _必要_ |
| **AzCredential** | Azure Stack 服務管理帳戶的認證。 使用部署 Azure Stack 時所使用的相同認證。 | _必要_ |
| **VMLocalCredential** | SQL 資源提供者 VM 之本機系統管理員帳戶的認證。 | _必要_ |
| **PrivilegedEndpoint** | 具特殊權限端點的 IP 位址或 DNS 名稱。 |  _必要_ |
| **DependencyFilesLocalPath** | 您的憑證 .pfx 檔案必須也放在這個目錄中。 | 選擇性 (如果是多節點，則為必要) |
| **DefaultSSLCertificatePassword** | .pfx 憑證的密碼。 | _必要_ |
| **MaxRetryCount** | 作業失敗時，您想要重試每個作業的次數。| 2 |
| **RetryDuration** | 重試之間的逾時間隔 (秒)。 | 120 |
| **解除安裝** | 移除資源提供者和所有關聯的資源 (請參閱下面的附註)。 | 否 |
| **DebugMode** | 防止在失敗時自動清除。 | 否 |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>使用 Azure Stack 入口網站確認部署是否成功

> [!NOTE]
>  在安裝指令碼執行完成之後，您必須重新整理入口網站，才能看到管理刀鋒視窗。


1. 以服務管理員身分登入管理入口網站。

2. 確認部署成功。 移至 [資源群組]。 然後選取 [**system.\<位置\>.sqladapter**] 資源群組。 確認全部四個部署都成功。

      ![確認 SQL 資源提供者的部署是否成功](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="update-the-sql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>更新 SQL 資源提供者配接器 (僅限多節點，組建 1710 和更新版本)
當 Azure Stack 組建更新時，可能會發行新的 SQL 資源提供者配接器。 建議您在現有配接器繼續運作的情況下，儘快更新至最新的組建。 更新必須依序安裝：您無法略過版本 (請參閱[部署資源提供者](#deploy-the-resource-provider)的步驟 3)。

若要更新資源提供者，您需使用 *UpdateSQLProvider.ps1* 指令碼。 此程序與用來安裝資源提供者的程序類似，如本文的[部署資源提供者](#deploy-the-resource-provider)一節所述。 指令碼隨附於所下載的資源提供者中。

*UpdateSQLProvider.ps1* 指令碼會以最新的資源提供者程式碼建立新 VM，然後將設定從舊 VM 移轉至新 VM。 移轉的設定包括資料庫和主控伺服器資訊，以及必要的 DNS 記錄。

此指令碼會要求使用與針對 DeploySqlProvider.ps1 指令碼所述的相同引數。 請一併在這裡提供憑證。 

建議您從 Marketplace Management 下載最新的 Windows Server 2016 Core 映像。 如果您需要安裝某個更新，您可以在本機相依性路徑中放置單一的 .MSU 套件。 如果找到多個 .MSU 檔案，指令碼將會失敗。

以下是一個您可以從 PowerShell 提示字元中執行的 *UpdateSQLProvider.ps1* 指令碼範例。 請務必視需要變更帳戶資訊和密碼： 

> [!NOTE]
> 此更新程序僅適用於整合式系統。

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 參數
您可以在命令列中指定這些參數。 如果您未指定參數，或任何參數驗證失敗，系統就會提示您提供所需的參數。

| 參數名稱 | 說明 | 註解或預設值 |
| --- | --- | --- |
| **CloudAdminCredential** | 雲端管理員的認證，這是存取具特殊權限端點所需的認證。 | _必要_ |
| **AzCredential** | Azure Stack 服務管理帳戶的認證。 使用與部署 Azure Stack 時所用認證相同的認證。 | _必要_ |
| **VMLocalCredential** | SQL 資源提供者 VM 之本機系統管理員帳戶的認證。 | _必要_ |
| **PrivilegedEndpoint** | 具特殊權限端點的 IP 位址或 DNS 名稱。 |  _必要_ |
| **DependencyFilesLocalPath** | 您的憑證 .pfx 檔案必須也放在這個目錄中。 | 選擇性 (如果是多節點，則為必要) |
| **DefaultSSLCertificatePassword** | .pfx 憑證的密碼。 | _必要_ |
| **MaxRetryCount** | 作業失敗時，您想要重試每個作業的次數。| 2 |
| **RetryDuration** |重試之間的逾時間隔 (秒)。 | 120 |
| **解除安裝** | 移除資源提供者和所有關聯的資源 (請參閱下面的附註)。 | 否 |
| **DebugMode** | 防止在失敗時自動清除。 | 否 |


## <a name="collect-diagnostic-logs"></a>收集診斷記錄
SQL 資源提供者是一部鎖定的虛擬機器。 如果從虛擬機器收集記錄變成必要，就會提供 PowerShell Just Enough Administration (JEA) 端點 _DBAdapterDiagnostics_ 來因應該目的。 透過這個端點，有兩個命令可用：

* Get-AzsDBAdapterLog - 準備一個包含 RP 診斷記錄的 zip 套件，然後將它放在工作階段使用者磁碟機上。 呼叫此命令時，可以不搭配任何參數，並且會收集過去四小時的記錄。
* Remove-AzsDBAdapterLog - 清除資源提供者 VM 上現有的記錄套件

在部署或更新 RP 以連線至診斷端點來擷取 RP 記錄的期間，會建立一個名為 _dbadapterdiag_ 的使用者帳戶。 此帳戶的密碼與在部署/更新期間為本機系統管理員帳戶提供的密碼相同。

若要使用這些命令，您將必須建立一個連至資源提供者虛擬機器的遠端 PowerShell 工作階段，然後叫用命令。 您可以視需要提供 FromDate 和 ToDate 參數。 如果您未指定其中一個參數或兩者都未指定，FromDate 將會是目前時間之前的四小時，而 ToDate 則會是目前的時間。

以下範例指令碼示範如何使用這些命令：

```
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

## <a name="maintenance-operations-integrated-systems"></a>維護作業 (整合式系統)
SQL 資源提供者是一部鎖定的虛擬機器。 若要更新資源提供者虛擬機器的安全性，可以透過 PowerShell Just Enough Administration (JEA) 端點 _DBAdapterMaintenance_ 來完成。

RP 安裝套件隨附一個指令碼，可協助執行這些作業。

### <a name="update-the-virtual-machine-operating-system"></a>更新虛擬機器作業系統
有數種方式可以更新 Windows Server VM：
* 使用目前已修補的 Windows Server 2016 Core 映像來安裝最新的資源提供者套件
* 在安裝或更新 RP 時安裝 Windows Update 套件


### <a name="update-the-virtual-machine-windows-defender-definitions"></a>更新虛擬機器 Windows Defender 定義

請依照下列步驟來更新 Defender 定義：

1. 從 [Windows Defender 定義](https://www.microsoft.com/en-us/wdsi/definitions) \(英文\) 下載 Windows Defender 定義更新

    在該頁面上的 “Manually download and install the definitions” (手動下載及安裝定義) 底下，下載 “Windows Defender Antivirus for Windows 10 and Windows 8.1” 64 位元檔案。 
    
    直接連結：https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. 建立連至 SQL RP 配接器虛擬機器維護端點的 PowerShell 工作階段
3. 使用維護端點工作階段將定義更新檔案複製到 DB 配接器電腦
4. 在維護 PowerShell 工作階段上，叫用 _Update-DBAdapterWindowsDefenderDefinitions_ 命令
5. 安裝之後，建議移除所使用的定義更新檔案。 您可以在維護工作階段上使用 _Remove-ItemOnUserDrive)_ 命令來移除它。


以下是一個更新 Defender 定義的範例指令碼 (請以實際值取代虛擬機器的位址或名稱)：

```
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

## <a name="remove-the-sql-resource-provider-adapter"></a>移除 SQL 資源提供者配接器

若要移除資源提供者，必須先移除任何相依性。

1. 確定您具有為此 SQL 資源提供者配接器版本下載的原始部署套件。

2. 必須從資源提供者刪除所有使用者資料庫。 (刪除使用者資料庫並不會刪除資料)。此工作應該由使用者自己執行。

3. 系統管理員必須從 SQL 資源提供者配接器刪除主控伺服器。

4. 系統管理員必須刪除參考 SQL 資源提供者配接器的所有方案。

5. 系統管理員必須刪除與 SQL 資源提供者配接器關聯的所有 SKU 和配額。

6. 使用下列元素來重新執行部署指令碼：
    - -Uninstall 參數
    - Azure Resource Manager 端點
    - DirectoryTenantID
    - 服務管理員帳戶的認證


## <a name="next-steps"></a>後續步驟

[新增主控伺服器](azure-stack-sql-resource-provider-hosting-servers.md)和[建立資料庫](azure-stack-sql-resource-provider-databases.md)。

嘗試其他 [PaaS 服務](azure-stack-tools-paas-services.md)，如 [MySQL Server 資源提供者](azure-stack-mysql-resource-provider-deploy.md)和[應用程式服務資源提供者](azure-stack-app-service-overview.md)。
