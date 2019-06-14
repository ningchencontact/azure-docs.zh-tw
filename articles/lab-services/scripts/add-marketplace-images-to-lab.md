---
title: PowerShell 指令碼：在 Azure DevTest Labs 中對實驗室新增 marketplace 映像 |Microsoft Docs
description: 這個 PowerShell 指令碼會將市集映像新增至 Azure DevTest Labs 的實驗室中。
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
ms.openlocfilehash: e099a29a198d43bf8d00487ab45e2648479aedbe
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160590"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>使用 PowerShell 將市集映像新增至 Azure DevTest Labs 的實驗室中

這個範例 PowerShell 指令碼會將市集映像新增至 Azure DevTest Labs 的實驗室中。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>必要條件
* **實驗室**。 指令碼需要您擁有現有的實驗室。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

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
