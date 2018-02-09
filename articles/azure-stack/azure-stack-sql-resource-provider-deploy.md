---
title: "在 Azure Stack 上使用 SQL 資料庫 | Microsoft Docs"
description: "了解如何在 Azure Stack 上部署 SQL 資料庫即服務，並了解部署 SQL Server 資源提供者配接器的快速步驟。"
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: JeffGo
ms.openlocfilehash: bf52ed4986b4e0930b57721c0e38bbf748045a36
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2018
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
- **資源提供者本身**：負責處理佈建要求並公開資料庫資源。
- **裝載 SQL Server 的伺服器**：為稱為主控伺服器的資料庫提供容量。

您必須建立一個 (或多個) SQL Server 執行處理和/或提供對外部 SQL Server 執行個體的存取權。

## <a name="deploy-the-resource-provider"></a>部署資源提供者

1. 如果您尚未這樣做，請註冊您的開發套件並透過 Marketplace 管理下載下載 Windows Server 2016 Datacenter Core 映像。 您必須使用 Windows Server 2016 Core 映像。 您也可以使用指令碼建立 [Windows Server 2016 映像](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image) (\英文\)。 (請務必選取核心選項)。 不再需要 .NET 3.5 執行階段。

2. 登入可存取具特殊權限端點 VM 的主機。

    - 在 Azure Stack 開發套件安裝上，登入實體主機。

    - 在多節點系統上，主機必須是可存取具特殊權限端點的系統。
    
    >[!NOTE]
    > 執行指令碼的系統必須為已安裝最新版 .NET 執行階段的 Windows 10 或 Windows Server 2016 系統。 否則，安裝會失敗。 Azure Stack SDK 主機便符合此準則。


3. 下載 SQL 資源提供者二進位檔。 然後執行自我解壓縮程式，以將內容解壓縮至暫存目錄。

    >[!NOTE] 
    > 資源提供者組建會對應至 Azure Stack 組建。 請務必下載適用於目前所執行 Azure Stack 版本的正確二進位檔。

    | Azure Stack 組建 | SQL 資源提供者安裝程式 |
    | --- | --- |
    |1.0.180102.3、1.0.180103.2 或 1.0.180106.1 (多節點) | [SQL RP 版本 1.1.14.0](https://aka.ms/azurestacksqlrp1712) |
    | 1.0.171122.1 | [SQL RP 版本 1.1.12.0](https://aka.ms/azurestacksqlrp1711) |
    | 1.0.171028.1 | [SQL RP 版本 1.1.8.0](https://aka.ms/azurestacksqlrp1710) |
  

4. Azure Stack 根憑證擷取自具特殊權限的端點。 針對 Azure Stack SDK，會在此程序的執行過程中建立自我簽署憑證。 對於多節點，您必須提供適當的憑證。

   若要提供自己的憑證，請依照下列方式，將 .pfx 檔案放在 **DependencyFilesLocalPath** 中：

    - \*.dbadapter.\<區域\>.\<外部 FQDN\> 的萬用字元憑證，或具有 sqladapter.dbadapter.\<區域\>.\<外部 FQDN\> 一般名稱的單一網站憑證。

    - 此憑證必須受到信任。 也就是說，信任鏈結必須存在，而不需要中繼憑證。

    - DependencyFilesLocalPath 中僅存在單一憑證檔案。

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

> [!NOTE]
> 如果安裝花費的時間超過 90 分鐘，可能表示失敗。 如果發生失敗，您就會在畫面上和記錄檔中看到失敗訊息，但會從發生失敗的步驟重試部署。 系統如果不符合建議的記憶體和 vCPU 規格，可能無法部署 SQL 資源提供者。
>

以下是您可以從 PowerShell 命令提示字元執行的範例。 (請務必視需要變更帳戶資訊和密碼)。

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
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
每當 Azure Stack 組建更新時，都會發行新的 SQL 資源提供者配接器。 現有的配接器可以繼續運作。 不過，建議您在 Azure Stack 更新後，儘快更新至最新的組建。 

更新程序與先前所述的安裝程序類似。 您會使用最新的資源提供者程式碼來建立一個新 VM。 此外，您會將設定移轉至這個新執行個體，包括資料庫和主控伺服器資訊。 此外，您也會移轉必要的 DNS 記錄。

請使用 UpdateSQLProvider.ps1 指令碼搭配先前所述的相同引數。 您也必須提供憑證。

> [!NOTE]
> 只有多節點系統上才支援此更新程序。

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

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
