---
title: 設定客戶管理的金鑰以從 PowerShell 進行 Azure 儲存體加密
description: 瞭解如何使用 PowerShell 來設定客戶管理的金鑰，以進行 Azure 儲存體加密。 客戶管理的金鑰可讓您建立、輪替、停用及撤銷存取控制。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 982996bdbc5e618cf90aa4b028c0585a7898f465
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2019
ms.locfileid: "71670966"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-powershell"></a>設定客戶管理的金鑰以從 PowerShell 進行 Azure 儲存體加密

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文說明如何使用 PowerShell 以客戶管理的金鑰來設定金鑰保存庫。

> [!IMPORTANT]
> 使用客戶管理的金鑰搭配 Azure 儲存體加密時，金鑰保存庫必須設定兩個必要屬性，即虛**刪除**和「不要**清除**」。 當您在 Azure 入口網站中建立新的金鑰保存庫時，預設會啟用這些屬性。 不過，如果您需要在現有的金鑰保存庫上啟用這些屬性，則必須使用 PowerShell 或 Azure CLI。
> 僅支援 RSA 金鑰和金鑰大小2048。

## <a name="assign-an-identity-to-the-storage-account"></a>將身分識別指派給儲存體帳戶

若要為您的儲存體帳戶啟用客戶管理的金鑰，請先將系統指派的受控識別指派給儲存體帳戶。 您將使用此受控識別來授與儲存體帳戶存取金鑰保存庫的許可權。

若要使用 PowerShell 指派受控識別，請呼叫[new-azstorageaccount](/powershell/module/az.storage/set-azstorageaccount)。 請記得以您自己的值取代括弧中的預留位置值。

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

如需有關使用 PowerShell 來設定系統指派的受控識別的詳細資訊，請參閱[使用 powershell 在 AZURE VM 上設定 azure 資源的受控](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)識別。

## <a name="create-a-new-key-vault"></a>建立新的金鑰保存庫

若要使用 PowerShell 建立新的金鑰保存庫，請呼叫[AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)。 您用來儲存 Azure 儲存體加密客戶管理金鑰的金鑰保存庫，必須啟用兩個金鑰保護設定： [虛**刪除**] 和 [不要**清除**]。 

請記得以您自己的值取代括弧中的預留位置值。 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>設定 key vault 存取原則

接下來，設定金鑰保存庫的存取原則，讓儲存體帳戶有權存取它。 在此步驟中，您將使用先前指派給儲存體帳戶的受控識別。

若要設定金鑰保存庫的存取原則，請呼叫[set-set-azkeyvaultaccesspolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)。 請記得以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>建立新金鑰

接下來，在金鑰保存庫中建立新的金鑰。 若要建立新的金鑰，請呼叫[AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)。 請記得以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>使用客戶管理的金鑰設定加密

根據預設，Azure 儲存體加密會使用 Microsoft 管理的金鑰。 在此步驟中，請將您的 Azure 儲存體帳戶設定為使用客戶管理的金鑰，並指定要與儲存體帳戶建立關聯的金鑰。

呼叫[new-azstorageaccount](/powershell/module/az.storage/set-azstorageaccount)以更新儲存體帳戶的加密設定。 請記得以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>更新金鑰版本

當您建立新版本的金鑰時，您必須更新儲存體帳戶，才能使用新版本。 首先，呼叫[AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey)以取得最新版本的金鑰。 然後呼叫[new-azstorageaccount](/powershell/module/az.storage/set-azstorageaccount) ，將儲存體帳戶的加密設定更新為使用新版本的金鑰，如上一節所示。

## <a name="next-steps"></a>後續步驟

- [待用資料的 Azure 儲存體加密](storage-service-encryption.md) 
- [什麼是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
