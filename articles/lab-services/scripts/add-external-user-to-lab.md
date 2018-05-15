---
title: PowerShell 指令碼：將外部使用者新增至 Azure 實驗室服務的自訂實驗室中 | Microsoft Docs
description: 這個 PowerShell 指令碼會將外部使用者新增至 Azure 實驗室服務中的自訂實驗室。
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
ms.openlocfilehash: b089067a889f0ffd3b317fcc3f0784d176473b91
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-add-an-external-user-to-a-custom-lab"></a>使用 PowerShell 將外部使用者新增至自訂實驗室

此範例 PowerShell 指令碼會將外部使用者新增至 Azure 實驗室服務中的自訂實驗室。 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>先決條件
* **自訂實驗室**。 指令碼需要您擁有現有的自訂實驗室。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a custom lab")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| 命令 | 注意 |
|---|---|
| [Get-AzureRmADUser](/powershell/module/azurerm.resources/get-azurermaduser) | 從 Azure Active Directory 擷取使用者物件。 |
| [New-AzureRmRoleAssignment](/module/azurerm.resources/new-azurermroleassignment) | 在指定的範圍中，將指定的角色指派給指定的主體。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在 [Azure 實驗室服務 PowerShell 範例](../samples-powershell.md)中找到其他的 Azure 實驗室服務 PowerShell 指令碼範例。