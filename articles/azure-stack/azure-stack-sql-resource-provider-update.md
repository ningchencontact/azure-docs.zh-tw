---
title: 更新 Azure Stack SQL 資源提供者 | Microsoft Docs
description: 了解如何更新 Azure Stack SQL 資源提供者。
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
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 051c34c631795479cb5e5d0f67209bae89e82940
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766736"
---
# <a name="update-the-sql-resource-provider"></a>更新 SQL 資源提供者

*適用於：Azure Stack 整合式系統。*

當 Azure Stack 更新為新的組建時，可能會發行新的 SQL 資源提供者。 雖然現有資源提供者仍可正常運作，但建議您盡快更新至最新組建。 

從 SQL 資源提供者 1.1.33.0 版開始，更新會不斷累加，而不需要依其發行順序來安裝；只要您是從 1.1.24.0 版或更新版本開始就行。 例如，如果您執行 1.1.24.0 版的 SQL 資源提供者，則不需要先安裝 1.1.30.0 版就能升級至 1.1.33.0 版或更新版本。 若要檢閱可用的資源提供者版本，以及其支援的 Azure Stack 版本，請參閱[部署資源提供者必要條件](./azure-stack-sql-resource-provider-deploy.md#prerequisites)中的版本清單。

若要更新資源提供者，請使用 *UpdateSQLProvider.ps1* 指令碼。 此指令碼隨附於所下載的新 SQL 資源提供者中。 此更新程序類似於用來[部署資源提供者](./azure-stack-sql-resource-provider-deploy.md)的程序。 更新指令碼會使用和 DeploySqlProvider.ps1 指令碼相同的引數，而且您必須提供憑證資訊。

 > [!IMPORTANT]
 > 在升級資源提供者之前，請先檢閱版本資訊，以了解有哪些新功能、修正，以及任何可能對部署造成影響的已知問題。

## <a name="update-script-processes"></a>更新指令碼程序

UpdateSQLProvider.ps1 指令碼會以最新的資源提供者程式碼建立新的虛擬機器 (VM)。

> [!NOTE]
> 建議您從 Marketplace Management 下載最新的 Windows Server 2016 Core 映像。 如果您需要安裝某個更新，可以將**單一** MSU 套件放在本機相依性路徑中。 如果這個位置中有多個 MSU 檔案，指令碼就會失敗。

UpdateSQLProvider.ps1 指令碼在建立新的 VM 後，會從舊提供者 VM 遷移下列設定：

* 資料庫資訊
* 裝載伺服器資訊
* 必要的 DNS 記錄

## <a name="update-script-parameters"></a>更新指令碼參數

您可以在執行 **UpdateSQLProvider.ps1** PowerShell 指令碼時從命令列指定下列參數。 如果未指定參數，或任何參數驗證失敗，系統就會提示您提供所需的參數。

| 參數名稱 | 說明 | 註解或預設值 |
| --- | --- | --- |
| **CloudAdminCredential** | 雲端管理員的認證，這是存取具特殊權限端點所需的認證。 | _必要_ |
| **AzCredential** | Azure Stack 服務系統管理員帳戶的認證。 使用與部署 Azure Stack 時所用認證相同的認證。 | _必要_ |
| **VMLocalCredential** | SQL 資源提供者 VM 之本機系統管理員帳戶的認證。 | _必要_ |
| **PrivilegedEndpoint** | 具特殊權限端點的 IP 位址或 DNS 名稱。 |  _必要_ |
| **AzureEnvironment** | 您用來部署 Azure Stack 的服務管理員帳戶所屬的 Azure 環境。 只有部署 Azure AD 時才需要。 支援的環境名稱為 **AzureCloud**、**AzureUSGovernment**，或如果使用中國 Azure AD，則為 **AzureChinaCloud**。 | AzureCloud |
| **DependencyFilesLocalPath** | 您也必須將憑證 .pfx 檔案放在這個目錄中。 | 若為單一節點，屬選擇性，若為多重節點，則屬必要 |
| **DefaultSSLCertificatePassword** | .pfx 憑證的密碼。 | _必要_ |
| **MaxRetryCount** | 當作業失敗時，您想要重試每個作業的次數。| 2 |
| **RetryDuration** |重試之間的逾時間隔 (秒)。 | 120 |
| **解除安裝** | 移除資源提供者及所有關聯的資源。 | 否 |
| **DebugMode** | 防止在失敗時自動清除。 | 否 |

## <a name="update-script-powershell-example"></a>更新指令碼 PowerShell 範例
以下舉例說明如何使用可從已提升權限的 PowerShell 主控台來執行的 *UpdateSQLProvider.ps1* 指令碼。 請務必視需要變更變數資訊和密碼：  

> [!NOTE]
> 此更新程序僅適用於 Azure Stack 整合式系統。

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.5.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service administrator account (this can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential required for privileged endpoint access.
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
  -AzureEnvironment $AzureEnvironment `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `

 ```

## <a name="next-steps"></a>後續步驟

[維護 SQL 資源提供者](azure-stack-sql-resource-provider-maintain.md)
