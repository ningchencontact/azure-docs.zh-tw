---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 660bbf50e1a8ae73bd7bbe1f7c42691ed62d276a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552960"
---
如果您還沒有 VNet，可以使用 PowerShell 快速建立一個。 您也可以使用 Azure 入口網站建立虛擬網路。

* 請務必確認您所建立的 VNet 位址空間不會與任何其他想要連線的 Vnet 位址範圍重疊，或是不會與內部部署網路的位址空間重疊。 
* 如果您已經有 VNet，請確認它符合所需的準則，而且沒有虛擬網路閘道。

按一下本文中的 [試試看] 來開啟 PowerShell 主控台，即可輕鬆建立您的 VNet。 調整值，然後複製命令並貼到主控台視窗。

### <a name="create-a-resource-group"></a>建立資源群組

調整 PowerShell 命令，然後建立資源群組。

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>建立 VNet

調整 PowerShell 命令，以建立適用於您環境的 VNet。

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```