---
title: "在 Azure 中建立虛擬網路 - PowerShell | Microsoft Docs"
description: "快速了解如何使用 PowerShell 來建立虛擬網路。 虛擬網路可讓許多類型的 Azure 資源互相進行私密通訊。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: dd8203763eb6abd19e2b3483636dc4d80f7effdf
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-virtual-network-using-powershell"></a>使用 PowerShell 建立虛擬網路

在本文中，您將了解如何建立虛擬網路。 建立虛擬網路之後，您需將兩部虛擬機器部署到虛擬網路中，以在兩者間測試私人網路通訊。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

如果您選擇在本機安裝和使用 PowerShell，本文會要求使用 AzureRM PowerShell 模組版本 5.1.1 或更新版本。 若要尋找已安裝的版本，請執行 ` Get-Module -ListAvailable AzureRM`。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Login-AzureRmAccount` 以建立與 Azure 的連線。

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup) 建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。 所有 Azure 資源都會建立在 Azure 位置 (或區域) 內。

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-virtual-network"></a>建立虛擬網路

使用 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) 建立虛擬網路。 以下範例會在 *EastUS* 位置建立名為 *myVirtualNetwork* 的預設虛擬網路：

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/24
```

所有虛擬網路都有一或多個指派的位址首碼。 位址空間需以 CIDR 標記法指定。 位址空間 10.0.0.0/24 包含 10.0.0.0-10.0.0.254。 虛擬網路會包含零個或多個子網路。 資源會部署至虛擬網路內的子網路中。 

請使用 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) 來建立子網路設定。 所有子網路都有存在於虛擬網路位址首碼內的位址首碼。 在此範例中，會建立位址首碼與虛擬網路位址首碼相同的子網路設定：

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

雖然子網路位址首碼包含 10.0.0.0-10.0.0.254，但可供使用的只有位址 10.0.0.4-10.0.0.254，因為 Azure 會保留每個子網路中的前四個位址 (0-3) 和最後一個位址。 由於子網路位址首碼與虛擬網路位址首碼相同，因此虛擬網路中只能有一個子網路。

請使用 [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) 將子網路設定寫入至虛擬網路，以建立子網路：

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="test-network-communication"></a>測試網路通訊

虛擬網路可讓數種類型的 Azure 資源互相進行私密通訊。 其中一種您可以部署到虛擬網路中的資源類型是虛擬機器。 在虛擬網路中建立兩部虛擬機器，因此您可以在稍後步驟中驗證它們之間的私密通訊。

### <a name="create-virtual-machines"></a>建立虛擬機器

請使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 來建立虛擬機器。 執行此步驟時，系統會提示您輸入認證。 您輸入的值會設定為虛擬機器的使用者名稱和密碼。 虛擬機器的建立位置必須與虛擬網路所的位置相同。 虛擬機器不一定要與虛擬網路屬於相同資源群組，雖然在本文中兩者屬於相同資源群組。 `-AsJob` 參數可讓命令在背景執行，因此您可以繼續進行下一個工作。

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

將會傳回類似以下範例輸出的輸出，而 Azure 則會開始在背景建立虛擬機器。

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

建立虛擬機器時，Azure DHCP 會自動將 10.0.0.4 指派給虛擬機器，因為這是 *default* 子網路中第一個可用的位址。

建立第二部虛擬機器。 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```
建立虛擬機器需要幾分鐘的時間。 建立之後，Azure 會傳回有關所建立虛擬機器的輸出。 雖然傳回的輸出中不會包含所指派的位址，但 Azure 已將 *10.0.0.5* 指派給 *myVm2* 虛擬機器，因為這是子網路中下一個可用的位址。

### <a name="connect-to-a-virtual-machine"></a>連接到虛擬機器

使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 命令，以傳回虛擬機器的公用 IP 位址。 Azure 預設會為每部虛擬機器指派一個公用、可透過網際網路路由傳送的 IP 位址。 將會從[指派給每個 Azure 區域的位址集區](https://www.microsoft.com/download/details.aspx?id=41653) \(英文\) 指派公用 IP 位址給虛擬機器。 雖然 Azure 知道哪個公用 IP 位址已指派給虛擬機器，但在虛擬機器中執行的作業系統並不會察覺指派給它的任何公用 IP 位址。 以下範例會傳回 *myVm1* 虛擬機器的公用 IP 位址：

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -Name myVm1 -ResourceGroupName myResourceGroup | Select IpAddress
```

請從您的本機電腦使用下列命令，來建立與 *myVm1* 虛擬機器的遠端桌面工作階段。 以上一個命令傳回的 IP 位址取代 `<publicIpAddress>`。

```
mstsc /v:<publicIpAddress>
```

將會建立一個「遠端桌面通訊協定」(.rdp) 檔案、下載至您的電腦並開啟。 輸入您在建立虛擬機器時指定的使用者名稱和密碼，然後按一下 [確定]。 您可能會在登入過程中收到憑證警告。 按一下 [是] 或 [繼續] 以繼續進行連線。

### <a name="validate-communication"></a>驗證通訊

嘗試針對 Windows 虛擬機器執行 Ping 會失敗，因為預設不允許 Ping 穿過 Windows 防火牆。 若要允許針對 *myVm1* 執行 Ping，請從命令提示字元中輸入下列命令：

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

若要驗證與 *myVm2* 的通訊，請從 *myVm1* 虛擬機器上的命令提示字元中輸入下列命令。 請提供您建立虛擬機器時所使用的認證，然後完成連線：

```
mstsc /v:myVm2
```

遠端桌面連線成功，因為兩部虛擬機器都已從 *default* 子網路獲指派私人 IP 位址，並且預設穿過 Windows 防火牆開啟了遠端桌面。 您能夠依據主機名稱連線至 *myVm2*，因為 Azure 會自動為虛擬網路內的所有主機提供 DNS 名稱解析。 在命令提示字元中，從 *myVm2* 針對 *myVm1* 執行 Ping。

```
ping myvm1
```

Ping 執行成功，因為您在上一個步驟中允許它穿過 *myVm1* 虛擬機器上的 Windows 防火牆。 若要確認對網際網路的輸出通訊，請輸入下列命令：

```
ping bing.com
```

您會從 bing.com 收到四個回覆。根據預設，虛擬網路中的所有虛擬機器都能對網際網路進行輸出通訊。

結束遠端桌面工作階段。 

## <a name="clean-up-resources"></a>清除資源

不再需要資源群組時，您可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令來移除資源群組及其包含的所有資源：

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

在本文中，您已部署含有一個子網路的預設虛擬網路。 若要了解如何建立含有多個子網路的自訂虛擬網路，請繼續進行建立自訂虛擬網路的教學課程。

> [!div class="nextstepaction"]
> [建立自訂虛擬網路](virtual-networks-create-vnet-arm-pportal.md#powershell)
