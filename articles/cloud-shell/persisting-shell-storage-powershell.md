---
title: 在 Azure Cloud Shell 的 PowerShell 中保存檔案 (預覽) | Microsoft Docs
description: 逐步解說 Azure Cloud Shell 如何保存檔案。
services: azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: damaerte
ms.openlocfilehash: b87c4a408393e4ae341898e8cfa23e9acbcb4fc2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
ms.locfileid: "32154393"
---
[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-powershell-in-azure-cloud-shell-preview-works"></a>Azure Cloud Shell 中的 PowerShell (預覽) 如何運作
Azure Cloud Shell 中的 PowerShell (預覽) 透過下列方法保存檔案： 
* 在您的 `$Home` 目錄中，將指定的 Azure 檔案共用掛接為 `clouddrive`，以便直接與檔案共用互動。

## <a name="list-clouddrive-azure-file-shares"></a>列出 `clouddrive` Azure 檔案共用
`Get-CloudDrive` 命令會擷取 Cloud Shell 中 `clouddrive` 目前所掛接的 Azure 檔案共用資訊。 <br>
![Running Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-clouddrive"></a>卸載 `clouddrive`
您可以隨時將掛接至 Cloud Shell 的 Azure 檔案共用卸載。 如果已移除 Azure 檔案共用，則系統會在下一個工作階段提示您建立和掛接新的 Azure 檔案共用。

`Dismount-CloudDrive` 命令會從目前儲存體帳戶卸載 Azure 檔案共用。 卸載 `clouddrive` 會終止目前工作階段。 系統會提示使用者在下一個工作階段期間建立和掛接新的 Azure 檔案共用。
![執行 Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>後續步驟
[PowerShell 的快速入門](quickstart-powershell.md) <br>
[了解 Azure 檔案](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[了解儲存體標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>