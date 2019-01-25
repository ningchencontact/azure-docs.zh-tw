---
title: 教學課程：建立具有保留 IP 位址的自動調整規模、區域備援應用程式閘道 - Azure PowerShell
description: 在本教學課程中，您將了解如何使用 Azure PowerShell 建立具有保留 IP 位址且可自動調整規模的區域備援應用程式閘道。
services: application-gateway
author: amitsriva
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: dd6cc65fca98bc435a8cfea575ba10e3cff376be
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2019
ms.locfileid: "54424671"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>教學課程：建立改善 Web 應用程式存取的應用程式閘道

如果您是 IT 系統管理員，並且想改善 Web 應用程式的存取，您可以最佳化應用程式閘道，以根據客戶需求並跨越多個可用性區域來調整規模。 本教學課程可協助您設定 Azure 應用程式閘道功能，以完成：自動調整、區域備援及保留的 VIP (靜態 IP)。 若要解決此問題，您將使用 Azure PowerShell Cmdlet 和 Azure Resource Manager 部署模型。

> [!IMPORTANT] 
> 自動調整規模和區域備援應用程式閘道 SKU 目前為公開預覽版。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立自動調整虛擬網路
> * 建立保留公用 IP
> * 設定應用程式閘道基礎結構
> * 指定自動調整
> * 建立應用程式閘道
> * 測試應用程式閘道

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

進行本教學課程時，您必須在本機執行 Azure PowerShell。 您必須安裝 Azure PowerShell 模組 6.9.0 版或更新版本。 執行 `Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)。 驗證 PowerShell 版本之後，請執行 `Login-AzureRmAccount` 以建立與 Azure 的連線。

## <a name="sign-in-to-azure"></a>登入 Azure

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>建立資源群組
在其中一個可用的位置建立資源群組。

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzureRmResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>建立虛擬網路

建立具有一個自動調整應用程式閘道專用子網路的虛擬網路。 目前在每個專用子網路只可以部署一個自動調整應用程式閘道。

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzureRmVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>建立保留公用 IP

將 PublicIPAddress 的配置方法指定為 [靜態]。 自動調整應用程式閘道 VIP 只能是靜態。 不支援動態 IP。 只支援標準 PublicIpAddress SKU。

```azurepowershell
#Create static public IP
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>擷取詳細資料

擷取本機物件中的資源群組、子網路和 IP 詳細資料，來建立應用程式閘道的 IP 設定詳細資料。

```azurepowershell
$resourceGroup = Get-AzureRmResourceGroup -Name $rg
$publicip = Get-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>設定基礎結構

以和現有標準應用程式閘道相同的格式，設定 IP 組態、前端 IP 組態、後端集區、HTTP 設定、憑證、連接埠、接聽程式和規則。 新的 SKU 會遵循與標準 SKU 相同的物件模型。

```azurepowershell
$ipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzureRmApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzureRmApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "scrap" -AsPlainText -Force
$sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "D:\Networking\ApplicationGateway\scrap.pfx"
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>指定自動調整

現在您可以指定應用程式閘道的自動調整設定。 支援兩種自動調整設定類型：

* **固定容量模式**。 在此模式中，應用程式閘道不會自動調整，並且會以固定的縮放單位容量運作。

   ```azurepowershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **自動調整模式**。 在此模式中，應用程式閘道會根據應用程式流量模式而自動調整。

   ```azurepowershell
   $autoscaleConfig = New-AzureRmApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>建立應用程式閘道

建立應用程式閘道，並包含備援區域和自動調整設定。

```azurepowershell
$appgw = New-AzureRmApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>測試應用程式閘道

使用 Get-AzureRmPublicIPAddress 取得應用程式閘道的公用 IP 位址。 將公用 IP 位址或 DNS 名稱複製並貼到您瀏覽器的網址列。

`Get-AzureRmPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>清除資源

首先，探索使用應用程式閘道建立的資源。 然後，若不再需要這些資源，您可以使用 `Remove-AzureRmResourceGroup` 命令將資源群組、應用程式閘道和所有相關資源移除。

`Remove-AzureRmResourceGroup -Name $rg`

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立包含 URL 路徑型路由規則的應用程式閘道](./tutorial-url-route-powershell.md)
