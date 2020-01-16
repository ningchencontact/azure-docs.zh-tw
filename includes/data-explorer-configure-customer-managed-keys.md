---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 5443ee6912c30b89cee6fdb43f84f3bc1fbcfe68
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020908"
---
Azure 資料總管會加密待用儲存體帳戶中的所有資料。 根據預設，資料會使用 Microsoft 管理的金鑰進行加密。 若要進一步控制加密金鑰，您可以提供客戶管理的金鑰以用於資料加密。 客戶管理的金鑰必須儲存在[Azure Key Vault](/azure/key-vault/key-vault-overview)中。 您可以建立自己的金鑰，並將其儲存在金鑰保存庫中，或者您可以使用 Azure Key Vault API 來產生金鑰。 Azure 資料總管叢集和金鑰保存庫必須位於相同的區域中，但它們可以位於不同的訂用帳戶中。 如需客戶管理之金鑰的詳細說明，請參閱[客戶管理的金鑰與 Azure Key Vault](/azure/storage/common/storage-service-encryption)。 本文說明如何設定客戶管理的金鑰。

> [!Note]
> 若要使用 Azure 資料總管來設定客戶管理的金鑰，您必須[在金鑰保存庫上設定兩個屬性](/azure/key-vault/key-vault-ovw-soft-delete)：「虛**刪除**」和「不要**清除**」。 預設不會啟用這些屬性。 若要啟用這些屬性，請使用[PowerShell](/azure/key-vault/key-vault-soft-delete-powershell)或[Azure CLI](/azure/key-vault/key-vault-soft-delete-cli)。 僅支援 RSA 金鑰和金鑰大小2048。

## <a name="assign-an-identity-to-the-cluster"></a>將身分識別指派給叢集

若要為您的叢集啟用客戶管理的金鑰，請先將系統指派的受控識別指派給叢集。 您將使用此受控識別來授與叢集許可權以存取金鑰保存庫。 若要設定系統指派的受控識別，請參閱[受控](/azure/data-explorer/managed-identities)識別。

## <a name="create-a-new-key-vault"></a>建立新的金鑰保存庫

若要使用 PowerShell 建立新的金鑰保存庫，請呼叫[AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)。 您用來儲存 Azure 資料總管加密客戶管理金鑰的金鑰保存庫，必須啟用兩個金鑰保護設定： [虛**刪除**] 和 [**不要清除**]。 以您自己的值取代括弧中的預留位置值，如下列範例所示。

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>設定 key vault 存取原則

接下來，設定金鑰保存庫的存取原則，讓叢集有許可權可以存取它。 在此步驟中，您將使用先前指派給叢集的系統指派受控識別。 若要設定金鑰保存庫的存取原則，請呼叫[set-set-azkeyvaultaccesspolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)。 以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>建立新的金鑰

接下來，在金鑰保存庫中建立新的金鑰。 若要建立新的金鑰，請呼叫[AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)。 以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
