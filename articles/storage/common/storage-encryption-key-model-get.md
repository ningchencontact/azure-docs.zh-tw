---
title: 判斷用於儲存體帳戶的加密金鑰模型
titleSuffix: Azure Storage
description: 使用 Azure 入口網站、PowerShell 或 Azure CLI 來檢查如何管理儲存體帳戶的加密金鑰。 金鑰可能是由 Microsoft （預設值）或客戶管理。 客戶管理的金鑰必須儲存在 Azure Key Vault 中。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 3806fead9226978c277e87f3d97b14ee38d9552d
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665416"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>判斷用於儲存體帳戶的 Azure 儲存體加密金鑰模型

Azure 儲存體會自動加密儲存體帳戶中的資料。 Azure 儲存體加密提供兩個選項來管理儲存體帳戶層級的加密金鑰：

- **Microsoft 管理的金鑰。** 根據預設，Microsoft 會管理用來加密儲存體帳戶的金鑰。
- **客戶管理的金鑰。** 您可以選擇性地選擇管理儲存體帳戶的加密金鑰。 客戶管理的金鑰必須儲存在 Azure Key Vault 中。

此外，您可以針對某些 Blob 儲存體作業，提供個別要求層級的加密金鑰。 在要求上指定加密金鑰時，該金鑰會覆寫儲存體帳戶上作用中的加密金鑰。 如需詳細資訊，請參閱[在對 Blob 儲存體的要求上指定客戶提供的金鑰](../blobs/storage-blob-customer-provided-key.md)。

如需加密金鑰的詳細資訊，請參閱待用[資料的加密 Azure 儲存體](storage-service-encryption.md)。

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>檢查儲存體帳戶的加密金鑰模型

若要判斷儲存體帳戶是否使用 Microsoft 管理的金鑰或客戶管理的金鑰進行加密，請使用下列其中一種方法。

# <a name="azure-portaltabportal"></a>[Azure 入口網站](#tab/portal)

若要使用 Azure 入口網站來檢查儲存體帳戶的加密模型，請遵循下列步驟：

1. 在 Azure 入口網站中，瀏覽至您的儲存體帳戶。
1. 選取 [**加密**] 設定，並記下設定。

下圖顯示使用客戶管理的金鑰進行加密的儲存體帳戶：

![顯示 Azure 入口網站中加密金鑰設定的螢幕擷取畫面](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 檢查儲存體帳戶的加密模型，請呼叫[new-azstorageaccount](/powershell/module/az.storage/get-azstorageaccount)命令，然後檢查帳戶的**keysource filename**屬性。

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

如果**keysource filename**屬性的值是 `Microsoft.Storage`，則會使用 Microsoft 管理的金鑰來加密帳戶。 如果**keysource filename**屬性的值是 `Microsoft.Keyvault`，則會使用客戶管理的金鑰來加密帳戶。

# <a name="azure-clitabcli"></a>[Azure CLI](#tab/cli)

若要使用 Azure CLI 來檢查儲存體帳戶的加密模型，請呼叫[az storage account show](/cli/azure/storage/account#az-storage-account-show)命令，然後檢查帳戶的**keysource filename**屬性。

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

如果**keysource filename**屬性的值是 `Microsoft.Storage`，則會使用 Microsoft 管理的金鑰來加密帳戶。 如果**keysource filename**屬性的值是 `Microsoft.Keyvault`，則會使用客戶管理的金鑰來加密帳戶。

---

## <a name="next-steps"></a>後續步驟

[待用資料的 Azure 儲存體加密](storage-service-encryption.md)
