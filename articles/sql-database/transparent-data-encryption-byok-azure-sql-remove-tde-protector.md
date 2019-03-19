---
title: PowerShell - 移除 TDE 保護裝置 - Azure SQL Database| Microsoft Docs
description: 本操作指南說明 Azure SQL Database 或資料倉儲若是使用具有「攜帶您自己的金鑰」(BYOK) 支援的 TDE，在其 TDE 保護裝置可能遭破壞時，應如何因應。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: a49c22a0597c34075de3e5fd7b9a324169e1da00
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "57895164"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>使用 PowerShell 移除透明資料加密 (TDE) 保護裝置

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database，仍然支援 PowerShell 的 Azure Resource Manager 模組，但所有未來的開發是 Az.Sql 模組。 這些指令程式，請參閱 < [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 在 Az 模組和 AzureRm 模組中命令的引數是本質上相同的。

- 您必須具有 Azure 訂用帳戶，並且是該訂用帳戶的系統管理員
- 您必須安裝並執行 Azure PowerShell。 
- 本操作指南假設您已使用 Azure Key Vault 中的金鑰作為 Azure SQL Database 或資料倉儲的 TDE 保護裝置。 若要深入了解，請參閱[具有 BYOK 支援的透明資料加密](transparent-data-encryption-byok-azure-sql.md)。

## <a name="overview"></a>概觀

本操作指南說明 Azure SQL Database 或資料倉儲若是在 Azure Key Vault 中搭配由客戶管理的金鑰來使用 TDE (攜帶您自己的金鑰 (BYOK) 支援)，在其 TDE 保護裝置可能遭破壞時，應如何因應。 若要深入了解 TDE 的 BYOK 支援，請參閱[概觀頁面](transparent-data-encryption-byok-azure-sql.md)。 

只有在極端情況下或測試環境中，才需要執行下列程序。 請仔細檢閱本操作指南，因為從 Azure Key Vault 中刪除正在使用的 TDE 保護裝置，可能會導致**資料遺失**。 

如果金鑰疑似遭到破壞，而使服務或使用者可在未經授權的情況下存取金鑰，則最好將金鑰刪除。

請切記，在 Key Vault 中刪除 TDE 保護裝置後，**系統會封鎖該伺服器下所有與已加密資料庫的連線；這些資料庫會離線，並在 24 小時內遭到捨棄**。 以遭破壞的金鑰加密的舊備份，將不再可供存取。

本操作指南會根據事件回應之後的所需結果討論兩種方法：

- 將 Azure SQL 資料庫/資料倉儲保持為**可供存取**
- 使 Azure SQL 資料庫/資料倉儲變成**無法存取**

## <a name="to-keep-the-encrypted-resources-accessible"></a>將加密資源保持為可供存取

1. 建立 [Key Vault 中的新金鑰](/powershell/module/az.keyvault/add-azkeyvaultkey)。 請務必將此新金鑰建立在與可能遭破壞的 TDE 保護裝置不同的金鑰保存庫中，因為存取控制佈建於保存庫層級上。
2. 將新的金鑰新增至伺服器中使用[新增 AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)並[組 AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet 並將它更新為新伺服器的 TDE 保護裝置。

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   # Set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. 請確定伺服器和任何複本已更新為使用新的 TDE 保護裝置[Get AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet。 

   >[!NOTE]
   > 新的 TDE 保護裝置可能需要幾分鐘的時間才能傳播至伺服器下的所有資料庫和次要資料庫。

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. 在 Key Vault 中建立[新金鑰的備份](/powershell/module/az.keyvault/backup-azkeyvaultkey)。

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. 使用 Key Vault 刪除遭盜用的金鑰[移除 AzKeyVaultKey](/powershell/module/azurerm.keyvault/remove-azurekeyvaultkey) cmdlet。 

   ```powershell
   Remove-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. 若要還原至金鑰保存庫在未來使用的金鑰[還原 AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) cmdlet:
   ```powershell
   Restore-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```

## <a name="to-make-the-encrypted-resources-inaccessible"></a>使加密資源變得無法存取

1. 捨棄以可能遭破壞的金鑰加密的資料庫。

   資料庫和記錄檔會自動備份，因此資料庫的時間點還原可以在任何時間點執行 (只要您提供金鑰)。 資料庫必須在作用中的 TDE 保護裝置刪除之前捨棄，否則最多可能會遺失最新交易 10 分鐘內的資料。 
2. 在 Key Vault 中備份 TDE 保護裝置的金鑰內容。
3. 從 Key Vault 中移除可能遭破壞的金鑰

## <a name="next-steps"></a>後續步驟

- 了解如何輪替伺服器的 TDE 保護裝置以符合安全需求：[使用 PowerShell 輪替透明資料加密保護裝置](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- 開始將「攜帶您自己的金鑰支援」用於 TDE：[透過 PowerShell 從 Key Vault 使用您自己的金鑰開啟 TDE](transparent-data-encryption-byok-azure-sql-configure.md)
