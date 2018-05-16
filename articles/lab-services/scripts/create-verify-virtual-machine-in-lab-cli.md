---
title: Azure CLI 指令碼範例 - 在自訂實驗室中建立和驗證虛擬機器 | Microsoft Docs
description: 此 Azure CLI 指令碼會在自訂實驗室中建立虛擬機器，並確認它可正常運作。
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2018
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: bd564cda7b4d5c2158b8499b48b8faa68309b461
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-custom-lab"></a>使用 Azure CLI 在自訂實驗室中建立並驗證虛擬機器的可用性

此 Azure CLI 指令碼會在自訂實驗室中建立虛擬機器 (VM)。 使用 SSH 驗證從 Marketplace 映像建立 VM。 然後指令碼則會驗證 VM 是否已可供使用。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/create-verify-virtual-machine-in-lab/create-verify-virtual-machine-in-lab.sh "Create and verify availability of a VM")]

## <a name="clean-up-deployment"></a>清除部署 

執行下列命令來移除資源群組、VM 和所有相關資源。

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令：

| 命令 | 注意 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 建立用來存放所有資源的資源群組。 |
| [az lab vm create ](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-create) | 在自訂實驗室中建立虛擬機器 (VM)。 |
| [az lab vm show](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-show) | 顯示自訂實驗室中的 VM 狀態。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。

您可以在 [Azure 實驗室服務 CLI 範例](../samples-cli.md)中找到其他的 Azure 實驗室服務 PowerShell 指令碼範例。