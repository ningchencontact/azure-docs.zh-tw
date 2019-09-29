---
title: 設定客戶管理的金鑰，以從 Azure CLI Azure 儲存體加密
description: 瞭解如何使用 Azure CLI 來設定 Azure 儲存體加密的客戶管理金鑰。 客戶管理的金鑰可讓您建立、輪替、停用及撤銷存取控制。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 036322e73470ad4aa25de03e95c506e9f04496d8
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2019
ms.locfileid: "71670989"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>設定客戶管理的金鑰，以從 Azure CLI Azure 儲存體加密

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文說明如何使用 Azure CLI 以客戶管理的金鑰來設定金鑰保存庫。

> [!IMPORTANT]
> 使用客戶管理的金鑰搭配 Azure 儲存體加密時，金鑰保存庫必須設定兩個必要屬性，即虛**刪除**和「不要**清除**」。 當您在 Azure 入口網站中建立新的金鑰保存庫時，預設會啟用這些屬性。 不過，如果您需要在現有的金鑰保存庫上啟用這些屬性，則必須使用 PowerShell 或 Azure CLI。
> 僅支援 RSA 金鑰和金鑰大小2048。

## <a name="assign-an-identity-to-the-storage-account"></a>將身分識別指派給儲存體帳戶

若要為您的儲存體帳戶啟用客戶管理的金鑰，請先將系統指派的受控識別指派給儲存體帳戶。 您將使用此受控識別來授與儲存體帳戶存取金鑰保存庫的許可權。

若要使用 Azure CLI 指派受控識別，請呼叫[az storage account update](/cli/azure/storage/account#az-storage-account-update)。 請記得以您自己的值取代括弧中的預留位置值。

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

如需有關使用 Azure CLI 設定系統指派的受控識別的詳細資訊，請參閱[使用 Azure CLI 在 AZURE VM 上設定 azure 資源的受控](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)識別。

## <a name="create-a-new-key-vault"></a>建立新的金鑰保存庫

您用來儲存 Azure 儲存體加密客戶管理金鑰的金鑰保存庫，必須啟用兩個金鑰保護設定： [虛**刪除**] 和 [不要**清除**]。 若要在啟用這些設定的情況下使用 PowerShell 或 Azure CLI 建立新的金鑰保存庫，請執行下列命令。 請記得以您自己的值取代括弧中的預留位置值。 

若要使用 Azure CLI 建立新的金鑰保存庫，請呼叫[az keyvault create](/cli/azure/keyvault#az-keyvault-create)。 請記得以您自己的值取代括弧中的預留位置值。

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>設定 key vault 存取原則

接下來，設定金鑰保存庫的存取原則，讓儲存體帳戶有權存取它。 在此步驟中，您將使用先前指派給儲存體帳戶的受控識別。

若要設定金鑰保存庫的存取原則，請呼叫[az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy)。 請記得以您自己的值取代括弧中的預留位置值。

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

## <a name="create-a-new-key"></a>建立新金鑰

接下來，在金鑰保存庫中建立金鑰。 若要建立金鑰，請呼叫[az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create)。 請記得以您自己的值取代括弧中的預留位置值。

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>使用客戶管理的金鑰設定加密

根據預設，Azure 儲存體加密會使用 Microsoft 管理的金鑰。 為客戶管理的金鑰設定您的 Azure 儲存體帳戶，並指定要與儲存體帳戶建立關聯的金鑰。

若要更新儲存體帳戶的加密設定，請呼叫[az storage account update](/cli/azure/storage/account#az-storage-account-update)。 此範例也會查詢金鑰保存庫 URI 和金鑰版本，這兩個值都需要用來將金鑰與儲存體帳戶建立關聯。 請記得以您自己的值取代括弧中的預留位置值。

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

## <a name="update-the-key-version"></a>更新金鑰版本

當您建立新版本的金鑰時，您必須更新儲存體帳戶，才能使用新版本。 首先，藉由呼叫 az [keyvault show](/cli/azure/keyvault#az-keyvault-show)，並針對金鑰版本查詢金鑰保存庫 URI，方法是呼叫[az keyvault key list-版本](/cli/azure/keyvault/key#az-keyvault-key-list-versions)。 然後呼叫[az storage account update](/cli/azure/storage/account#az-storage-account-update)來更新儲存體帳戶的加密設定，以使用新版本的金鑰，如上一節所示。

## <a name="next-steps"></a>後續步驟

- [待用資料的 Azure 儲存體加密](storage-service-encryption.md) 
- [什麼是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
