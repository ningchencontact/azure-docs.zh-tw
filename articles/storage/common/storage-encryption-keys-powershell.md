---
title: 設定客戶管理的金鑰，從 PowerShell 的 Azure 儲存體加密
description: 了解如何使用 PowerShell 來設定 Azure 儲存體加密的客戶管理金鑰。 客戶管理的金鑰可讓您建立、 輪替、 停用和撤銷的存取控制。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 01d5226a2d6f4c4e2e376803a3f0a25d2d5b0fd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61593558"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-powershell"></a>設定客戶管理的金鑰，從 PowerShell 的 Azure 儲存體加密

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文說明如何使用客戶管理的金鑰，使用 PowerShell 設定金鑰保存庫。

## <a name="assign-an-identity-to-the-storage-account"></a>指派給儲存體帳戶的身分識別

若要啟用儲存體帳戶的客戶管理的金鑰，第一次指派給儲存體帳戶的系統指派的受控身分識別。 您將使用這個受管理的身分識別來授與存取金鑰保存庫的儲存體帳戶的權限。

若要指派的受管理的身分識別，使用 PowerShell，呼叫[組 AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)。 請務必以您自己的值取代方括號中的預留位置值。

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

如需有關如何使用 PowerShell 設定系統指派給受控身分識別的詳細資訊，請參閱 <<c0> [ 設定管理使用 PowerShell 為 Azure 虛擬機器上的 Azure 資源的身分識別](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)。

## <a name="create-a-new-key-vault"></a>建立新的金鑰保存庫

若要建立新的金鑰保存庫，以使用 PowerShell，呼叫[新增 AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)。 您用來儲存客戶管理的金鑰，Azure 儲存體加密，必須有兩個金鑰的保護設定已啟用，金鑰保存庫**虛刪除**並**不要清除**。 

請務必以您自己的值取代方括號中的預留位置值。 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>設定金鑰保存庫的存取原則

接下來，設定金鑰保存庫的存取原則，使儲存體帳戶具有存取權限。 在此步驟中，您將使用您先前指派給儲存體帳戶的受管理身分識別。

若要設定金鑰保存庫的存取原則，請呼叫[組 AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)。 請記得以您自己的值取代方括號中的預留位置值，並將先前的範例中所定義的變數。

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>建立新的金鑰

接下來，建立新的金鑰在金鑰保存庫。 若要建立新的金鑰，請呼叫[新增 AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)。 請記得以您自己的值取代方括號中的預留位置值，並將先前的範例中所定義的變數。

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>使用客戶管理的金鑰設定加密

根據預設，Azure 儲存體加密會使用 Microsoft 管理的金鑰。 在此步驟中，設定您的 Azure 儲存體帳戶，以使用客戶管理的金鑰，並指定要與儲存體帳戶相關聯的索引鍵。

呼叫[組 AzStorageAccount](/powershell/module/az.keyvault/set-azstorageaccount)更新儲存體帳戶的加密設定。 請記得以您自己的值取代方括號中的預留位置值，並將先前的範例中所定義的變數。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>更新金鑰的版本

當您建立新版本的金鑰時，您將需要更新儲存體帳戶，以使用新的版本。 首先，呼叫[Get AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey)以取得最新版的金鑰。 然後呼叫[組 AzStorageAccount](/powershell/module/az.keyvault/set-azstorageaccount)更新儲存體帳戶的加密設定，以使用新版本的金鑰上, 一節中所示。

## <a name="next-steps"></a>後續步驟

- [待用資料的 azure 儲存體加密](storage-service-encryption.md) 
- [什麼是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)？
