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
ms.openlocfilehash: 20b289c16a73bd20ed020987116975c8abe893f0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198570"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>在 Microsoft Azure Stack 上使用 SQL 資料庫
使用 Azure Stack SQL Server 資源提供者來公開 Azure Stack 的 SQL 資料庫即服務。 SQL 資源提供者服務在 SQL 資源提供者 VM (Windows Server Core 虛擬機器) 上執行。

## <a name="prerequisites"></a>先決條件
您必須先滿足數個先決條件，才能部署 Azure Stack SQL 資源提供者。 在存取具有特殊權限端點 VM 的電腦上執行以下步驟：

- 如果您尚未這麼做，請向 Azure [註冊 Azure Stack](.\azure-stack-registration.md)，以便下載 Azure Marketplace 項目。
- 透過下載 **Windows Server 2016 Server Core** 映像，將必要的 Windows Server Core VM 新增到 Azure Stack Marketplace。 如果您需要安裝某個更新，您可以在本機相依性路徑中放置單一的 .MSU 套件。 如果找到的 .MSU 檔案超過一個，則 SQL 資源提供者的安裝會失敗。
- 下載 SQL 資源提供者二進位檔，然後執行自我解壓縮程式，以將內容解壓縮到至目錄。 資源提供者會有最低限度的相對應 Azure Stack 組建。 請務必為您正在執行的 Azure Stack 下載正確版本的二進位檔：
    - Azure Stack 1802 版 (1.0.180302.1)：[SQL RP 1.1.18.0 版](https://aka.ms/azurestacksqlrp1802)。
    - Azure Stack 1712 版 (1.0.180102.3、1.0.180103.2 或 1.0.180106.1 (整合式系統))：[SQL RP 1.1.14.0 版](https://aka.ms/azurestacksqlrp1712)。
- 僅適用於整合式系統安裝，您必須透過將 .pfx 檔案放置在 **DependencyFilesLocalPath** 參數指定的位置，來提供 SQL PaaS PKI 憑證，如 [Azure Stack 部署 PKI 需求](.\azure-stack-pki-certs.md#optional-paas-certificates)中的選擇性＜PaaS 憑證＞一節所述。
- 請確定您已安裝[最新版的 Azure Stack PowerShell](.\azure-stack-powershell-install.md) (v1.2.11)。 

## <a name="deploy-the-sql-resource-provider"></a>部署 SQL 資源提供者
當您符合所有先決條件，可以安裝 SQL 資源提供者之後，即可以執行 **DeploySqlProvider.ps1** 指令碼來部署 SQL 資源提供者。 DeploySqlProvider.ps1 指令碼是從 SQL 資源提供者二進位檔 (根據您的 Azure Stack 版本所下載) 的一部分擷取而來。 

> [!IMPORTANT]
> 執行指令碼的系統必須是已安裝最新版 .NET 執行階段的 Windows 10 或 Windows Server 2016 系統。


若要部署 SQL 資源提供者，請開啟新的提升權限 (系統管理員) 的 PowerShell 主控台，然後變更至您解壓縮 SQL 資源提供者二進位檔的目錄。

> [!NOTE]
> 請使用新的 PowerShell 主控台視窗，以避免系統上已載入不正確 PowerShell 模組可能引發的問題。

執行 DeploySqlProvider.ps1 指令碼，這會執行下列步驟：
- 將憑證和其他成品上傳到 Azure Stack 上的儲存體帳戶。
- 發佈資源庫套件，以便讓您透過資源庫部署 SQL 資料庫。
- 發佈用於部署主控伺服器的資源庫套件。
- 使用在步驟 1 中建立的 Windows Server 2016 映像來部署 VM，然後安裝資源提供者。
- 註冊與您資源提供者 VM 對應的本機 DNS 記錄。
- 向本機 Azure Resource Manager 註冊您的資源提供者 (使用者和管理員)。
- 在 RP 安裝期間，選擇性地安裝單一 Windows 更新。

SQL 資源提供者部署會開始，並建立 system.local.sqladapter 資源群組。 最多可能需要 75 分鐘才能完成部署至此資源群組的四個必要部署。

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 參數
您可以在命令列中指定這些參數。 如果您未指定參數，或任何參數驗證失敗，系統就會提示您提供所需的參數。

| 參數名稱 | 說明 | 註解或預設值 |
| --- | --- | --- |
| **CloudAdminCredential** | 雲端管理員的認證，這是存取具特殊權限端點所需的認證。 | _必要_ |
| **AzCredential** | Azure Stack 服務管理帳戶的認證。 使用部署 Azure Stack 時所使用的相同認證。 | _必要_ |
| **VMLocalCredential** | SQL 資源提供者 VM 之本機系統管理員帳戶的認證。 | _必要_ |
| **PrivilegedEndpoint** | 具特殊權限端點的 IP 位址或 DNS 名稱。 |  _必要_ |
| **DependencyFilesLocalPath** | 您的憑證 .pfx 檔案必須也放在這個目錄中。 | _選擇性_ (對於整合式系統為_必要_) |
| **DefaultSSLCertificatePassword** | .pfx 憑證的密碼。 | _必要_ |
| **MaxRetryCount** | 作業失敗時，您想要重試每個作業的次數。| 2 |
| **RetryDuration** | 重試之間的逾時間隔 (秒)。 | 120 |
| **解除安裝** | 移除資源提供者和所有關聯的資源 (請參閱下面的附註)。 | 否 |
| **DebugMode** | 防止在失敗時自動清除。 | 否 |

>[!NOTE]
> 最多需要一小時才能在入口網站中看到 SKU。 在建立 SKU 前，您無法建立資料庫。


## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>使用自訂指令碼部署 SQL 資源提供者
若要在 DeploySqlProvider.ps1 指令碼執行期間避免手動輸入必要資訊，您可以視需要變更預設的帳戶資訊與密碼，以自訂以下指令碼範例：

```powershell
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

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>使用 Azure Stack 入口網站確認部署是否成功
本節中的步驟可用來確保已經成功部署 SQL 資源提供者。

> [!NOTE]
>  安裝指令碼完成執行之後，您必須重新整理入口網站，才會看到系統管理刀鋒視窗和資源庫項目。

1. 以服務管理員身分登入管理入口網站。

2. 確認部署成功。 移至 [資源群組]。 然後選取 [**system.\<位置\>.sqladapter**] 資源群組。 確認全部四個部署都成功。

      ![確認 SQL 資源提供者的部署是否成功](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="next-steps"></a>後續步驟

[新增主控伺服器](azure-stack-sql-resource-provider-hosting-servers.md)
