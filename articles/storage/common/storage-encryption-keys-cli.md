---
title: 設定客戶管理的金鑰，從 Azure CLI 的 Azure 儲存體加密
description: 了解如何使用 Azure CLI 來設定 Azure 儲存體加密的客戶管理金鑰。 客戶管理的金鑰可讓您建立、 輪替、 停用和撤銷的存取控制。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/24/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 925b69e064e260a78a102a068f052ad7d396c380
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357065"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>設定客戶管理的金鑰，從 Azure CLI 的 Azure 儲存體加密

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文說明如何使用客戶管理的金鑰，使用 Azure CLI 設定金鑰保存庫。

## <a name="assign-an-identity-to-the-storage-account"></a>指派給儲存體帳戶的身分識別

若要啟用儲存體帳戶的客戶管理的金鑰，第一次指派給儲存體帳戶的系統指派的受控身分識別。 您將使用這個受管理的身分識別來授與存取金鑰保存庫的儲存體帳戶的權限。

若要將指派的受控身分識別使用 Azure CLI，請呼叫[az 儲存體帳戶更新](/cli/azure/storage/account#az-storage-account-update)。 請務必以您自己的值取代方括號中的預留位置值。

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

如需有關如何使用 Azure CLI 設定系統指派給受控身分識別的詳細資訊，請參閱 <<c0> [ 設定受管理身分識別使用 Azure CLI 在 Azure VM 上的 Azure 資源的](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)。

## <a name="create-a-new-key-vault"></a>建立新的金鑰保存庫

您用來儲存客戶管理的金鑰，Azure 儲存體加密，必須有兩個金鑰的保護設定已啟用，金鑰保存庫**虛刪除**並**不要清除**。 若要建立新的金鑰保存庫，以啟用這些設定中使用 PowerShell 或 Azure CLI，執行下列命令。 請務必以您自己的值取代方括號中的預留位置值。 

若要建立新的金鑰保存庫，以使用 Azure CLI，請呼叫[az keyvault 建立](/cli/azure/keyvault#az-keyvault-create)。 請務必以您自己的值取代方括號中的預留位置值。

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>設定金鑰保存庫的存取原則

接下來，設定金鑰保存庫的存取原則，使儲存體帳戶具有存取權限。 在此步驟中，您將使用您先前指派給儲存體帳戶的受管理身分識別。

若要設定金鑰保存庫的存取原則，請呼叫[az keyvault 設定原則](/cli/azure/keyvault#az-keyvault-set-policy)。 請務必以您自己的值取代方括號中的預留位置值。

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>建立新的金鑰

接下來，建立金鑰保存庫中的金鑰。 若要建立金鑰，呼叫[az keyvault key 建立](/cli/azure/keyvault/key#az-keyvault-key-create)。 請務必以您自己的值取代方括號中的預留位置值。

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>使用客戶管理的金鑰設定加密

根據預設，Azure 儲存體加密會使用 Microsoft 管理的金鑰。 設定您的 Azure 儲存體帳戶的客戶管理的金鑰，並指定要與儲存體帳戶相關聯的索引鍵。

若要更新儲存體帳戶的加密設定，請呼叫[az 儲存體帳戶更新](/cli/azure/storage/account#az-storage-account-update)。 此範例也會查詢的金鑰保存庫 URI 和金鑰版本，這兩個值所需的儲存體帳戶相關聯的索引鍵。 請務必以您自己的值取代方括號中的預留位置值。

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update 
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>更新金鑰的版本

當您建立新版本的金鑰時，您將需要更新儲存體帳戶，以使用新的版本。 首先，藉由呼叫查詢金鑰保存庫 URI [az keyvault show](/cli/azure/keyvault#az-keyvault-show)，以及藉由呼叫金鑰版本[az keyvault 金鑰清單-版本](/cli/azure/keyvault/key#az-keyvault-key-list-versions)。 然後呼叫[az 儲存體帳戶更新](/cli/azure/storage/account#az-storage-account-update)更新儲存體帳戶的加密設定，以使用新版本的金鑰上, 一節中所示。

## <a name="next-steps"></a>後續步驟

- [待用資料的 azure 儲存體加密](storage-service-encryption.md) 
- [什麼是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)？
