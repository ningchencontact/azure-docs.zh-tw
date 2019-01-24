---
title: 更新 Azure Stack MySQL 資源提供者 | Microsoft Docs
description: 了解如何更新 Azure Stack MySQL 資源提供者。
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
ms.date: 01/11/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.openlocfilehash: 9f53dbd3546fcd3f761338664179b42fce5be200
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246019"
---
# <a name="update-the-mysql-resource-provider"></a>更新 MySQL 資源提供者 

*適用於：Azure Stack 整合式系統。*

當 Azure Stack 組建更新時，可能會發行新的 MySQL 資源提供者配接器。 建議您在現有配接器繼續運作的情況下，儘快更新至最新的組建。 

從 MySQL 資源提供者 1.1.33.0 版開始，更新會不斷累加，而不需要依其發行順序來安裝；只要您是從 1.1.24.0 版或更新版本開始就行。 例如，如果您執行 1.1.24.0 版的 MySQL 資源提供者，則不需要先安裝 1.1.30.0 版就能升級至 1.1.33.0 版或更新版本。 若要檢閱可用的資源提供者版本，以及其支援的 Azure Stack 版本，請參閱[部署資源提供者必要條件](./azure-stack-mysql-resource-provider-deploy.md#prerequisites)中的版本清單。

若要更新資源提供者，您需使用 **UpdateMySQLProvider.ps1** 指令碼。 此程序與用來安裝資源提供者的程序類似，如本文的[部署資源提供者](#deploy-the-resource-provider)一節所述。 指令碼隨附於所下載的資源提供者中。 

 > [!IMPORTANT]
 > 在升級資源提供者之前，請先檢閱版本資訊，以了解有哪些新功能、修正，以及任何可能對部署造成影響的已知問題。

## <a name="update-script-processes"></a>更新指令碼程序

**UpdateMySQLProvider.ps1** 指令碼會以最新的資源提供者程式碼建立新 VM，然後將設定從舊 VM 移轉至新 VM。 移轉的設定包括資料庫和主控伺服器資訊，以及必要的 DNS 記錄。 

>[!NOTE]
>建議您從 Marketplace Management 下載最新的 Windows Server 2016 Core 映像。 如果您需要安裝某個更新，可以將**單一** MSU 套件放在本機相依性路徑中。 如果這個位置中有多個 MSU 檔案，指令碼就會失敗。

此指令碼會要求使用與針對 DeployMySqlProvider.ps1 指令碼所述的相同引數。 請一併在這裡提供憑證。  


## <a name="update-script-parameters"></a>更新指令碼參數 
您可以在執行 **UpdateMySQLProvider.ps1** PowerShell 指令碼時從命令列指定下列參數。 如果未指定參數，或任何參數驗證失敗，系統就會提示您提供所需的參數。 

| 參數名稱 | 說明 | 註解或預設值 | 
| --- | --- | --- | 
| **CloudAdminCredential** | 雲端管理員的認證，這是存取具特殊權限端點所需的認證。 | _必要_ | 
| **AzCredential** | Azure Stack 服務管理帳戶的認證。 使用部署 Azure Stack 時所使用的相同認證。 | _必要_ | 
| **VMLocalCredential** |SQL 資源提供者 VM 之本機系統管理員帳戶的認證。 | _必要_ | 
| **PrivilegedEndpoint** | 具特殊權限端點的 IP 位址或 DNS 名稱。 |  _必要_ | 
| **AzureEnvironment** | 您用來部署 Azure Stack 的服務管理員帳戶所屬的 Azure 環境。 只有部署 Azure AD 時才需要。 支援的環境名稱為 **AzureCloud**、**AzureUSGovernment**，或如果使用中國 Azure AD，則為 **AzureChinaCloud**。 | AzureCloud |
| **DependencyFilesLocalPath** | 您的憑證 .pfx 檔案必須也放在這個目錄中。 | 選擇性 (如果是多節點，則為必要) | 
| **DefaultSSLCertificatePassword** | .pfx 憑證的密碼。 | _必要_ | 
| **MaxRetryCount** | 作業失敗時，您想要重試每個作業的次數。| 2 | 
| **RetryDuration** | 重試之間的逾時間隔 (秒)。 | 120 | 
| **解除安裝** | 移除資源提供者和所有關聯的資源 (請參閱下面的附註)。 | 否 | 
| **DebugMode** | 防止在失敗時自動清除。 | 否 | 
| **AcceptLicense** | 略過接受 GPL 授權的提示。  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>更新指令碼範例
以下舉例說明如何使用可從已提升權限的 PowerShell 主控台來執行的 *UpdateMySQLProvider.ps1* 指令碼。 請務必視需要變更變數資訊和密碼：  

> [!NOTE] 
> 此更新程序僅適用於整合式系統。 

```powershell 
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.5.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS virtual machines 
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

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
$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds ` 
-VMLocalCredential $vmLocalAdminCreds ` 
-CloudAdminCredential $cloudAdminCreds ` 
-PrivilegedEndpoint $privilegedEndpoint ` 
-AzureEnvironment $AzureEnvironment `
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
```  

## <a name="next-steps"></a>後續步驟
[維護 MySQL 資源提供者](azure-stack-mysql-resource-provider-maintain.md)
