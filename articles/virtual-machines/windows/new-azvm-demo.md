---
title: 使用 Azure Cloud Shell 中簡化的 New-AzVM Cmdlet 建立 Windows VM | Microsoft Docs
description: 快速了解如何在 Azure Cloud Shell 中使用簡化的 New-AzVM Cmdlet 來建立 Windows 虛擬機器。
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 32862d06cfa6a9a8dd9b99459362ec53fd88cec2
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978932"
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azvm-cmdlet-in-cloud-shell"></a>在 Cloud Shell 中使用簡化的 New-AzVM Cmdlet 來建立 Windows 虛擬機器 

[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm?view=azurermps-6.8.1) Cmdlet 已新增一組簡化的參數，用於使用 PowerShell 建立新的 VM。 本主題說明如何在 Azure Cloud Shell 中使用 PowerShell 搭配預先安裝的最新版本 New-AzureVM Cmdlet 來建立新的 VM。 我們將使用會使用智慧型預設值來自動建立所有必要資源的簡化參數集。 

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]


## <a name="create-the-vm"></a>建立 VM

您可以使用 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm?view=azurermps-6.8.1) Cmdlet 來建立虛擬機器，其中包括使用來自於 Azure Marketplace 的 Windows Server 2016 Datacenter 映像等智慧型預設值。 您可以使用 New-AzVM 僅搭配 **-Name** 參數，它將會使用該值做為所有資源名稱。 在此範例中，我們將 **-Name** 參數設為 *myVM*。 

請確定已在 Cloud Shell 中選取了 **PowerShell**，然後鍵入：

```azurepowershell-interactive
New-AzVm -Name myVM
```

系統會要求您建立 VM 的使用者名稱和密碼，稍後在本主題中當您連線到 VM 時會用到。 密碼長度必須是 12-123 個字元，且符合下列四個複雜性需求的其中三項：1 個小寫字元、1 個大寫字元、1 個數字和 1 個特殊字元。

只要花一分鐘即可建立虛擬機器和相關聯的資源。 完成時，您可以看到所有使用 [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) Cmdlet 建立的資源。

```azurepowershell-interactive
Get-AzResource `
    -ResourceGroupName myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>連接至 VM

完成部署之後，建立與虛擬機器的遠端桌面連線。

使用 [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) 命令，以傳回虛擬機器的公用 IP 位址。 記下此 IP 位址。

```azurepowershell-interactive
Get-AzPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

在您的本機電腦上開啟命令提示字元，並使用 **mstsc** 命令以啟動遠端桌面工作階段與新的虛擬機器。 以虛擬機器的 IP 位址取代 &lt;publicIPAddress&gt;。 出現提示時，請輸入建立 VM 時指定的使用者名稱和密碼。

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>指定不同的資源名稱

您也可以為資源提供更具描述性的名稱，並且仍自動建立。 以下是我們為新 VM 命名多個資源的範例，包括新的資源群組。

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 3389
```

## <a name="clean-up-resources"></a>清除資源

當不再需要時，您可以使用 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) 命令來移除資源群組、VM 及所有相關資源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myVM
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本主題中，您已使用 New-AzVM 部署了簡單的虛擬機器，並透過 RDP 連線到該虛擬機器。 若要深入了解 Azure 虛擬機器，請繼續 Windows VM 的教學課程。

> [!div class="nextstepaction"]
> [Azure Windows 虛擬機器教學課程](./tutorial-manage-vm.md)
