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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 061d9431890622dafafa8c180027b8df735c100e
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296010"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>在 Microsoft Azure Stack 上使用 MySQL 資料庫
使用 Azure Stack MySQL Server 資源提供者來公開 Azure Stack 的 MySQL 資料庫即服務。 MySQL 資源提供者服務在 MySQL 資源提供者 VM (Windows Server Core 虛擬機器) 上執行。

## <a name="deploy-the-resource-provider"></a>部署資源提供者 
1. 如果您尚未這樣做，請註冊您的開發套件，並下載可透過 Marketplace 管理下載的「Windows Server 2016 Datacenter Core」映像。 您必須使用 Windows Server 2016 Core 映像。 您也可以使用指令碼建立 [Windows Server 2016 映像](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image) (\英文\)。 (請務必選取核心選項)。 

2. 登入可存取具特殊權限端點 VM 的主機：
    - 在 Azure SDK 安裝上，登入實體主機。  
    - 在整合系統上，主機必須是可存取具特殊權限端點的系統。 
    
    >[!NOTE] 
    > 執行指令碼的系統「必須」是已安裝最新版 .NET 執行階段的 Windows 10 或 Windows Server 2016 系統。 否則，安裝會失敗。 Azure Stack ASDK 主機便符合此準則。 
    
3. 下載 MySQL 資源提供者二進位檔。 然後執行自我解壓縮程式，以將內容解壓縮至暫存目錄。 
    >[!NOTE]  
    > 資源提供者會有最低限度的相對應 Azure Stack 組建。 請務必下載適用於目前所執行 Azure Stack 版本的正確二進位檔。

    | Azure Stack 版本 | MySQL RP 版本| 
    | --- | --- | 
    | 1804 版 (1.0.180513.1)|[MySQL RP 1.1.24.0 版](https://aka.ms/azurestackmysqlrp1804) | 
    | 1802 版 (1.0.180302.1) | [MySQL RP 版本 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) | 
    | 1712 版 (1.0.180102.3 或 1.0.180106.1 (整合系統)) | [MySQL RP 1.1.14.0 版](https://aka.ms/azurestackmysqlrp1712) | 

4.  對於 ASDK，系統會在此程序的執行過程中建立自我簽署憑證。 針對整合式系統，您必須提供適當的憑證。 如果您需要提供自己的憑證，請將符合下列準則的 .pfx 檔案放在 **DependencyFilesLocalPath** 中： 
    - \*.dbadapter.\<區域\>.\<外部 FQDN\> 的萬用字元憑證，或具有 mysqladapter.dbadapter.\<區域\>.\<\> 一般名稱的單一網站憑證。 
    - 此憑證必須受到信任。 也就是說，信任鏈結必須存在，而不需要中繼憑證。 
    - DependencyFilesLocalPath 中僅存在單一憑證檔案。 
    - 檔案名稱不得包含任何特殊字元或空格。 

5. 開啟一個已提高權限 (系統管理) 的**新** PowerShell 主控台。 然後變更至檔案的解壓縮目錄。 使用新視窗，以避免因系統上已載入不正確的 PowerShell 模組而可能發生的問題。 

6. 執行 **DeployMySqlProvider.ps1** 指令碼。 指令碼會執行這些步驟： 
    - 下載 MySQL 連接器二進位檔 (可離線提供)。 
    - 將憑證和其他成品上傳到 Azure Stack 上的儲存體帳戶。 
    - 發佈資源庫套件，以便讓您透過資源庫部署 SQL 資料庫。 
    - 發佈用於部署主控伺服器的資源庫套件。 
    - 使用 Windows Server 2016 Azure Stack 市集映像部署 VM，並安裝資源提供者。 
    - 註冊與您資源提供者 VM 對應的本機 DNS 記錄。 
    - 向本機 Azure Resource Manager 註冊您的資源提供者 (租用戶和管理員)。 

    您可以在命令列上指定所需參數，或執行不含任何參數的指令碼，於系統提示時再輸入參數。 

    以下是您可以從 PowerShell 命令提示字元執行的範例。 請務必視需要變更帳戶資訊和密碼： 

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
    # Find the ERCS01 IP address first, and make sure the certificate 
    # file is in the specified directory. 
    $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds ` 
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

> [!NOTE] 
> 最多需要一小時才能在入口網站中看到 SKU。 在建立 SKU 前，您無法建立資料庫。 

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>使用 Azure Stack 入口網站來確認部署是否成功 
> [!NOTE] 
>  在安裝指令碼執行完成之後，您必須重新整理入口網站，才能看到管理刀鋒視窗。 

1. 以服務管理員身分登入管理入口網站。 
2. 確認部署成功。 移至 [資源群組]，然後選取 [**system.\<位置\>.mysqladapter**] 資源群組。 確認全部四個部署都成功：

      ![確認 MySQL RP 的部署是否成功](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png) 


## <a name="next-steps"></a>後續步驟
[新增主控伺服器](azure-stack-mysql-resource-provider-hosting-servers.md)
