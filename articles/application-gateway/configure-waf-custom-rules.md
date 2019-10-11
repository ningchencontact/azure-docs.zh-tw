---
title: 使用 Azure PowerShell 設定 web 應用程式防火牆 v2 自訂規則
description: 瞭解如何使用 Azure PowerShell 來設定 web 應用程式防火牆 v2 自訂規則
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 9e50b47e22f5760c213cd0cafad82ecca592dec8
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263738"
---
# <a name="configure-web-application-firewall-v2-custom-rules-by-using-azure-powershell"></a>使用 Azure PowerShell 設定 web 應用程式防火牆 v2 自訂規則

<!--- If you make any changes to the PowerShell in this article, also make the change in the corresponding Sample file: azure-docs-powershell-samples/application-gateway/waf-rules/waf-custom-rules.ps1 --->

透過自訂規則，您可以建立自己的規則，其會針對通過 web 應用程式防火牆（WAF）的每個要求進行評估。 這些規則的優先順序高於受管理規則集的其餘規則。 若要允許完全自訂，自訂規則具有動作（允許或封鎖）、比對條件和運算子。

本文會建立使用自訂規則 Azure 應用程式閘道 WAF v2。 如果要求標頭包含使用者代理程式 *evilbot*，則自訂規則會封鎖流量。

若要查看更多自訂規則範例，請參閱[建立和使用自訂 web 應用程式防火牆規則](create-custom-waf-rules.md)。

若要在可複製、貼上及執行的一個連續腳本中執行本文中的 Azure PowerShell 程式碼，請參閱[Azure 應用程式閘道 PowerShell 範例](powershell-samples.md)。

## <a name="prerequisites"></a>必要條件
* [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* 您需要 Azure PowerShell 模組。 如果您選擇在本機安裝和使用 Azure PowerShell，此腳本需要 Azure PowerShell 模組版本2.1.0 或更新版本。 請執行下列動作：

  1. 若要尋找版本，請執行 `Get-Module -ListAvailable Az`。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。
  2. 若要建立與 Azure 的連線，請執行 `Connect-AzAccount`。

## <a name="example-script"></a>範例指令碼

### <a name="set-up-variables"></a>設定變數

執行下列程式碼：

```azurepowershell
$rgname = "CustomRulesTest"

$location = "East US"

$appgwName = "WAFCustomRules"
```

### <a name="create-a-resource-group"></a>建立資源群組

執行下列程式碼：

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
```

### <a name="create-a-virtual-network"></a>建立虛擬網路

執行下列程式碼：

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"

$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"

$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-vip"></a>建立靜態公用 VIP

執行下列程式碼：

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-a-pool-and-front-end-port"></a>建立集區和前端埠

執行下列程式碼：

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet

$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip

$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 80
```

### <a name="create-a-listener-http-setting-rule-and-autoscale"></a>建立接聽程式、HTTP 設定、規則和自動調整

執行下列程式碼：

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled

$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3

$sku = New-AzApplicationGatewaySku -Name WAF_v2 -Tier WAF_v2
```

### <a name="create-the-custom-rule-and-apply-it-to-waf-policy"></a>建立自訂規則，並將它套用至 WAF 原則

執行下列程式碼：

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestHeaders -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "evilbot" -Transform Lowercase -NegationCondition $False  

$rule = New-AzApplicationGatewayFirewallCustomRule -Name blockEvilBot -Priority 2 -RuleType MatchRule -MatchCondition $condition -Action Block

$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafPolicy -ResourceGroup $rgname -Location $location -CustomRule $rule

$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="create-an-application-gateway"></a>建立應用程式閘道

執行下列程式碼：

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname `
   -Location $location -BackendAddressPools $pool `
   -BackendHttpSettingsCollection  $poolSetting01 `
   -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
   -FrontendPorts $fp01 -HttpListeners $listener01 `
   -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig `
   -WebApplicationFirewallConfig $wafConfig `
   -FirewallPolicy $wafPolicy
```

## <a name="next-steps"></a>後續步驟

[深入瞭解 web 應用程式防火牆](waf-overview.md)
