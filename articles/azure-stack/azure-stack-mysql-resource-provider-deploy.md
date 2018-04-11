---
title: 在 Azure Stack 上使用 MySQL 資料庫做為 PaaS | Microsoft Docs
description: 了解如何在 Azure Stack 上部署「MySQL 資源提供者」並提供 MySQL 資料庫即服務。
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
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: 66e1d5691b431be0c3d040570b13e8d16b1669ef
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>在 Microsoft Azure Stack 上使用 MySQL 資料庫

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

您可以在 Azure Stack 上部署 MySQL 資源提供者。 在部署資源提供者之後，您可以透過 Azure Resource Manager 部署範本來建立 MySQL 伺服器和資料庫。 您也可以提供 MySQL 資料庫即服務。 

網站上常用的 MySQL 資料庫支援許多網站平台。 例如，在部署資源提供之後，您可以從 Azure Stack 的 Web Apps 平台即服務 (PaaS) 附加元件建立 WordPress 網站。

若要在無法存取網際網路的系統上部署 MySQL 提供者，請將 [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi)檔複製到本機共用。 接著，在提示您輸入共用名稱時，提供該共用名稱。 您必須安裝 Azure 和 Azure Stack PowerShell 模組。


## <a name="mysql-server-resource-provider-adapter-architecture"></a>MySQL 伺服器資源提供者配接器架構

資源提供者由三個元件組成：

- **MySQL 資源提供者配接器 VM**：這是執行提供者服務的 Windows 虛擬機器。

- **資源提供者本身**，它會處理佈建要求並公開資料庫資源。

- **裝載 MySQL 伺服器的伺服器**：為稱為主控伺服器的資料庫提供容量。

此版本已不再建立 MySQL 執行個體。 這意謂著您必須自行建立它們和/或提供對外部 SQL 執行個體的存取權。 瀏覽 [Azure Stack 快速入門資源庫](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows)，以取得可以執行下列動作的範例範本：
- 為您建立 MySQL 伺服器。
- 從 Azure Marketplace 下載和部署 MySQL 伺服器。

> [!NOTE]
> 建立安裝在 Azure Stack 整合式系統上的主控伺服器時，必須從租用戶訂用帳戶建立。 無法從預設提供者訂用帳戶建立這些伺服器。 必須從租用戶入口網站或從具有適當登入的 PowerShell 工作階段來建立這些伺服器。 所有主控伺服器都是可計費的 VM，而且必須具有適當的授權。 服務管理員可以是租用戶訂用帳戶的擁有者。

### <a name="required-privileges"></a>必要的權限
系統帳戶必須具有下列權限：

1.  資料庫：建立、卸除
2.  登入：建立、設定、卸除、授與、撤銷

## <a name="deploy-the-resource-provider"></a>部署資源提供者

1. 如果您尚未這樣做，請註冊您的開發套件，並下載可透過 Marketplace 管理下載的「Windows Server 2016 Datacenter Core」映像。 您必須使用 Windows Server 2016 Core 映像。 您也可以使用指令碼建立 [Windows Server 2016 映像](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image) (\英文\)。 (請務必選取核心選項)。


2. 登入可存取具特殊權限端點 VM 的主機。

    - 在 Azure SDK 安裝上，登入實體主機。 
    - 在多節點系統上，主機必須是可存取具特殊權限端點的系統。
    
    >[!NOTE]
    > 執行指令碼的系統「必須」是已安裝最新版 .NET 執行階段的 Windows 10 或 Windows Server 2016 系統。 否則，安裝會失敗。 Azure Stack SDK 主機便符合此準則。
    

3. 下載 MySQL 資源提供者二進位檔。 然後執行自我解壓縮程式，以將內容解壓縮至暫存目錄。

    >[!NOTE] 
    > 資源提供者會有最低限度的相對應 Azure Stack 組建。 請務必下載適用於目前所執行 Azure Stack 版本的正確二進位檔。

    | Azure Stack 組建 | MySQL RP 安裝程式 |
    | --- | --- |
    | 1802：1.0.180302.1 | [MySQL RP 版本 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) |
    | 1712：1.0.180102.3 或 1.0.180106.1 (多節點) | [MySQL RP 1.1.14.0 版](https://aka.ms/azurestackmysqlrp1712) |
    | 1711：1.0.171122.1 | [MySQL RP 版本 1.1.12.0](https://aka.ms/azurestackmysqlrp1711) |
    | 1710：1.0.171028.1 | [MySQL RP 版本 1.1.8.0](https://aka.ms/azurestackmysqlrp1710) |

4.  針對 Azure SDK，會在此程序的執行過程中建立自我簽署憑證。 對於多節點，您必須提供適當的憑證。

    如果您需要提供自己的憑證，請將符合下列準則的 .pfx 檔案放在 **DependencyFilesLocalPath** 中：

    - \*.dbadapter.\<區域\>.\<外部 FQDN\> 的萬用字元憑證，或具有 mysqladapter.dbadapter.\<區域\>.\<\> 一般名稱的單一網站憑證。

    - 此憑證必須受到信任。 也就是說，信任鏈結必須存在，而不需要中繼憑證。

    - DependencyFilesLocalPath 中僅存在單一憑證檔案。
    
    - 檔案名稱不得包含任何特殊字元或空格。


5. 開啟一個已提高權限 (系統管理) 的**新** PowerShell 主控台。 然後變更至檔案的解壓縮目錄。 使用新視窗，以避免因系統上已載入不正確的 PowerShell 模組而可能發生的問題。

6. [安裝 Azure PowerShell 1.2.11 版](azure-stack-powershell-install.md)。

7. 執行 `DeployMySqlProvider.ps1` 指令碼。

指令碼會執行這些步驟：

* 下載 MySQL 連接器二進位檔 (可離線提供)。
* 將憑證和其他成品上傳到 Azure Stack 上的儲存體帳戶。
* 發佈資源庫套件，以便讓您透過資源庫部署 SQL 資料庫。
* 發佈用於部署主控伺服器的資源庫套件。
* 使用在步驟 1 中建立的 Windows Server 2016 映像來部署 VM。 它也會安裝資源提供者。
* 註冊與您資源提供者 VM 對應的本機 DNS 記錄。
* 向本機 Azure Resource Manager 註冊您的資源提供者 (租用戶和管理員)。


您可以：
- 在命令列上指定所需的參數。
- 在不使用任何參數的情況下執行，然後在出現提示時輸入參數。

以下是您可以從 PowerShell 命令提示字元執行的範例。 請務必視需要變更帳戶資訊和密碼：


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
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files.
# Find the ERCS01 IP address first, and make sure the certificate
# file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```


### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 參數
您可以在命令列中指定這些參數。 如果您未指定參數，或任何參數驗證失敗，系統就會提示您提供所需的參數。

| 參數名稱 | 說明 | 註解或預設值 |
| --- | --- | --- |
| **CloudAdminCredential** | 雲端管理員的認證，這是存取具特殊權限端點所需的認證。 | _必要_ |
| **AzCredential** | Azure Stack 服務管理帳戶的認證。 使用與部署 Azure Stack 時所用認證相同的認證。 | _必要_ |
| **VMLocalCredential** | MySQL 資源提供者 VM 之本機系統管理員帳戶的認證。 | _必要_ |
| **PrivilegedEndpoint** | 具特殊權限端點的 IP 位址或 DNS 名稱。 |  _必要_ |
| **DependencyFilesLocalPath** | 包含 [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) 的本機共用路徑。 如果您提供這些路徑其中之一，就必須將憑證檔案也放在這個目錄中。 | 選擇性 (如果是多節點，則為必要) |
| **DefaultSSLCertificatePassword** | .pfx 憑證的密碼。 | _必要_ |
| **MaxRetryCount** | 作業失敗時，您想要重試每個作業的次數。| 2 |
| **RetryDuration** | 重試之間的逾時間隔 (秒)。 | 120 |
| **解除安裝** | 移除資源提供者和所有關聯的資源 (請參閱下面的附註)。 | 否 |
| **DebugMode** | 防止在失敗時自動清除。 | 否 |
| **AcceptLicense** | 略過接受 GPL 授權的提示。  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>使用 Azure Stack 入口網站來確認部署是否成功

> [!NOTE]
>  在安裝指令碼執行完成之後，您必須重新整理入口網站，才能看到管理刀鋒視窗。


1. 以服務管理員身分登入管理入口網站。

2. 確認部署成功。 移至 [資源群組]，然後選取 [**system.\<位置\>.mysqladapter**] 資源群組。 確認全部四個部署都成功。

      ![確認 MySQL RP 的部署是否成功](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>透過連線到 MySQL 主控伺服器以提供容量

1. 作為服務管理員登入 Azure Stack 入口網站。

2. 選取 [系統管理資源] > [MySQL 主控伺服器] > [+新增]。

    在 [MySQL 主控伺服器] 刀鋒視窗上，您可以將 MySQL 伺服器資源提供者連線到作為資源提供者後端的實際 MySQL 伺服器執行個體。

    ![主控伺服器](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. 提供 MySQL 伺服器執行個體的連線詳細資料。 請務必提供完整網域名稱 (FQDN) 或有效的 IPv4 位址，而不是簡短的 VM 名稱。 這項安裝不會再提供預設 MySQL 執行個體。 所提供的大小可協助資源提供者管理資料庫容量。 它應該接近資料庫伺服器的實體容量。

    > [!NOTE]
    >如果租用戶和管理 Azure Resource Manager 可以存取 MySQL 執行個體，資源提供者就可以控制此執行個體。 MySQL 執行個體「必須」專門配置給資源提供者。

4. 新增伺服器時，您必須將它們指派給新的或現有的 SKU，以允許服務產品的差異化。
  例如，您可以有一個企業執行個體，提供下列：
    - 資料庫容量
    - 自動備份
    - 為個別部門保留高效能伺服器
 

SKU 名稱應反映屬性，讓租用戶可適當地安置其資料庫。 且 SKU 中的所有主控伺服器都應具有相同的功能。

![建立 MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
> 最多需要一小時才能在入口網站中看到 SKU。 在建立 SKU 前，您無法建立資料庫。


## <a name="test-your-deployment-by-creating-your-first-mysql-database"></a>建立您的第一個 MySQL 資料庫來測試部署


1. 作為服務管理員登入 Azure Stack 入口網站。

2. 選取 [+ 新增] > [資料 + 儲存體]  > [MySQL 資料庫]。

3. 提供資料庫詳細資料。

    ![建立測試 MySQL 資料庫](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. 選取 SKU。

    ![選取 SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. 建立登入設定。 您可以重複使用現有的登入設定，或建立新的登入設定。 此設定包含資料庫的使用者名稱和密碼。

    ![建立新的資料庫登入](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    連接字串包含實際的資料庫伺服器名稱。 從入口網站複製它。

    ![取得 MySQL 資料庫的連接字串](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> MySQL 5.7 中的使用者名稱長度不可超過 32 個字元。 在較舊的版本中則不可超過 16 個字元。


## <a name="add-capacity"></a>新增容量

在 Azure Stack 入口網站中新增額外的 MySQL 伺服器來新增容量。 可以將其他伺服器新增至新的或現有的 SKU。 確定伺服器特性都相同。


## <a name="make-mysql-databases-available-to-tenants"></a>將 MySQL 資料庫提供給租用戶使用
建立方案和產品，讓租用戶使用 MySQL 資料庫。 例如，新增 Microsoft.MySqlAdapter 服務、新增配額等等。

![建立方案和產品來加入資料庫](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>更新系統管理密碼
您可以先在 MySQL 伺服器執行個體上變更它來修改密碼。 選取 [系統管理資源] > [MySQL 主控伺服器]。 然後選取主控伺服器。 在 [設定] 面板中，選取 [密碼]。

![更新管理員密碼](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="update-the-mysql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>更新 MySQL 資源提供者配接器 (僅限多節點，組建 1710 和更新版本)
當 Azure Stack 組建更新時，可能會發行新的 SQL 資源提供者配接器。 建議您在現有配接器繼續運作的情況下，儘快更新至最新的組建。 

若要更新資源提供者，您需使用 *UpdateMySQLProvider.ps1* 指令碼。 此程序與用來安裝資源提供者的程序類似，如本文的[部署資源提供者](#deploy-the-resource-provider)一節所述。 指令碼隨附於所下載的資源提供者中。

*UpdateMySQLProvider.ps1* 指令碼會以最新的資源提供者程式碼建立新 VM，然後將設定從舊 VM 移轉至新 VM。 移轉的設定包括資料庫和主控伺服器資訊，以及必要的 DNS 記錄。

此指令碼會要求使用與針對 DeployMySqlProvider.ps1 指令碼所述的相同引數。 請一併在這裡提供憑證。 

以下是一個您可以從 PowerShell 提示字元中執行的 *UpdateMySQLProvider.ps1* 指令碼範例。 請務必視需要變更帳戶資訊和密碼： 

> [!NOTE]
> 此更新程序僅適用於整合式系統。

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense
 ```

### <a name="updatemysqlproviderps1-parameters"></a>UpdateMySQLProvider.ps1 參數
您可以在命令列中指定這些參數。 如果您未指定參數，或任何參數驗證失敗，系統就會提示您提供所需的參數。

| 參數名稱 | 說明 | 註解或預設值 |
| --- | --- | --- |
| **CloudAdminCredential** | 雲端管理員的認證，這是存取具特殊權限端點所需的認證。 | _必要_ |
| **AzCredential** | Azure Stack 服務管理帳戶的認證。 使用部署 Azure Stack 時所使用的相同認證。 | _必要_ |
| **VMLocalCredential** |SQL 資源提供者 VM 之本機系統管理員帳戶的認證。 | _必要_ |
| **PrivilegedEndpoint** | 具特殊權限端點的 IP 位址或 DNS 名稱。 |  _必要_ |
| **DependencyFilesLocalPath** | 您的憑證 .pfx 檔案必須也放在這個目錄中。 | 選擇性 (如果是多節點，則為必要) |
| **DefaultSSLCertificatePassword** | .pfx 憑證的密碼 | _必要_ |
| **MaxRetryCount** | 作業失敗時，您想要重試每個作業的次數。| 2 |
| **RetryDuration** | 重試之間的逾時間隔 (秒)。 | 120 |
| **解除安裝** | 移除資源提供者和所有關聯的資源 (請參閱下面的附註)。 | 否 |
| **DebugMode** | 防止在失敗時自動清除。 | 否 |
| **AcceptLicense** | 略過接受 GPL 授權的提示。  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


## <a name="collect-diagnostic-logs"></a>收集診斷記錄
MySQL 資源提供者是一部鎖定的虛擬機器。 如果從虛擬機器收集記錄變成必要，就會提供 PowerShell Just Enough Administration (JEA) 端點 _DBAdapterDiagnostics_ 來因應該目的。 透過這個端點，有兩個命令可用：

* Get-AzsDBAdapterLog - 準備一個包含 RP 診斷記錄的 zip 套件，然後將它放在工作階段使用者磁碟機上。 呼叫此命令時，可以不搭配任何參數，並且會收集過去四小時的記錄。
* Remove-AzsDBAdapterLog - 清除資源提供者 VM 上現有的記錄套件

在部署或更新 RP 以連線至診斷端點來擷取 RP 記錄的期間，會建立一個名為 _dbadapterdiag_ 的使用者帳戶。 此帳戶的密碼與在部署/更新期間為本機系統管理員帳戶提供的密碼相同。

若要使用這些命令，您必須建立一個連至資源提供者虛擬機器的遠端 PowerShell 工作階段，然後叫用命令。 您可以視需要提供 FromDate 和 ToDate 參數。 如果您未指定其中一個參數或兩者都未指定，FromDate 將會是目前時間之前的四小時，而 ToDate 則會是目前的時間。

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
MySQL 資源提供者是一部鎖定的虛擬機器。 若要更新資源提供者虛擬機器的安全性，可以透過 PowerShell Just Enough Administration (JEA) 端點 _DBAdapterMaintenance_ 來完成。

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

2. 建立連至 MySQL RP 配接器虛擬機器維護端點的 PowerShell 工作階段
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


## <a name="remove-the-mysql-resource-provider-adapter"></a>移除 MySQL 資源提供者配接器

若要移除資源提供者，必須先移除任何相依性。

1. 確定您具有為此資源提供者版本下載的原始部署套件。

2. 必須從資源提供者刪除所有租用戶資料庫。 (刪除租用戶資料庫並不會刪除資料)。此工作應該由租用戶自己執行。

3. 必須從命名空間中取消註冊租用戶。

4. 系統管理員必須從 MySQL 配接器中刪除主控伺服器。

5. 系統管理員必須刪除參考 MySQL 配接器的所有方案。

6. 系統管理員必須刪除與 MySQL 配接器關聯的所有配額。

7. 使用下列元素來重新執行部署指令碼：
    - -Uninstall 參數
    - Azure Resource Manager 端點
    - DirectoryTenantID
    - 服務管理員帳戶的認證
