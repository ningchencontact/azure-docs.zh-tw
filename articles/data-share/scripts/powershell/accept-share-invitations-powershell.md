---
title: PowerShell 指令碼：接受來自 Azure 資料共用的邀請 |Microsoft Docs
description: 此 PowerShell 腳本會接受來自現有資料共用的邀請。
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 9ef35e0865a7c5bbfb4accc058a8d63bb6f1e80b
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70243070"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>使用 PowerShell 接受資料共用邀請

此 PowerShell 腳本會接受傳送給取用者的邀請。

## <a name="sample-script"></a>範例指令碼
```powershell
#List invitations sent to a consumer
Get-AzDataShareInvitation

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$invitationId = "<Invitation id>"

#Accept a specific invitation by creating a share subscription
New-AzDataShareSubscription -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName -InvitationId $invitationId

```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| 命令 | 注意 |
|---|---|
| [AzDataShareInvitation](/powershell/module/az.resources/get-azdatashareinvitation) | 取得並列出已傳送的資料共用邀請。 |
| [新增-AzDataShareSubscription](/powershell/module/az.resources/get-azdatashareinvitation) | 建立資料共用訂用帳戶。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在[Azure 資料共用 powershell 範例](../../samples-powershell.md)中找到其他 Azure 資料共用 powershell 腳本範例。

