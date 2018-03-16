---
title: "Azure CLI 指令碼範例 - 建立 Batch 帳戶 - 使用者訂用帳戶 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 在使用者訂用帳戶模式中建立 Batch 帳戶"
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: 0923512d62a1efe31e08377b6c074df2e0618552
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>CLI 範例：在使用者訂用帳戶模式中建立 Batch 帳戶

此指令碼會在使用者訂用帳戶模式中建立 Azure Batch 帳戶。 將計算節點配置到您的訂用帳戶的帳戶必須透過 Azure Active Directory 權杖驗證。 配置的計算節點會計入您訂用帳戶的 vCPU (核心) 配額。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0.20 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

## <a name="example-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>清除部署

執行下列命令來移除資源群組及其所有相關聯的資源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [az 角色指派建立](/cli/azure/role#az_role_assignment_create) | 為使用者、群組或服務主體建立新的角色指派。 |
| [az group create](/cli/azure/group#az_group_create) | 建立用來存放所有資源的資源群組。 |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_create) | 建立金鑰保存庫。 |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_set_policy) | 更新指定的 Key Vault 的安全性原則。 |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | 建立 Batch 帳戶。  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | 對指定的 Batch 帳戶驗證以進行進一步的 CLI 互動。  |
| [az group delete](/cli/azure/group#az_group_delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。
