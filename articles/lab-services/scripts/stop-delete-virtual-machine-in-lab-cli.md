---
title: Azure CLI 指令碼範例 - 停止和刪除自訂實驗室中的虛擬機器 | Microsoft Docs
description: 此 Azure CLI 指令碼會停止和刪除自訂實驗室中的虛擬機器。
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
ms.openlocfilehash: 0da99bb5e029f4d1c6be74d09ad11c6017121a7c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-custom-lab"></a>使用 Azure CLI 停止和刪除自訂實驗室中的虛擬機器

此 Azure CLI 指令碼會停止和刪除自訂實驗室中的虛擬機器 (VM)。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a custom lab")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令：

| 命令 | 注意 |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-stop) | 停止自訂實驗室中的虛擬機器 (VM)。 此作業可能需要一些時間來完成。 |
| [az lab vm delete](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-delete) | 刪除自訂實驗室中的虛擬機器 (VM)。 此作業可能需要一些時間來完成。 |


## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。

您可以在 [Azure 實驗室服務 CLI 範例](../samples-cli.md)中找到其他的 Azure 實驗室服務 PowerShell 指令碼範例。