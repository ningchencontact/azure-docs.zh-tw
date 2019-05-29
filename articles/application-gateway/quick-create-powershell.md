---
title: 快速入門 - 使用 Azure 應用程式閘道引導網路流量 - Azure PowerShell | Microsoft Docs
description: 深入了解如何使用 Azure PowerShell 建立 Azure 應用程式閘道，該應用程式閘道會將網路流量導向至後端集區中的虛擬機器。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/11/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: a387d6fd8b99706e266ae4c2d9a51f823b0582e4
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65539312"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-powershell"></a>快速入門：使用 Azure 應用程式閘道引導網路流量 - Azure PowerShell

本快速入門說明如何使用 Azure PowerShell 快速建立應用程式閘道。  建立應用程式閘道之後，您要加以測試，確定它可正常運作。 您會使用 Azure 應用程式閘道，將接聽程式指派給連接埠、建立規則，以及將資源新增至後端集區，來將應用程式網路流量導向至特定資源。 為了簡單起見，本文使用簡單的設定，包括公用前端 IP、在此應用程式閘道上裝載單一網站的基本接聽程式、用於後端集區的兩部虛擬機器，以及基本的要求路由規則。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>必要條件

### <a name="azure-powershell-module"></a>Azure PowerShell 模組

如果您選擇在本機安裝和使用 Azure PowerShell，在執行本教學課程時，您必須使用 Azure PowerShell 模組 1.0.0 版或更新版本。

1. 若要尋找版本，請執行 `Get-Module -ListAvailable Az`。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 
2. 若要建立與 Azure 的連線，請執行 `Login-AzAccount`。

### <a name="resource-group"></a>資源群組

在 Azure 中，您可以將相關資源配置到資源群組。 您可以使用現有的資源群組，或建立一個新的群組。 在此範例中，我們將使用 [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup) Cmdlet 建立新的資源群組，如下所示： 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

### <a name="required-network-resources"></a>必要的網路資源

Azure 需要虛擬網路才能在您所建立的資源之間進行通訊。  應用程式閘道子網路只能包含應用程式閘道。 不允許任何其他資源。  您可以為應用程式閘道建立新的子網路，或使用現有的子網路。 在此範例中您會建立兩個子網路：一個用於應用程式閘道，另一個用於後端伺服器。 您可以根據自己的使用案例，將應用程式閘道的前端 IP 設定為「公用」或「私人」。 在此範例中，我們會選擇公用前端 IP。

1. 呼叫 [New-AzVirtualNetworkSubnetConfig](/powershell/module/Az.network/new-Azvirtualnetworksubnetconfig) 以建立子網路組態。
2. 呼叫 [New-AzVirtualNetwork](/powershell/module/Az.network/new-Azvirtualnetwork) 以使用子網路組態建立虛擬網路。 
3. 呼叫 [New-AzPublicIpAddress](/powershell/module/Az.network/new-Azpublicipaddress) 以建立公用 IP 位址。 

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```
### <a name="backend-servers"></a>後端伺服器

後端可以包含 NIC、虛擬機器擴展集、公用 IP、內部 IP、完整網域名稱 (FQDN)，以及多租用戶後端，例如 Azure App Service。 在此範例中，您會建立兩個虛擬機器，供 Azure 作為應用程式閘道的後端伺服器。 您也可以在虛擬機器上安裝 IIS，以確認 Azure 已成功建立應用程式閘道。

#### <a name="create-two-virtual-machines"></a>建立兩部虛擬機器

1. 使用 [New-AzNetworkInterface](/powershell/module/Az.network/new-Aznetworkinterface) 建立網路介面。 
2. 使用 [New-AzVMConfig](/powershell/module/Az.compute/new-Azvmconfig) 建立虛擬機器設定。
3. 使用 [New-AzVM](/powershell/module/Az.compute/new-Azvm) 來建立虛擬機器。

當您執行下列程式碼範例以建立虛擬機器時，Azure 會提示您輸入認證。 輸入「azureuser」  作為使用者名稱，並且輸入「Azure123456!」  作為密碼：
    
```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -SubnetId $subnet.Id
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostics `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="create-an-application-gateway"></a>建立應用程式閘道

### <a name="create-the-ip-configurations-and-frontend-port"></a>建立 IP 設定與前端連接埠

1. 使用 [New-AzApplicationGatewayIPConfiguration](/powershell/module/Az.network/new-Azapplicationgatewayipconfiguration)，建立讓您先前建立的子網路與應用程式閘道產生關聯的組態。 
2. 使用 [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/Az.network/new-Azapplicationgatewayfrontendipconfig)，建立會將您先前建立的公用 IP 位址指派給應用程式閘道的組態。 
3. 使用 [New-AzApplicationGatewayFrontendPort](/powershell/module/Az.network/new-Azapplicationgatewayfrontendport) 指派連接埠 80，用以存取應用程式閘道。

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool"></a>建立後端集區

1. 使用 [New-AzApplicationGatewayBackendAddressPool](/powershell/module/Az.network/new-Azapplicationgatewaybackendaddresspool) 建立應用程式閘道的後端集區。 
2. 使用 [New-AzApplicationGatewayBackendHttpSetting](/powershell/module/Az.network/new-Azapplicationgatewaybackendhttpsetting) 設定後端集區的設定。

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool `
  -BackendIPAddresses $address1.ipconfigurations[0].privateipaddress, $address2.ipconfigurations[0].privateipaddress
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-add-a-rule"></a>建立接聽程式並新增規則

Azure 需要接聽程式才能讓應用程式閘道將流量適當地路由到後端集區。 Azure 也需要接聽程式的規則，以得知要對傳入的流量使用哪個後端集區。 

1. 使用 [New-AzApplicationGatewayHttpListener](/powershell/module/Az.network/new-Azapplicationgatewayhttplistener) 以及您先前建立的前端設定和前端連接埠，來建立接聽程式。 
2. 使用 [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/Az.network/new-Azapplicationgatewayrequestroutingrule) 建立名為 rule1  的規則。 

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>建立應用程式閘道

您已建立必要的支援資源，接著請建立應用程式閘道：

1. 使用 [New-AzApplicationGatewaySku](/powershell/module/Az.network/new-Azapplicationgatewaysku) 指定應用程式閘道的參數。
2. 使用 [New-AzApplicationGateway](/powershell/module/Az.network/new-Azapplicationgateway) 建立應用程式閘道。

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>測試應用程式閘道

雖然不需要 IIS 即可建立應用程式閘道，但您仍會在本快速入門中加以安裝，以確認 Azure 是否已成功建立應用程式閘道。 使用 IIS 測試應用程式閘道：

1. 執行 [Get-AzPublicIPAddress](/powershell/module/Az.network/get-Azpublicipaddress) 以取得應用程式閘道的公用 IP 位址。 
2. 將公用 IP 位址複製並貼到瀏覽器的網址列中。 當您重新整理瀏覽器時，應該會看到虛擬機器的名稱。 有效的回應會確認應用程式閘道已成功建立，並能與後端順利連線。

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![測試應用程式閘道](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>清除資源

當您不再需要先前為應用程式閘道建立的資源時，請移除資源群組。 藉由移除資源群組，您也可以移除應用程式閘道及其所有相關資源。 

若要移除資源群組，請呼叫 [Remove-AzResourceGroup](/powershell/module/Az.resources/remove-Azresourcegroup) Cmdlet，如下所示：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure PowerShell 以應用程式閘道管理網路流量](./tutorial-manage-web-traffic-powershell.md)

