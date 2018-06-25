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
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 3a7656e54181c8e8e7b6b1bd39f80ce8ed01c807
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294855"
---
# <a name="update-the-sql-resource-provider"></a>更新 SQL 資源提供者
當 Azure Stack 組建更新時，可能會發行新的 SQL 資源提供者。 建議您在現有配接器繼續運作的情況下，儘快更新至最新的組建。 更新必須依序安裝：您無法略過版本 (請參閱[部署資源提供者的必要條件](.\azure-stack-sql-resource-provider-deploy.md#prerequisites)中的版本清單)。

若要更新資源提供者，您需使用 *UpdateSQLProvider.ps1* 指令碼。 此流程與用來安裝資源提供者的流程類似，如[部署資源提供者](.\azure-stack-sql-resource-provider-deploy.md)文章中所述。 指令碼隨附於所下載的資源提供者中。

*UpdateSQLProvider.ps1* 指令碼會以最新的資源提供者程式碼建立新 VM，然後將設定從舊 VM 移轉至新 VM。 移轉的設定包括資料庫和主控伺服器資訊，以及必要的 DNS 記錄。

此指令碼會要求使用與針對 DeploySqlProvider.ps1 指令碼所述的相同引數。 請一併在這裡提供憑證。 

建議您從 Marketplace Management 下載最新的 Windows Server 2016 Core 映像。 如果您需要安裝某個更新，您可以在本機相依性路徑中放置單一的 .MSU 套件。 如果找到多個 .MSU 檔案，指令碼將會失敗。

以下是一個您可以從 PowerShell 提示字元中執行的 *UpdateSQLProvider.ps1* 指令碼範例。 請務必視需要變更帳戶資訊和密碼： 

> [!NOTE]
> 此更新程序僅適用於整合式系統。

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

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

## <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 參數
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


## <a name="next-steps"></a>後續步驟

[維護 SQL 資源提供者](azure-stack-sql-resource-provider-maintain.md)
