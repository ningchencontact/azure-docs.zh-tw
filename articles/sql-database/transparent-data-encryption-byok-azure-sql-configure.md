---
title: PowerShell 和 CLI：啟用 SQL TDE - 使用 Azure Key Vault - 攜帶您自己的金鑰 - Azure SQL Database | Microsoft Docs
description: 了解如何設定 Azure SQL Database 和資料倉儲，以透過 PowerShell 或 CLI 開始使用透明資料加密 (TDE) 進行待用資料加密。
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
ms.openlocfilehash: c42c6175512105de38a29be260c370851e152137
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60330868"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell 和 CLI：從 Azure Key Vault 使用客戶管理的金鑰啟用透明資料加密

本文將逐步解說如何將 Azure Key Vault 中的金鑰用於 SQL Database 或資料倉儲上的透明資料加密 (TDE)。 若要深入了解以 Azure Key Vault 整合進行 TDE - 攜帶您自己的金鑰 (BYOK) 的支援，請參閱 [Azure SQL 透明資料加密：「攜帶您自己的金鑰」支援](transparent-data-encryption-byok-azure-sql.md)。 

## <a name="prerequisites-for-powershell"></a>PowerShell 的必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

- 您必須具有 Azure 訂用帳戶，並且是該訂用帳戶的系統管理員。
- [建議執行的選擇性作業] 備妥硬體安全性模組 (HSM) 或本機金鑰存放區，用來建立 TDE 保護裝置金鑰內容的本機複本。
- 您必須安裝並執行 Azure PowerShell。 
- 建立要用於 TDE 的 Azure Key Vault 和金鑰。
  - [從 Key Vault 使用 PowerShell 的指示](../key-vault/key-vault-overview.md)
  - [使用硬體安全性模組 (HSM) 與 Key Vault 的指示](../key-vault/key-vault-hsm-protected-keys.md)
    - 金鑰保存庫必須具有下列屬性才能用於 TDE：
  - [虛刪除](../key-vault/key-vault-ovw-soft-delete.md)
  - [如何使用 Key Vault 虛刪除與 PowerShell](../key-vault/key-vault-soft-delete-powershell.md) 
- 金鑰必須具有下列屬性才能用於 TDE：
   - 沒有到期日
   - 未停用
   - 能夠執行*取得*、*包裝金鑰*、*解除包裝金鑰*作業

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>步驟 1. 將 Azure AD 身分識別指派給您的伺服器 

如果您有現有的伺服器，請使用下列程式碼將 Azure AD 身分識別新增至您的伺服器：

   ```powershell
   $server = Set-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

如果您要建立伺服器，使用[新增 AzSqlServer](/powershell/module/az.sql/new-azsqlserver) cmdlet 搭配標記位在伺服器建立期間新增 Azure AD 身分識別的身分識別：

   ```powershell
   $server = New-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>步驟 2. 為您的伺服器授與 Key Vault 權限

使用[組 AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet 可授與您伺服器的存取金鑰保存庫之前將它的金鑰用於 TDE。

   ```powershell
   Set-AzKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>步驟 3. 將 Key Vault 金鑰新增至伺服器，並設定 TDE 保護裝置

- 使用[新增 AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) cmdlet 將金鑰從 Key Vault 新增至伺服器。
- 使用[組 AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet 來設定所有伺服器資源的 TDE 保護裝置的金鑰。
- 使用[Get AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet，確認已設定的 TDE 保護裝置，如預期般。

> [!Note]
> 金鑰保存庫名稱和金鑰名稱的合併長度不可超過 94 個字元。
> 

>[!Tip]
>Key Vault 中的範例 KeyId： https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>步驟 4. 開啟 TDE 

使用[組 AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet 來開啟 tde。

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

現在，資料庫或資料倉儲已使用 Key Vault 中的加密金鑰啟用 TDE。

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>步驟 5。 檢查加密狀態和加密活動

使用[Get AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption)若要取得加密狀態並[Get AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity)來檢查資料庫的加密進度或資料倉儲。

   ```powershell
   # Get the encryption state
   Get-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>其他實用的 PowerShell Cmdlet

- 使用[組 AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet 來關閉 TDE。

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- 使用[Get AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey)指令程式可傳回清單中的 Key Vault 金鑰新增至伺服器。

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- 使用[移除 AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey)從伺服器移除 Key Vault 金鑰。

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>疑難排解

如果發生問題，請進行下列檢查：
- 如果找不到金鑰保存庫，請確定您在使用正確的訂用帳戶[Get AzSubscription](/powershell/module/az.accounts/get-azsubscription) cmdlet。

   ```powershell
   Get-AzSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- 如果新的金鑰無法新增至伺服器，或新的金鑰無法更新為 TDE 保護裝置，請進行下列檢查：
   - 金鑰不應有到期日
   - 金鑰必須已啟用*取得*、*包裝金鑰*和*解除包裝金鑰*作業。

## <a name="next-steps"></a>後續步驟

- 了解如何輪替伺服器的 TDE 保護裝置以符合安全需求：[使用 PowerShell 輪替透明資料加密保護裝置](transparent-data-encryption-byok-azure-sql-key-rotation.md)。
- 如有安全性風險，請了解如何移除可能遭破壞的 TDE 保護裝置：[移除可能遭破壞的金鑰](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)。 

## <a name="prerequisites-for-cli"></a>CLI 的必要條件

- 您必須具有 Azure 訂用帳戶，並且是該訂用帳戶的系統管理員。
- [建議執行的選擇性作業] 備妥硬體安全性模組 (HSM) 或本機金鑰存放區，用來建立 TDE 保護裝置金鑰內容的本機複本。
- 命令列介面 2.0 版或更新版本。 若要安裝最新版本，並連線至 Azure 訂用帳戶，請參閱[安裝與設定 Azure 跨平台命令列介面 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 
- 建立要用於 TDE 的 Azure Key Vault 和金鑰。
  - [使用 CLI 2.0 管理 Key Vault](../key-vault/key-vault-manage-with-cli2.md)
  - [使用硬體安全性模組 (HSM) 與 Key Vault 的指示](../key-vault/key-vault-hsm-protected-keys.md)
    - 金鑰保存庫必須具有下列屬性才能用於 TDE：
  - [虛刪除](../key-vault/key-vault-ovw-soft-delete.md)
  - [如何使用 Key Vault 虛刪除與 CLI](../key-vault/key-vault-soft-delete-cli.md) 
- 金鑰必須具有下列屬性才能用於 TDE：
   - 沒有到期日
   - 未停用
   - 能夠執行*取得*、*包裝金鑰*、*解除包裝金鑰*作業
   
## <a name="step-1-create-a-server-with-an-azure-ad-identity"></a>步驟 1. 使用 Azure AD 身分識別建立伺服器
      cli
      # create server (with identity) and database
      az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
      az sql db create --name <dbname> --server <servername> --resource-group <rgname>  
 
 
>[!Tip]
>防止"principalID" 建立伺服器，它是用於在下一步中指派金鑰保存庫使用權限的物件識別碼
>
 
## <a name="step-2-grant-key-vault-permissions-to-the-logical-sql-server"></a>步驟 2. 為邏輯 SQL 伺服器授與 Key Vault 權限
      cli
      # create key vault, key and grant permission
       az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
       az keyvault key create --name <keyname> --vault-name <kvname> --protection software
       az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get 


>[!Tip]
>請保留新金鑰的金鑰 URI 或 keyID 以在下一個步驟中使用，例如： https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
 
       
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>步驟 3. 將 Key Vault 金鑰新增至伺服器，並設定 TDE 保護裝置
  
     cli
     # add server key and update encryption protector
     az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
     az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>

        
  > [!Note]
> 金鑰保存庫名稱和金鑰名稱的合併長度不可超過 94 個字元。
> 

  
## <a name="step-4-turn-on-tde"></a>步驟 4. 開啟 TDE 
      cli
      # enable encryption
      az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled 
      

現在，資料庫或資料倉儲已使用 Azure Key Vault 中客戶管理的加密金鑰啟用 TDE。

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>步驟 5。 檢查加密狀態和加密活動

     cli
      # get encryption scan progress
      az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

      # get whether encryption is on or off
      az sql db tde show --database <dbname> --server <servername> --resource-group <rgname> 

## <a name="sql-cli-references"></a>SQL CLI 參考

https://docs.microsoft.com/cli/azure/sql 

https://docs.microsoft.com/cli/azure/sql/server/key 

https://docs.microsoft.com/cli/azure/sql/server/tde-key 

https://docs.microsoft.com/cli/azure/sql/db/tde 

