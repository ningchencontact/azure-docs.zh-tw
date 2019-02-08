---
title: 設定 Azure Stack 站對站 VPN 連線 | Microsoft Docs
description: 了解 Azure Stack 中站對站 VPN 或 VNet 對 VNet 連線的 IPsec/IKE 原則
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2019
ms.author: sethm
ms.lastreviewed: 01/19/2019
ms.openlocfilehash: 9a14c819acbc46d8a281f73fd4f3185caa8a1e02
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478146"
---
# <a name="configure-ipsecike-policy-for-site-to-site-vpn-connections"></a>設定站對站 VPN 連線的 IPsec/IKE 原則

本文會逐步說明用來為 Azure Stack 中的站對站 (S2S) VPN 設定 IPsec/IKE 原則的步驟。

## <a name="ipsec-and-ike-policy-parameters-for-vpn-gateways"></a>VPN 閘道的 IPsec 和 IKE 原則參數

IPsec 和 IKE 通訊協定標準支援各種不同的密碼編譯演算法的各種組合。 若要了解 Azure Stack 中所支援的參數，請參閱  [IPsec/IKE 參數](azure-stack-vpn-gateway-settings.md#ipsecike-parameters)，以便滿足您的合規性或安全性需求。

本文會指導您如何建立和設定 IPsec/IKE 原則，並套用至新的或現有的連線。

## <a name="considerations"></a>考量

在使用這些原則時，請注意下列重要事項：

- IPsec/IKE 原則只適用於 Standard 和 HighPerformance ** (路由式) 閘道 SKU。

- 每個給定的連線只能指定「一個」原則組合。 **** 

- 您必須同時對 IKE (主要模式) 和 IPsec (快速模式) 指定所有的演算法和參數。 系統不允許只指定一部分原則。

- 請確認 VPN 裝置廠商規格，確保內部部署 VPN 裝置支援原則。 如果原則不相容，則無法建立站對站連線。

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a>第 1 部分 - 建立和設定 IPsec/IKE 原則的工作流程

本節概述要在站對站 VPN 連線上建立和更新 IPsec/IKE 原則所必須執行的工作流程：

1. 建立虛擬網路和 VPN 閘道。

2. 為跨單位連線建立區域網路閘道。

3. 使用選取的演算法和參數建立 IPsec/IKE 原則。

4. 使用 IPsec/IKE 原則建立 IPsec 連線。

5. 新增/更新/移除現有連線的 IPsec/IKE 原則。

本文中的指示可協助您安裝和設定 IPsec/IKE 原則，如下圖所示：

![安裝和設定 IPsec/IKE 原則](media/azure-stack-vpn-s2s/site-to-site.png)

## <a name="part-2---supported-cryptographic-algorithms-and-key-strengths"></a>第 2 部分 - 支援的密碼編譯演算法和金鑰長度

下表列出可供 Azure Stack 客戶設定的支援密碼編譯演算法和金鑰強度：

| IPsec/IKEv2                                          | 選項                                                                  |
|------------------------------------------------------|--------------------------------------------------------------------------|
| IKEv2 加密                                     | AES256、AES192、AES128、DES3、DES                                        |
| IKEv2 完整性                                      | SHA384、SHA256、SHA1、MD5                                                |
| DH 群組                                             | ECP384、ECP256、DHGroup14、DHGroup2048、DHGroup2、DHGroup1、無         |
| IPsec 加密                                     | GCMAES256、GCMAES192、GCMAES128、AES256、AES192、AES128、DES3、DES、無 |
| IPsec 完整性                                      | GCMASE256、GCMAES192、GCMAES128、SHA256、SHA1、MD5                       |
| PFS 群組                                            | PFS24、ECP384、ECP256、PFS2048、PFS2、PFS1、無                         |
| QM SA 存留期                                       | (選擇性：如果未指定，即會使用預設值)<br />                         秒 (整數；最小 300/預設值 27000 秒)<br />                         KB 數 (整數；最小 1024/預設值 102400000 KB 數) |                                                                          |
| 流量選取器                                     | Azure Stack 不支援原則式流量選取器。         |

- 內部部署 VPN 裝置組態必須符合或包含您在 Azure IPsec/IKE 原則中指定的下列演算法和參數︰

  - IKE 加密演算法 (主要模式/第 1 階段)
  - IKE 完整性演算法 (主要模式/第 1 階段)
  - DH 群組 (主要模式/第 1 階段)
  - IPsec 加密演算法 (快速模式/第 2 階段)
  - IPsec 完整性演算法 (快速模式/第 2 階段)
  - PFS 群組 (快速模式/第 2 階段)
  - SA 存留期只需要在本機指定，不需要相符。

- 如果 GCMAES 會用於 IPsec 加密演算法，您必須基於 IPsec 完整性選取相同的 GCMAES 演算法和金鑰長度；例如，針對這兩者使用 GCMAES128。

- 在上表中：

  - IKEv2 會對應到主要模式或第 1 階段
  - IPsec 會對應到快速模式或第 2 階段
  - DH 群組會指定在主要模式或第 1 階段中使用的 Diffie-Hellmen 群組
  - PFS 群組會指定在快速模式或第 2 階段中使用的 Diffie-Hellmen 群組

- Azure Stack VPN 閘道的 IKEv2 主要模式 SA 存留期會固定為 28,800 秒。

下表列出自訂原則所支援的對應 Diffie-Hellman 群組：

| Diffie-Hellman 群組 | DHGroup   | PFSGroup      | 金鑰長度    |
|----------------------|-----------|---------------|---------------|
| 1                    | DHGroup1  | PFS1          | 768 位元 MODP  |
| 2                    | DHGroup2  | PFS2          | 1024 位元 MODP |
| 14                   | DHGroup14<br/>DHGroup2048 | PFS2048       | 2048 位元 MODP |
| 19                   | ECP256    | ECP256        | 256 位元 ECP   |
| 20                   | ECP384    | ECP384        | 384 位元 ECP   |
| 24                   | DHGroup24 | PFS24         | 2048 位元 MODP |

如需詳細資訊，請參閱 [RFC3526](https://tools.ietf.org/html/rfc3526) 和 [RFC5114](https://tools.ietf.org/html/rfc5114)。

## <a name="part-3---create-a-new-site-to-site-vpn-connection-with-ipsecike-policy"></a>第 3 部分 - 使用 IPsec/IKE 原則建立新的站對站 VPN 連線

本節將逐步引導您使用 IPsec/IKE 原則來建立站對站 VPN 連線。 下列步驟將建立連線，如下圖所示：

![site-to-site-policy](media/azure-stack-vpn-s2s/site-to-site.png)

如需如何建立站對站 VPN 連線的詳細逐步指示，請參閱 [建立站對站 VPN 連線](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)。

### <a name="prerequisites"></a>必要條件

在開始之前，請先確定您具備下列必要條件：

- Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，則可以啟用  [MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊 [免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

- Azure Resource Manager PowerShell Cmdlet。 如需如何安裝 PowerShell Cmdlet 的詳細資訊，請參閱 [安裝適用於 Azure Stack 的 PowerShell](../azure-stack-powershell-install.md) 。

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>步驟1 - 建立虛擬網路、VPN 閘道和區域網路閘道

#### <a name="1-declare-variables"></a>1.宣告變數

對於此練習，一開始請先宣告下列變數。 在針對生產環境進行設定時，請務必使用您自己的值取代預留位置：

```powershell
$Sub1 = "\<YourSubscriptionName\>"
$RG1 = "TestPolicyRG1"
$Location1 = "East US 2"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPconf1 = "gw1ipconf1"
$Connection16 = "VNet1toSite6"
$LNGName6 = "Site6"
$LNGPrefix61 = "10.61.0.0/16"
$LNGPrefix62 = "10.62.0.0/16"
$LNGIP6 = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2.連接至您的訂用帳戶並建立新的資源群組

請確定您切換為 PowerShell 模式以使用資源管理員 Cmdlet。 如需詳細資訊，請參閱 [以使用者的身分使用 PowerShell 連線到 Azure Stack](azure-stack-powershell-configure-user.md)。

開啟 PowerShell 主控台並連接到您的帳戶。 使用下列範例來協助您連接：

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3.建立虛擬網路、VPN 閘道和區域網路閘道

下列範例會建立有三個子網路的虛擬網路 **TestVNet1** 以及 VPN 閘道。 在替代值時，請務必一律將閘道子網路具體命名為 **GatewaySubnet**。 如果您將其命名為其他名稱，閘道建立會失敗。

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1

$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
-VirtualNetwork $vnet1

$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1
-Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
-Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn
-VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1
-Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix
$LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-site-to-site-vpn-connection-with-an-ipsecike-policy"></a>步驟 2 - 使用 IPsec/IKE 原則建立站對站 VPN 連線

#### <a name="1-create-an-ipsecike-policy"></a>1.建立 IPsec/IKE 原則

此範例指令碼會使用下列演算法和參數來建立 IPsec/IKE 原則：

- IKEv2：AES128、SHA1、DHGroup14
- IPsec：AES256、SHA256、無、SA 存留期 14400 秒和 102400000 KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup none -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

如果您針對 IPsec 使用 GCMAES，就必須針對 IPsec 加密和完整性使用相同的 GCMAES 演算法和金鑰長度。

#### <a name="2-create-the-site-to-site-vpn-connection-with-the-ipsecike-policy"></a>2.使用 IPsec/IKE 原則建立站對站 VPN 連線

建立站對站 VPN 連線，並套用先前所建立的 IPsec/IKE 原則。

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'Azs123'
```

> [!IMPORTANT]
> 在連線上指定 IPsec/IKE 原則之後，Azure VPN 閘道只會傳送或接受該特定連線上的 IPsec/IKE 提案，而提案具有所指定的密碼編譯演算法和金鑰長度。 確定連線的內部部署 VPN 裝置使用或接受確切原則組合，否則不會建立站對站 VPN 通道。

## <a name="part-4---update-ipsecike-policy-for-a-connection"></a>第 4 部分 - 更新連線的 IPsec/IKE 原則

上一節說明了如何管理現有站對站連線的 IPsec/IKE 原則。 下一節將會逐步引導您在連線上進行下列作業：

1. 顯示連線的 IPsec/IKE 原則
2. 新增或更新連線的 IPsec/IKE 原則
3. 移除連線的 IPsec/IKE 原則

> [!NOTE]
> 只有 Standard 和 HighPerformance 路由式 VPN 閘道才支援 IPsec/IKE 原則。 **  **  Basic 閘道 SKU 則不適用。

### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1.顯示連線的 IPsec/IKE 原則

下列範例示範如何取得連線上所設定的 IPsec/IKE 原則。 指令碼也會從前面的練習繼續：

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

最後一個命令會列出連線上所設定的目前 IPsec/IKE 原則 (如果有的話)。 下列範例是連線的範例輸出：

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

如果未設定任何 IPsec/IKE 原則，`$connection6.policy` 命令會收到空白傳回。 這並不表示連線上未設定 IPsec/IKE；其意義是沒有自訂的 IPsec/IKE 原則。 實際連線會使用內部部署 VPN 裝置與 Azure VPN 閘道之間交涉的預設原則。

### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2.新增或更新連線的 IPsec/IKE 原則

在連線上新增原則或更新現有原則的步驟相同：建立新的原則，然後將新的原則套用至連線。

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1

$newpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

$connection6.SharedKey = "AzS123"

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

您可以再次取得連線，以檢查是否已更新原則：

```powershell
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

您應該會在最後一行看到輸出，如下列範例所示：

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3.移除連線的 IPsec/IKE 原則

移除連線的自訂原則後，Azure VPN 閘道會回復為使用 [預設的 IPsec/IKE 提案](azure-stack-vpn-gateway-settings.md#ipsecike-parameters)，並與內部部署 VPN 裝置重新進行交涉。

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.SharedKey = “AzS123”
$currentpolicy = $connection6.IpsecPolicies\[0\]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

您可以使用相同的指令碼，以檢查是否已從連線中移除原則。

## <a name="next-steps"></a>後續步驟

- [Azure Stack 的 VPN 閘道組態設定](azure-stack-vpn-gateway-settings.md)
