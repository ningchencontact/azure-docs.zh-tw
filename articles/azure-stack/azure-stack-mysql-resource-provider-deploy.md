---
title: 在 Azure Stack 上使用 MySQL 資料庫 | Microsoft Docs
description: 了解如何在 Azure Stack 上部署 MySQL 資料庫即服務，並了解部署 MySQL Server 資源提供者配接器的快速步驟。
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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e4c3eb1d7dfd4894576d5fbed52cf4de5fed9e44
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938107"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>在 Azure Stack 上部署 MySQL 資源提供者

使用 MySQL Server 資源提供者將 MySQL 資料庫公開成 Azure Stack 服務。 MySQL 資源提供者在 Windows Server 2016 Server Core 虛擬機器 (VM) 上會以服務的形式執行。

## <a name="prerequisites"></a>先決條件

您必須先滿足數個先決條件，才能部署 Azure Stack MySQL 資源提供者。 為了滿足這些需求，請在能夠存取具特殊權限端點 VM 的電腦上完成本文中的步驟。

* 如果您尚未這麼做，請向 Azure [註冊 Azure Stack](.\azure-stack-registration.md)，以便下載 Azure Marketplace 項目。
* 透過下載 **Windows Server 2016 Datacenter - Server Core** 映像，將必要的 Windows Server Core VM 新增到 Azure Stack Marketplace。 您也可以使用指令碼建立 [Windows Server 2016 映像](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image) (\英文\)。 執行指令碼時，請務必選取核心選項。

  >[!NOTE]
  >如果您需要安裝某個更新，您可以在本機相依性路徑中放置單一的 .MSU 套件。 如果找到多個 .MSU 檔案，MySQL 資源提供者安裝將會失敗。

* 下載 MySQL 資源提供者二進位檔，然後執行自我解壓縮程式，以將內容解壓縮到至暫存目錄。

  >[!NOTE]
  >若要在無法存取網際網路的系統上部署 MySQL 提供者，請將 [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) 檔案複製到本機共用。 在提示您輸入共用名稱時，提供該共用名稱。 您必須安裝 Azure 和 Azure Stack PowerShell 模組。

* 資源提供者會有最低限度的相對應 Azure Stack 組建。 請務必為您正在執行的 Azure Stack 版本下載正確的二進位檔。

    | Azure Stack 版本 | MySQL RP 版本|
    | --- | --- |
    | 1804 版 (1.0.180513.1)|[MySQL RP 1.1.24.0 版](https://aka.ms/azurestackmysqlrp1804) |
    | 1802 版 (1.0.180302.1) | [MySQL RP 版本 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) |
    | 1712 版 (1.0.180102.3 或 1.0.180106.1 (整合系統)) | [MySQL RP 1.1.14.0 版](https://aka.ms/azurestackmysqlrp1712) |

### <a name="certificates"></a>憑證

針對 ASDK，系統會在此安裝過程中建立自我簽署憑證。 針對 Azure Stack 整合式系統，您必須提供適當的憑證。 如果您需要提供自己的憑證，請將符合下列準則的 .pfx 檔案放在 **DependencyFilesLocalPath** 中：

* \*.dbadapter.\<區域\>.\<外部 FQDN\> 的萬用字元憑證，或具有 mysqladapter.dbadapter.\<區域\>.\<\> 一般名稱的單一網站憑證。
* 此憑證必須受到信任。 信任鏈結必須存在，而不需要中繼憑證。
* DependencyFilesLocalPath 中僅存在單一憑證檔案。
* 檔案名稱不可包含任何特殊字元或空格。

## <a name="deploy-the-resource-provider"></a>部署資源提供者

安裝妥所有先決條件項目之後，請執行 **DeployMySqlProvider.ps1** 指令碼來部署 MYSQL 資源提供者。 DeployMySqlProvider.ps1 指令碼是從您針對 Azure Stack 版本下載的 MySQL 資源提供者二進位檔中解壓縮而來。

> [!IMPORTANT]
> 您執行指令碼的系統必須是已安裝最新版 .NET 執行階段的 Windows 10 或 Windows Server 2016 系統。

若要部署 MySQL 資源提供者，請開啟新的已提升權限 PowerShell 主控台視窗，然後變更至您解壓縮 MySQL 資源提供者二進位檔的目錄。 建議您使用新的 PowerShell 視窗，以避免已載入的 PowerShell 模組可能造成的問題。

請執行 **DeployMySqlProvider.ps1** 指令碼，這會完成下列工作：

* 將憑證和其他成品上傳到 Azure Stack 上的儲存體帳戶。
* 發佈資源庫套件，以便使用資源庫來部署 MySQL 資料庫。
* 發佈用於部署主控伺服器的資源庫套件。
* 使用您已下載的 Windows Server 2016 核心映像來部署 VM，然後安裝 MySQL 資源提供者。
* 註冊與您資源提供者 VM 對應的本機 DNS 記錄。
* 針對操作員和使用者帳戶，向本機 Azure Resource Manager 註冊您的資源提供者。
* 視需要在安裝資源提供者的期間，安裝單一 Windows Server 更新。

> [!NOTE]
> 當 MySQL 資源提供者部署開始時，會建立 **system.local.mysqladapter** 資源群組。 最多可能需要 75 分鐘的時間，才能完成對此資源群組的四個必要部署。

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 參數

您可以從命令列指定這些參數。 如果未指定參數，或任何參數驗證失敗，系統就會提示您提供所需的參數。

| 參數名稱 | 說明 | 註解或預設值 |
| --- | --- | --- |
| **CloudAdminCredential** | 雲端管理員的認證，這是存取具特殊權限端點所需的認證。 | _必要_ |
| **AzCredential** | Azure Stack 服務管理帳戶的認證。 使用與部署 Azure Stack 時所用認證相同的認證。 | _必要_ |
| **VMLocalCredential** | MySQL 資源提供者 VM 之本機系統管理員帳戶的認證。 | _必要_ |
| **PrivilegedEndpoint** | 具特殊權限端點的 IP 位址或 DNS 名稱。 |  _必要_ |
| **DependencyFilesLocalPath** | 包含 [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) 的本機共用路徑。 如果您提供這些路徑其中之一，就必須將憑證檔案也放在這個目錄中。 | 如果單一節點，便是「選擇性」，如果是多節點，則為「必要」。 |
| **DefaultSSLCertificatePassword** | .pfx 憑證的密碼。 | _必要_ |
| **MaxRetryCount** | 作業失敗時，您想要重試每個作業的次數。| 2 |
| **RetryDuration** | 重試之間的逾時間隔 (秒)。 | 120 |
| **解除安裝** | 移除資源提供者和所有關聯的資源 (請參閱下面的附註)。 | 否 |
| **DebugMode** | 防止在失敗時自動清除。 | 否 |
| **AcceptLicense** | 略過接受 GPL 授權的提示。  <http://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

> [!NOTE]
> 最多需要一小時才能在入口網站中看到 SKU。 您必須等到 SKU 部署完畢並開始執行之後，才能建立資料庫。

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>使用自訂指令碼來部署 MySQL 資源提供者

若要在部署資源提供者時免除任何手動設定，您可以自訂下列指令碼。 請視需要針對您的 Azure Stack 部署，變更預設帳戶資訊和密碼。

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

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
# Find the ERCS01 IP address first, and make sure the certificate file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

當資源提供者安裝指令碼完成時，請重新整理您的瀏覽器，以確定可以看到最新的更新。

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>使用 Azure Stack 入口網站來確認部署是否成功

1. 以服務管理員身分登入管理入口網站。
2. 選取 [資源群組]
3. 選取 [**system.\<位置\>.mysqladapter**] 資源群組。
4. 在資源群組概觀的摘要頁面上，[部署] 底下的訊息應該會是「3 個成功」。
5. 您可以在 [設定]底下取得有關資源提供者部署的更多詳細資訊。 選取 [部署] 以取得資訊，例如：每項部署的 [狀態]、[時間戳記] 及 [持續時間]。

## <a name="next-steps"></a>後續步驟

[新增主控伺服器](azure-stack-mysql-resource-provider-hosting-servers.md)
