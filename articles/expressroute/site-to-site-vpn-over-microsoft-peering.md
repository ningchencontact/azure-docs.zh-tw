---
title: "透過 Microsoft Azure expressroute 對等互連設定站對站 VPN |Microsoft 文件"
description: "設定透過使用站對站 VPN 閘道的 ExpressRoute Microsoft 對等互連電路的 IPsec/IKE 連線至 Azure。"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2017
ms.author: cherylmc
ms.openlocfilehash: 64203e2cbac1206224f0e0ad8b7d364f19ad0332
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2017
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>透過 ExpressRoute Microsoft 對等互連設定站對站 VPN

這篇文章可協助您設定透過私人 ExpressRoute 連線在內部部署網路與 Azure 虛擬網路 (Vnet) 之間的安全加密的連線。 設定安全通道透過 ExpressRoute 可讓資料交換與機密性、 禁止重新播放、 真實性和完整性。

## <a name="architecture"></a>架構

您可以利用 Microsoft 對等互連，建立您選取在內部部署網路與 Azure Vnet 之間的站對站 IPsec/IKE VPN 通道。

  ![連線概觀](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)

>[!NOTE]
>當您設定站對站 VPN 透過 Microsoft 對等互連時，您必須支付 VPN 閘道與 VPN 輸出。 如需詳細資訊，請參閱[VPN 閘道定價](https://azure.microsoft.com/pricing/details/vpn-gateway)。
>
>

高可用性和備援，您可以設定多個通道透過 ExpressRoute 電路的兩個 MSEE PE 組，及啟用通道之間的負載平衡。

  ![高可用性的選項](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

使用 VPN 閘道，或透過 Azure Marketplace 中的適當網路虛擬應用裝置 (NVA) 可用時，會終止 VPN 通道，透過 Microsoft 對等互連。 您可以交換路由靜態或動態方式透過加密通道而不會暴露至基礎的 Microsoft 對等的路由交換。 在本文範例中，BGP （不同於用來建立 Microsoft 對等互連 BGP 工作階段） 用來動態交換透過加密通道的 前置詞。

>[!IMPORTANT]
>針對在內部部署端，通常是 Microsoft 對等互連在周邊網路上的方式來終止，並私人互連終止核心網路區域上。 兩個區域均會被打散使用防火牆。 如果您要設定 Microsoft 專用的啟用安全通道透過 ExpressRoute 對等互連，請記得透過會開始透過 Microsoft 對等互連通告的感興趣的公用 Ip 篩選。
>
>

## <a name="workflow"></a>工作流程

1. 設定 Microsoft 對等 ExpressRoute 電路。
2. 通告選取 Azure 區域公用前置詞，以透過 Microsoft 對等內部部署網路。
3. 設定 VPN 閘道，並建立 IPsec 通道
4. 設定內部部署 VPN 裝置。
5. 建立站對站 IPsec/IKE 連線。
6. （選擇性）防火牆/篩選上設定內部部署 VPN 裝置。
7. 測試和驗證的 IPsec 通訊透過 ExpressRoute 電路。

## <a name="peering"></a>1.設定 Microsoft 對等互連

若要設定站對站 VPN 連線透過 ExpressRoute，您必須利用 ExpressRoute Microsoft 對等互連。

* 若要設定新的 ExpressRoute 電路，開頭[ExpressRoute 必要條件](expressroute-prerequisites.md)發行項，然後[建立及修改 ExpressRoute 電路](expressroute-howto-circuit-arm.md)。

* 如果您已經擁有 ExpressRoute 電路，但不是需要 Microsoft 對等設定，設定 Microsoft 對等互連使用[建立及修改 ExpressRoute 循環的對等互連](expressroute-howto-routing-arm.md#msft)發行項。

一旦您已設定您的電路及 Microsoft 對等互連，就可以輕鬆地檢視使用**概觀**在 Azure 入口網站頁面。

![循環](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="routefilter"></a>2.設定路由篩選器

路由篩選可讓您識別想要透過 ExpressRoute 線路的 Microsoft 對等互連使用的服務。 就本質上所有的 BGP 社群值的允許清單。 

![路由篩選器](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

在此範例中，會將部署只在*Azure 美國西部 2*區域。 路由篩選規則已加入，以允許只廣告 Azure 美國西部 2 區域前置詞，其具有 BGP 社群值*12076:51026*。 指定您想要允許選取的區域前置詞**管理規則**。

路由篩選條件內部，您也需要選擇路由篩選器所適用的 ExpressRoute 電路。 您可以選擇選取的 ExpressRoute 電路**新增電路**。 在上圖中，路由篩選器是相關聯的 ExpressRoute 電路的範例。

### <a name="configfilter"></a>2.1 設定路由篩選器

設定路由篩選器。 如需步驟，請參閱[設定 Microsoft 對等互連的路由篩選](how-to-routefilter-portal.md)。

### <a name="verifybgp"></a>2.2 確認 BGP 路由

一旦您已成功建立 Microsoft 對等互連透過 ExpressRoute 電路並聯電路的路由篩選器，您可以確認 BGP 路由對等互連與 MSEEs PE 裝置上收到 MSEEs。 驗證命令會根據您的 PE 裝置的作業系統而異。

#### <a name="cisco-examples"></a>Cisco 範例

這個範例會使用 Cisco IOS XE 命令。 在範例中，虛擬路由和轉送 (VRF) 執行個體用來找出對等互連流量。

```
show ip bgp vpnv4 vrf 10 summary
```

下列部分輸出顯示 68 前置詞已收到來自芳鄰 *.243.229.34 與 ASN 12076 (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

若要查看來自芳鄰的前置詞清單，請使用下列範例：

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

若要確認您收到一組正確的前置詞，您可以跨-確認。 下列 Azure PowerShell 命令輸出會列出公告透過 Microsoft 對等互連，為每個服務和每個 Azure 區域的前置詞：

```powershell
Get-AzureRmBgpServiceCommunity
```

## <a name="vpngateway"></a>3.設定 VPN 閘道和 IPsec 通道

在本節中，Azure VPN 閘道與內部部署 VPN 裝置之間建立 IPsec VPN 通道。 範例會使用 Cisco 雲端服務路由器 (CSR1000) VPN 裝置。

下圖顯示 IPsec VPN 通道在內部部署 VPN 裝置 1，與 Azure VPN 閘道執行個體組之間建立。 2 的內部部署 VPN 裝置之間的兩個 IPsec VPN 通道建立 Azure VPN 閘道執行個體組不在圖表中，說明並不會列出組態詳細資料。 不過，其他 VPN 通道，可改善高可用性。

  ![VPN 通道](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

透過 IPsec 通道組 eBGP 工作階段建立交換私人網路路由。 下圖顯示建立透過 IPsec 通道對 eBGP 工作階段：

  ![透過通道配對的 eBGP 工作階段](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

下圖顯示範例網路抽象的概觀：

  ![範例網路](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>關於 Azure Resource Manager 範本範例

在範例中，VPN 閘道和 IPsec 通道終止設定使用 Azure Resource Manager 範本。 如果您不熟悉使用資源管理員範本，或若要了解資源管理員範本，請參閱[瞭解之結構和語法的 Azure 資源管理員範本](../azure-resource-manager/resource-group-authoring-templates.md)。 本節中的範本建立綠地 Azure 環境 (VNet)。 不過，如果您有現有的 VNet，您可以參考它在範本中。 如果您不熟悉 VPN 閘道 IPsec/IKE 站對站組態，請參閱[建立站對站連線](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)。

>[!NOTE]
>您不需要使用 Azure 資源管理員範本，才能建立此設定。 您可以建立這個設定使用 Azure 入口網站或 PowerShell。
>
>

### <a name="variables3"></a>3.1 宣告的變數

在此範例中，變數宣告對應至網路的範例。 當您宣告變數，修改此區段，以反映您的環境。

* 變數**localAddressPrefix**是終止 IPsec 通道的內部 IP 位址的陣列。
* **GatewaySku**決定 VPN 輸送量。 GatewaySku 和 vpnType 相關的詳細資訊，請參閱[VPN 閘道組態設定](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku)。 如需定價，請參閱[VPN 閘道定價](https://azure.microsoft.com/pricing/details/vpn-gateway)。
* 設定**vpnType**至**RouteBased**。

```json
"variables": {
  "virtualNetworkName": "SecureVNet",       // Name of the Azure VNet
  "azureVNetAddressPrefix": "10.2.0.0/24",  // Address space assigned to the VNet
  "subnetName": "Tenant",                   // subnet name in which tenants exists
  "subnetPrefix": "10.2.0.0/25",            // address space of the tenant subnet
  "gatewaySubnetPrefix": "10.2.0.224/27",   // address space of the gateway subnet
  "localGatewayName": "localGW1",           // name of remote gateway (on-premises)
  "localGatewayIpAddress": "X.243.229.110", // public IP address of the on-premises VPN device
  "localAddressPrefix": [
    "172.16.0.1/32",                        // termination of IPsec tunnel-1 on-premises 
    "172.16.0.2/32"                         // termination of IPsec tunnel-2 on-premises 
  ],
  "gatewayPublicIPName1": "vpnGwVIP1",    // Public address name of the first VPN gateway instance
  "gatewayPublicIPName2": "vpnGwVIP2",    // Public address name of the second VPN gateway instance 
  "gatewayName": "vpnGw",                 // Name of the Azure VPN gateway
  "gatewaySku": "VpnGw1",                 // Azure VPN gateway SKU
  "vpnType": "RouteBased",                // type of VPN gateway
  "sharedKey": "string",                  // shared secret needs to match with on-premise configuration
  "asnVpnGateway": 65000,                 // BGP Autonomous System number assigned to the VPN Gateway 
  "asnRemote": 65010,                     // BGP Autonmous Syste number assigned to the on-premises device
  "bgpPeeringAddress": "172.16.0.3",      // IP address of the remote BGP peer on-premises
  "connectionName": "vpn2local1",
  "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]",
  "gatewaySubnetRef": "[concat(variables('vnetID'),'/subnets/','GatewaySubnet')]",
  "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
  "api-version": "2017-06-01"
},
```

### <a name="vnet"></a>3.2 建立虛擬網路 (VNet)

如果您要與 VPN 通道來產生關聯現有的 VNet，您可以略過此步驟。

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[variables('azureVNetAddressPrefix')]"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "[variables('subnetPrefix')]"
        }
      },
      {
        "name": "GatewaySubnet",
        "properties": {
          "addressPrefix": "[variables('gatewaySubnetPrefix')]"
        }
      }
    ]
  },
  "comments": "Create a Virtual Network with Subnet1 and Gatewaysubnet"
},
```

### <a name="ip"></a>3.3 將公用 IP 位址指派給 VPN 閘道器執行個體
 
指定每個執行個體的 VPN 閘道的公用 IP 位址。

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName1')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the first instance of the VPN gateway"
  },
  {
    "apiVersion": "[variables('api-version')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName2')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the second instance of the VPN gateway"
  },
```

### <a name="termination"></a>3.4 指定在內部部署 VPN 通道終止 （區域網路閘道）

在內部部署 VPN 裝置指**區域網路閘道**。 下列 json 片段也會指定遠端的 BGP 對等詳細資料：

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/localNetworkGateways",
  "name": "[variables('localGatewayName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "localNetworkAddressSpace": {
      "addressPrefixes": "[variables('localAddressPrefix')]"
    },
    "gatewayIpAddress": "[variables('localGatewayIpAddress')]",
    "bgpSettings": {
      "asn": "[variables('asnRemote')]",
      "bgpPeeringAddress": "[variables('bgpPeeringAddress')]",
      "peerWeight": 0
    }
  },
  "comments": "Local Network Gateway (referred to your on-premises location) with IP address of remote tunnel peering and IP address of remote BGP peer"
},
```

### <a name="creategw"></a>3.5 建立 VPN 閘道

本章節的範本使用主動-主動組態所需要的設定來設定 VPN 閘道。 請記住下列需求：

* 建立 VPN 閘道與**"RouteBased"** VpnType。 此設定是強制性，如果您想要啟用 VPN 閘道與 VPN 內部之間的 BGP 路由。
* 在主動 / 主動模式中，建立 VPN 閘道的兩個執行個體和指定的內部部署裝置之間的 VPN 通道**"activeActive"**參數設定為**true** Resource Manager 範本中. 若要了解有關高可用性的 VPN 閘道的詳細資訊，請參閱[高可用性的 VPN 閘道連線](../vpn-gateway/vpn-gateway-highlyavailable.md)。
* 若要設定之間的 VPN 通道 eBGP 工作階段，您必須指定兩個不同的 Asn 任一邊。 最好是指定私用的 ASN 編號。 如需詳細資訊，請參閱[BGP 的概觀和 Azure VPN 閘道](../vpn-gateway/vpn-gateway-bgp-overview.md)。

```json
{
"apiVersion": "[variables('api-version')]",
"type": "Microsoft.Network/virtualNetworkGateways",
"name": "[variables('gatewayName')]",
"location": "[resourceGroup().location]",
"dependsOn": [
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName1'))]",
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName2'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
],
"properties": {
  "ipConfigurations": [
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName1'))]"
        }
      },
      "name": "vnetGtwConfig1"
    },
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName2'))]"
        }
      },
          "name": "vnetGtwConfig2"
        }
      ],
      "sku": {
        "name": "[variables('gatewaySku')]",
        "tier": "[variables('gatewaySku')]"
      },
      "gatewayType": "Vpn",
      "vpnType": "[variables('vpnType')]",
      "enableBgp": true,
      "activeActive": true,
      "bgpSettings": {
        "asn": "[variables('asnVpnGateway')]"
      }
    },
    "comments": "VPN Gateway in active-active configuration with BGP support"
  },
```

### <a name="ipsectunnel"></a>3.6 建立 IPsec 通道

指令碼的最後一個動作會建立 Azure VPN 閘道與內部部署 VPN 裝置之間的 IPsec 通道。

```json
{
  "apiVersion": "[variables('api-version')]",
  "name": "[variables('connectionName')]",
  "type": "Microsoft.Network/connections",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworkGateways/', variables('gatewayName'))]",
    "[concat('Microsoft.Network/localNetworkGateways/', variables('localGatewayName'))]"
  ],
  "properties": {
    "virtualNetworkGateway1": {
      "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', variables('gatewayName'))]"
    },
    "localNetworkGateway2": {
      "id": "[resourceId('Microsoft.Network/localNetworkGateways', variables('localGatewayName'))]"
    },
    "connectionType": "IPsec",
    "routingWeight": 0,
    "sharedKey": "[variables('sharedKey')]",
    "enableBGP": "true"
  },
  "comments": "Create a Connection type site-to-site (IPsec) between the Azure VPN Gateway and the VPN device on-premises"
  }
```

## <a name="device"></a>4.設定內部部署 VPN 裝置

Azure VPN 閘道會與許多不同廠商的 VPN 裝置相容。 組態資訊及已經過驗證，才能使用 VPN 閘道裝置，請參閱[關於 VPN 裝置](../vpn-gateway/vpn-gateway-about-vpn-devices.md)。

當設定 VPN 裝置，您需要下列項目：

* 共用金鑰。 這是建立您的站對站 VPN 連線時所指定的相同共用的金鑰。 範例會使用基本的共用的金鑰。 我們建議您產生更複雜的金鑰以供使用。
* 您的 VPN 閘道的公用 IP 位址。 您可以使用 Azure 入口網站、PowerShell 或 CLI 來檢視公用 IP 位址。 若要尋找您使用 Azure 入口網站的 VPN 閘道的公用 IP 位址，請瀏覽至虛擬網路閘道，然後按一下您的閘道名稱。

通常 （通常透過 WAN 連線） 直接連接 eBGP 對等。 不過，當您設定透過 ExpressRoute Microsoft 對等互連的 IPsec VPN 通道 eBGP，有多個路由網域 eBGP 對等之間。 使用**ebgp multihop**命令，以不建立兩者間的 eBGP 芳鄰關聯性的直接連接對等。 遵循 ebgp multihop 命令的整數 BGP 封包中指定的 TTL 值。 此命令**最大路徑 eibgp 2**啟用負載平衡的兩個 BGP 路徑之間的流量。

### <a name="cisco1"></a>Cisco CSR1000 範例

下列範例會顯示在 HYPER-V 虛擬機器與內部部署 VPN 裝置 Cisco CSR1000 的組態：

```
!
crypto ikev2 proposal az-PROPOSAL
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy az-POLICY
 proposal az-PROPOSAL
!
crypto ikev2 keyring key-peer1
 peer azvpn1
  address 52.175.253.112
  pre-shared-key secret*1234
 !
!
crypto ikev2 keyring key-peer2
 peer azvpn2
  address 52.175.250.191
  pre-shared-key secret*1234
 !
!
!
crypto ikev2 profile az-PROFILE1
 match address local interface GigabitEthernet1
 match identity remote address 52.175.253.112 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer1
!
crypto ikev2 profile az-PROFILE2
 match address local interface GigabitEthernet1
 match identity remote address 52.175.250.191 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer2
!
crypto ikev2 dpd 10 2 on-demand
!
!
crypto ipsec transform-set az-IPSEC-PROPOSAL-SET esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile az-VTI1
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE1
!
crypto ipsec profile az-VTI2
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE2
!
!
interface Loopback0
 ip address 172.16.0.3 255.255.255.255
!
interface Tunnel0
 ip address 172.16.0.1 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.253.112
 tunnel protection ipsec profile az-VTI1
!
interface Tunnel1
 ip address 172.16.0.2 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.250.191
 tunnel protection ipsec profile az-VTI2
!
interface GigabitEthernet1
 description External interface
 ip address x.243.229.110 255.255.255.252
 negotiation auto
 no mop enabled
 no mop sysid
!
interface GigabitEthernet2
 ip address 10.0.0.1 255.255.255.0
 negotiation auto
 no mop enabled
 no mop sysid
!
router bgp 65010
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 network 10.0.0.0 mask 255.255.255.0
 network 10.1.10.0 mask 255.255.255.128
 neighbor 10.2.0.228 remote-as 65000
 neighbor 10.2.0.228 ebgp-multihop 5
 neighbor 10.2.0.228 update-source Loopback0
 neighbor 10.2.0.228 soft-reconfiguration inbound
 neighbor 10.2.0.228 filter-list 10 out
 neighbor 10.2.0.229 remote-as 65000    
 neighbor 10.2.0.229 ebgp-multihop 5
 neighbor 10.2.0.229 update-source Loopback0
 neighbor 10.2.0.229 soft-reconfiguration inbound
 maximum-paths eibgp 2
!
ip route 0.0.0.0 0.0.0.0 10.1.10.1
ip route 10.2.0.228 255.255.255.255 Tunnel0
ip route 10.2.0.229 255.255.255.255 Tunnel1
!
```

## <a name="firewalls"></a>5.設定 VPN 裝置篩選和防火牆 （選擇性）

設定您的防火牆和篩選，根據您的需求。

## <a name="testipsec"></a>6.測試和驗證的 IPsec 通道

IPsec 通道的狀態可以驗證 Azure VPN 閘道上，透過 Powershell 命令：

```powershell
Get-AzureRmVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

範例輸出︰

```powershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

若要獨立檢查 Azure VPN 閘道器執行個體上的通道的狀態，請使用下列範例：

```powershell
Get-AzureRmVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

範例輸出︰

```powershell
Tunnel                           : vpn2local1_52.175.250.191
ConnectionStatus                 : Connected
IngressBytesTransferred          : 4877438
EgressBytesTransferred           : 8754071
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:30

Tunnel                           : vpn2local1_52.175.253.112
ConnectionStatus                 : Connected
IngressBytesTransferred          : 5660773
EgressBytesTransferred           : 8980589
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:13
```

您也可以在內部部署 VPN 裝置檢查通道狀態。

Cisco CSR1000 範例：

```
show crypto session detail
show crypto ikev2 sa
show crypto ikev2 session detail
show crypto ipsec sa
```

範例輸出︰

```
csr1#show crypto session detail

Crypto session current status

Code: C - IKE Configuration mode, D - Dead Peer Detection
K - Keepalives, N - NAT-traversal, T - cTCP encapsulation
X - IKE Extended Authentication, F - IKE Fragmentation
R - IKE Auto Reconnect

Interface: Tunnel1
Profile: az-PROFILE2
Uptime: 00:52:46
Session status: UP-ACTIVE
Peer: 52.175.250.191 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.250.191
      Desc: (none)
  Session ID: 3
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.250.191/4500 Active
          Capabilities:DN connid:3 lifetime:23:07:14
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 279 drop 0 life (KB/Sec) 4607976/433
        Outbound: #pkts enc'ed 164 drop 0 life (KB/Sec) 4607992/433

Interface: Tunnel0
Profile: az-PROFILE1
Uptime: 00:52:43
Session status: UP-ACTIVE
Peer: 52.175.253.112 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.253.112
      Desc: (none)
  Session ID: 2
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.253.112/4500 Active
          Capabilities:DN connid:2 lifetime:23:07:17
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 668 drop 0 life (KB/Sec) 4607926/437
        Outbound: #pkts enc'ed 477 drop 0 life (KB/Sec) 4607953/437
```

線路通訊協定上虛擬通道介面 (VTI) 不會變更為 「 向上 」 IKE 階段 2 完成為止。 下列命令會驗證安全性關聯：

```
csr1#show crypto ikev2 sa

IPv4 Crypto IKEv2  SA

Tunnel-id Local                 Remote                fvrf/ivrf            Status
2         10.1.10.50/4500       52.175.253.112/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3277 sec

Tunnel-id Local                 Remote                fvrf/ivrf            Status
3         10.1.10.50/4500       52.175.250.191/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3280 sec

IPv6 Crypto IKEv2  SA

csr1#show crypto ipsec sa | inc encaps|decaps
    #pkts encaps: 177, #pkts encrypt: 177, #pkts digest: 177
    #pkts decaps: 296, #pkts decrypt: 296, #pkts verify: 296
    #pkts encaps: 554, #pkts encrypt: 554, #pkts digest: 554
    #pkts decaps: 746, #pkts decrypt: 746, #pkts verify: 746
```

### <a name="verifye2e"></a>確認內部之間的端對端連線的網路內部部署與 Azure VNet

如果 IPsec 通道都已啟動且已正確設定的靜態路由，您應該能夠 ping 遠端 BGP 對等體的 IP 位址：

```
csr1#ping 10.2.0.228
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.228, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 5/5/5 ms

#ping 10.2.0.229
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.229, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/5/6 ms
```

### <a name="verifybgp"></a>確認 IPsec 透過 BGP 工作階段

Azure VPN 閘道，請確認 BGP 對等的狀態：

```powershell
Get-AzureRmVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

範例輸出︰

```powershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

若要確認收到透過 eBGP VPN 集訊器在從內部網路首碼的清單，您可以篩選由屬性"Origin":

```powershell
Get-AzureRmVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

在此範例輸出，ASN 65010 是在 VPN 內部 BGP 自發系統編號。

```powershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

若要查看的通告的路由清單：

```powershell
Get-AzureRmVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

範例輸出︰

```powershell
AsPath LocalAddress Network        NextHop    Origin SourcePeer Weight
------ ------------ -------        -------    ------ ---------- ------
       10.2.0.229   10.2.0.0/24    10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.10/32 10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.5/32  10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.1/32  10.2.0.229 Igp                  0
65010  10.2.0.229   10.1.10.0/25   10.2.0.229 Igp                  0
65010  10.2.0.229   10.0.0.0/24    10.2.0.229 Igp                  0
```

例如，在內部部署 Cisco CSR1000:

```
csr1#show ip bgp neighbors 10.2.0.228 routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.2.0.0/24      10.2.0.228                             0 65000 i
 r>   172.16.0.1/32    10.2.0.228                             0 65000 i
 r>   172.16.0.2/32    10.2.0.228                             0 65000 i
 r>   172.16.0.3/32   10.2.0.228                             0 65000 i

Total number of prefixes 4
```

可以使用下列命令會列出公告從內部部署 Cisco CSR1000 Azure VPN 閘道的網路清單：

```powershell
csr1#show ip bgp neighbors 10.2.0.228 advertised-routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.0.0.0/24      0.0.0.0                  0         32768 i
 *>   10.1.10.0/25     0.0.0.0                  0         32768 i

Total number of prefixes 2
```

## <a name="next-steps"></a>後續步驟

* [設定 ExpressRoute 線路的網路效能監視器](how-to-npm.md)

* [新增站對站連線至現有的 VPN 閘道連線的 VNet](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)