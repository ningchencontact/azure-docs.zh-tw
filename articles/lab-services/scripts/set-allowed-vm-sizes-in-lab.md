---
title: PowerShell 指令碼：設定 Azure 實驗室服務中允許的 VM 大小 |Microsoft Docs
description: 這個 PowerShell 指令碼會在 Azure 實驗室服務中設定允許的 VM 大小。
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
ms.openlocfilehash: 0c82e304d3e3d8df1206c7c05883399b74229af7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160539"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>使用 PowerShell 在 Azure 實驗室服務中設定允許的 VM 大小

此範例 PowerShell 指令碼會在 Azure 實驗室服務中設定允許的虛擬機器 (VM) 大小。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>必要條件
* **實驗室**。 指令碼需要您擁有現有的實驗室。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| 命令 | 注意 |
|---|---|
| Find-AzResource | 根據指定的參數搜尋資源。 |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 取得資源。 |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | 修改資源。 |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | 建立資源。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在 [Azure 實驗室服務 PowerShell 範例](../samples-powershell.md)中找到其他的 Azure 實驗室服務 PowerShell 指令碼範例。
