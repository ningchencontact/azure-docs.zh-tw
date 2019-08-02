---
title: PowerShell - 輪替 TDE 保護裝置 - Azure SQL Database| Microsoft Docs
description: 了解如何輪替 Azure SQL Server 的透明資料加密 (TDE) 保護裝置。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 464ea73d9b3d7116205377600ffccee13a9e2dcb
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566044"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>使用 PowerShell 輪替透明資料加密 (TDE) 保護裝置

本文說明如何使用 TDE 保護裝置從 Azure Key Vault 進行 Azure SQL Server 的金鑰輪替。 輪替 Azure SQL Server 的 TDE 保護裝置，其實就是切換為新的非對稱金鑰來保護伺服器上的資料庫。 金鑰輪替是一項線上作業，應可在幾秒內完成，因為此作業只會解密和重新加密資料庫的資料加密金鑰，而不是整個資料庫。

本指南將討論用來在伺服器上輪替 TDE 保護裝置的兩個選項。

> [!NOTE]
> 進行金鑰輪替之前，必須先恢復暫停的 SQL 資料倉儲。
>

> [!IMPORTANT]
> 在完成變換後，**請勿刪除**舊版的金鑰。  在金鑰變換後，部分資料仍會以先前的金鑰加密，例如較舊的資料庫備份。 
>

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組, 但所有未來的開發都是針對 Az .Sql 模組。 如需這些 Cmdlet, 請參閱[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。

- 本操作指南假設您已使用 Azure Key Vault 中的金鑰作為 Azure SQL Database 或資料倉儲的 TDE 保護裝置。 請參閱[具有 BYOK 支援的透明資料加密](transparent-data-encryption-byok-azure-sql.md)。
- 您必須已安裝且正在執行 Azure PowerShell。
- [建議執行的選擇性作業] 先在硬體安全性模組 (HSM) 或本機金鑰存放區中建立 TDE 保護裝置的金鑰內容，然後將金鑰內容匯入至 Azure Key Vault。 請依照[使用硬體安全性模組 (HSM) 與 Key Vault 的指示](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)操作，以深入了解。

## <a name="manual-key-rotation"></a>手動金鑰輪替

手動金鑰輪替會使用[AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey)、 [AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)和[AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) Cmdlet 來新增全新的金鑰, 這可能會在新的索引鍵名稱或甚至是另一個金鑰底下保險櫃. 使用此方法，可支援將相同金鑰新增至不同的金鑰保存庫，以支援高可用性和異地災害復原案例。

>[!NOTE]
>金鑰保存庫名稱和金鑰名稱的合併長度不可超過 94 個字元。

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
  
   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```


## <a name="other-useful-powershell-cmdlets"></a>其他實用的 PowerShell Cmdlet

- 若要將 TDE 保護裝置從 Microsoft 管理切換到 BYOK 模式, 請使用[AzSqlServerTransparentDataEncryptionProtector 指令程式](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)。

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- 若要將 TDE 保護裝置從 BYOK 模式切換到 Microsoft 管理, 請使用[AzSqlServerTransparentDataEncryptionProtector 指令程式](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)。

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>後續步驟

- 如有安全性風險，請了解如何移除可能遭破壞的 TDE 保護裝置：[移除可能遭破壞的金鑰](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- 針對 TDE 開始使用 Azure Key Vault 整合與攜帶您自己的金鑰支援：[透過 PowerShell 從 Key Vault 使用您自己的金鑰開啟 TDE](transparent-data-encryption-byok-azure-sql-configure.md)
