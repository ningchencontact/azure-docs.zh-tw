---
title: PowerShell 指令碼：從 Azure 實驗室服務中的 VHD 檔案建立自訂映像 |Microsoft Docs
description: 此 PowerShell 指令碼會從 Azure 實驗室服務中的 VHD 檔案建立自訂映像。
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
ms.openlocfilehash: 2d0cc4012adf2c17b2f7a2e769f2d666b158a8c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160571"
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>使用 PowerShell 從 Azure 實驗室服務中的 VHD 檔案建立自訂映像

此範例 PowerShell 指令碼會從 Azure 實驗室服務中的 VHD 檔案建立自訂映像

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>必要條件
* **實驗室**。 指令碼需要您擁有現有的實驗室。 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| 命令 | 注意 |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 取得資源。 |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | 取得 Azure 儲存體帳戶的存取金鑰。 |
| [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) | 將 Azure 部署加入資源群組。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在 [Azure 實驗室服務 PowerShell 範例](../samples-powershell.md)中找到其他的 Azure 實驗室服務 PowerShell 指令碼範例。
