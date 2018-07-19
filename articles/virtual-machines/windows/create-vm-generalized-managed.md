---
title: 在 Azure 中從受控映像建立 VM | Microsoft Docs
description: 在 Resource Manager 部署模型中使用 Azure PowerShell 或 Azure 入口網站，從一般化受控映像建立 Windows 虛擬機器。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: cynthn
ms.openlocfilehash: 6baf784068b1fba0c35d2848b8d2dda4f1064a2d
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867975"
---
# <a name="create-a-vm-from-a-managed-image"></a>從受控映像建立 VM

您可以使用 PowerShell 或 Azure 入口網站，從受控 VM 映像建立多個 VM。 受控 VM 映像包含建立 VM 所需的資訊，包括 OS 和資料磁碟。 組成映像的 VHD (包括 OS 磁碟和任何資料磁碟) 會儲存為受控磁碟。 

您必須已經[建立受控 VM 映像](capture-image-resource.md)，才能用來建立新的 VM。 

## <a name="use-the-portal"></a>使用入口網站

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
2. 在左側功能表中，選取 [所有資源]。 您可以依 [類型] 排序資源，以輕鬆找到您的映像。
3. 從清單選取您要使用的映像。 映像的 [概觀] 頁面隨即開啟。
4. 按一下功能表中的 [+ 建立 VM]。
5. 輸入虛擬機器資訊。 在此輸入的使用者名稱和密碼用於登入虛擬機器。 完成時，按一下 [確定]。 您可以在現有的資源群組中建立新的 VM，或選擇 [建立新項目] 來建立用於儲存 VM 的新資源群組。
6. 選取 VM 的大小。 若要查看更多大小，請選取 [檢視全部] 或變更 [支援的磁碟類型] 篩選條件。 
7. 在 [設定] 底下，視需要變更，然後按一下 [確定]。 
8. 在 [摘要] 頁面上，您應該會看到您的映像名稱列為**私人映像**。 按一下 [確定] 以開始虛擬機器部署。


## <a name="use-powershell"></a>使用 PowerShell

您可以使用 PowerShell，使用對於 [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm) Cmdlet 設定的簡化參數從映像建立 VM。 映像必須位在與您想要用來建立 VM 映像的同一個資源群組中。

此範例需要 AzureRM 模組 5.6.0 版或更新版本。 執行 ` Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。

New-AzureRmVm 設定的簡化參數只會要求您提供名稱、資源群組和映像名稱，以便從映像建立 VM，但是會使用 **-Name** 參數的值做為自動建立的所有資源的名稱。 在此範例中，會對每個資源提供更詳細的名稱，但會讓 Cmdlet 自動建立這些資源。 您也可以事先建立資源，例如虛擬網路，並將名稱傳遞至 Cmdlet。 如果可藉由名稱找到資源，即會使用現有的資源。

下列範例會在 *myResourceGroup* 資源群組中從名稱為 *myImage* 的映像建立名稱為 *myVMFromImage* 的 VM。 


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```



## <a name="next-steps"></a>後續步驟
若要使用 PowerShell 來管理您的新虛擬機器，請參閱[使用 Azure PowerShell 模組來建立和管理 Windows VM](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

