---
title: PowerShell 指令碼：將市集映像新增至 Azure 實驗室服務的自訂實驗室中 | Microsoft Docs
description: 此 PowerShell 指令碼會將市集映像新增至 Azure 實驗室服務的自訂實驗室中。
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: 64d168c132edce4ecd128b795fbfa5ab2607cb19
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-custom-lab"></a>使用 PowerShell 將市集映像新增至自訂實驗室

此範例 PowerShell 指令碼會將市集映像新增至 Azure 實驗室服務的自訂實驗室中。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>先決條件
* **自訂實驗室**。 指令碼需要您擁有現有的自訂實驗室。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a custom lab")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| 命令 | 注意 |
|---|---|
| [Find-AzureRmResource](/module/azurerm.resources/find-azurermresource) | 根據指定的參數搜尋資源。 |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | 取得資源。 |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | 修改資源。 |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | 建立資源。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在 [Azure 實驗室服務 PowerShell 範例](../samples-powershell.md)中找到其他的 Azure 實驗室服務 PowerShell 指令碼範例。