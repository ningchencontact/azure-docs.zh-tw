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
ms.openlocfilehash: ac5073d1abc32b7598a869750f9c5a801559e9e6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264072"
---
# <a name="update-the-sql-resource-provider"></a>更新 SQL 資源提供者

適用於：Azure Stack 整合系統。

當 Azure Stack 更新為新的組建時，可能會發行新的 SQL 資源提供者。 雖然現有配接器仍可正常運作，但建議您盡快更新至最新組建。

>[!IMPORTANT]
>您必須依照更新的發行順序來進行安裝。 不可略過版本。 請參閱[部署資源提供者先決條件](.\azure-stack-sql-resource-provider-deploy.md#prerequisites)中的版本清單。

## <a name="overview"></a>概觀

若要更新資源提供者，請使用 *UpdateSQLProvider.ps1* 指令碼。 此指令碼隨附於所下載的新 SQL 資源提供者中。 此更新程序類似於用來[部署資源提供者](.\azure-stack-sql-resource-provider-deploy.md)的程序。 更新指令碼會使用和 DeploySqlProvider.ps1 指令碼相同的引數，而且您必須提供憑證資訊。

### <a name="update-script-processes"></a>更新指令碼程序

UpdateSQLProvider.ps1 指令碼會以最新的資源提供者程式碼建立新的虛擬機器 (VM)。

>[!NOTE]
>建議您從 Marketplace Management 下載最新的 Windows Server 2016 Core 映像。 如果您需要安裝某個更新，可以將**單一** MSU 套件放在本機相依性路徑中。 如果這個位置中有多個 MSU 檔案，指令碼就會失敗。

UpdateSQLProvider.ps1 指令碼在建立新的 VM 後，會從舊提供者 VM 遷移下列設定：

* 資料庫資訊
* 裝載伺服器資訊
* 必要的 DNS 記錄

### <a name="update-script-powershell-example"></a>更新指令碼 PowerShell 範例

您可以從提高權限的 PowerShell ISE 編輯並執行下列指令碼。 請記得視需要變更環境的帳戶資訊和密碼。

> [!NOTE]
> 此更新程序僅適用於 Azure Stack 整合式系統。

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

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
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `

 ```

## <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 參數

您可以在執行指令碼時從命令列指定下列參數。 如果未指定參數，或任何參數驗證失敗，系統就會提示您提供所需的參數。

| 參數名稱 | 說明 | 註解或預設值 |
| --- | --- | --- |
| **CloudAdminCredential** | 雲端管理員的認證，這是存取具特殊權限端點所需的認證。 | _必要_ |
| **AzCredential** | Azure Stack 服務系統管理員帳戶的認證。 使用與部署 Azure Stack 時所用認證相同的認證。 | _必要_ |
| **VMLocalCredential** | SQL 資源提供者 VM 之本機系統管理員帳戶的認證。 | _必要_ |
| **PrivilegedEndpoint** | 具特殊權限端點的 IP 位址或 DNS 名稱。 |  _必要_ |
| **DependencyFilesLocalPath** | 您也必須將憑證 .pfx 檔案放在這個目錄中。 | 若為單一節點，屬選擇性，若為多重節點，則屬必要 |
| **DefaultSSLCertificatePassword** | .pfx 憑證的密碼。 | _必要_ |
| **MaxRetryCount** | 當作業失敗時，您想要重試每個作業的次數。| 2 |
| **RetryDuration** |重試之間的逾時間隔 (秒)。 | 120 |
| **解除安裝** | 移除資源提供者及所有關聯的資源。 | 否 |
| **DebugMode** | 防止在失敗時自動清除。 | 否 |

## <a name="next-steps"></a>後續步驟

[維護 SQL 資源提供者](azure-stack-sql-resource-provider-maintain.md)
