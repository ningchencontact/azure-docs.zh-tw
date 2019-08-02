---
title: Azure 澳大利亞的 azure VPN 閘道
description: 在 Azure 澳大利亞中執行 VPN 閘道以符合 ISM 規範, 並有效地保護澳大利亞政府機關
author: galey801
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 442ad6334a1775033018005d4a85875dbcb08ada
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571844"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Azure 澳大利亞的 azure VPN 閘道

具有任何公用雲端的重要服務, 是將雲端資源和服務連線到現有內部部署系統的安全連線。  在 Azure 中提供這項功能的服務是 Azure VPN 閘道 (VPN 閘道)。 本文概述設定 VPN 閘道以符合澳大利亞信號情報局 (.ASD)[資訊安全手動控制項](https://acsc.gov.au/infosec/ism/)(ISM) 的主要考慮。

VPN 閘道可用來在 Azure 中的虛擬網路與另一個網路之間傳送加密的流量。  VPN 閘道會解決下列三種案例:

- **站對站**S2S
- **點對站**P2S
- **VNet 對 VNet**

本文將著重在 S2S VPN 閘道。 [圖 1] 顯示範例站對站 VPN 閘道設定。

![具有多網站連線的 VPN 閘道](media/vpngateway-multisite-connection-diagram.png)

*圖1– Azure 站對站 VPN 閘道*

## <a name="key-design-considerations"></a>主要設計考慮

有三個網路選項可將 Azure 連接到澳大利亞政府客戶:

- **圖示**
- **ExpressRoute**
- **公用網際網路**

[適用于 Azure](https://servicetrust.microsoft.com/viewpage/Australia)的澳大利亞網路安全中心取用者指南會建議使用 VPN 閘道 (或對等的受保護認證的協力廠商服務) 搭配三個網路選項, 以確保連接子合適用于加密和完整性的 ISM 控制項。

### <a name="encryption-and-integrity"></a>加密和完整性

根據預設, VPN 會在連線建立期間, 將加密和完整性演算法和參數協調成 IKE 交握的一部分。  在 IKE 交握期間, 設定和喜好設定的順序取決於 VPN 閘道是起始端還是回應者 (NB: 這是透過 VPN 裝置控制)。  連線的最後設定是由 VPN 裝置的設定所控制。  如需已驗證之 VPN 裝置和其設定的詳細資訊, 請參閱這裡:[關於 VPN 裝置](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)

VPN 閘道可以在連線上設定自訂的 IPsec/IKE 原則, 以控制加密和完整性。

### <a name="resource-operations"></a>資源作業

VPN 閘道會透過公用網際網路建立 Azure 和非 Azure 環境之間的連線。  ISM 具有與明確的連線授權相關的控制項。  根據預設, 您可以使用 VPN 閘道, 在安全的環境中建立未經授權的通道。  因此, 組織請務必使用 Azure 角色型存取控制 (RBAC) 來控制誰可以建立和修改 VPN 閘道及其連線。  Azure 沒有「內建」角色可管理 VPN 閘道, 因此, 這將需要自訂角色。

「擁有者」、「參與者」和「網路參與者」角色的存取權受到嚴格控制。  也建議將 Azure AD Privileged Identity Management 用於更細微的存取控制。

### <a name="high-availability"></a>高可用性

Azure VPN 閘道可以有多個連線 (請參閱 [圖 1]), 並支援將多個內部部署 VPN 裝置連接到相同的內部部署環境。  

Azure 中的虛擬網路可以有多個 VPN 閘道, 可以部署在獨立、主動-被動或主動-主動設定中。

建議您在[高](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)可用性設定中部署所有 Vpn 閘道: 例如, 兩個內部部署 VPN 裝置連接到主動-被動或主動-主動模式中的兩個 VPN 閘道 (請參閱 [圖 2])。

![VPN 閘道的重複連接](media/dual-redundancy.png)

*圖表2–主動-主動 VPN 閘道和兩個 VPN 裝置*

### <a name="forced-tunneling"></a>強制通道

強制通道重新導向或「強制」所有網際網路系結流量回到內部部署環境, 透過 VPN 閘道進行檢查和審核。 在沒有強制通道的情況下, 來自 Azure 中 Vm 的網際網路系結流量會將 Azure 網路基礎結構直接移出至公用網際網路, 而不會有檢查或審核流量的選項。  當組織需要使用安全的網際網路閘道 (SIG) 來進行環境時, 這是很重要的。

## <a name="detailed-configuration"></a>詳細組態

### <a name="service-attributes"></a>服務屬性

針對澳大利亞政府設定的 S2S 連線 VPN 閘道必須具有下列屬性:

|屬性 | 一定|
|--- | --- |
|gatewayType | 專用|
|

符合受保護的 ISM 控制項所需的屬性設定如下:

|屬性 | 一定|
|--- |---|
|vpnType |RouteBased|
|vpnClientConfiguration/vpnClientProtocols | IkeV2|
|

Azure VPN 閘道支援來自 IPsec 和 IKE 通訊協定標準的各種密碼編譯演算法。  預設原則會設定與各種協力廠商 VPN 裝置的最大化互通性。  因此, 在 IKE 交握期間, 系統可能會協商不相容的設定。  因此, 強烈建議您將[自訂 IPsec/IKE 原則](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell)參數套用至 VPN 閘道中的 vpnClientConfiguration, 以確保連線符合內部部署環境連線到 AZURE 的 ISM 控制項。  主要的屬性包括:

|屬性|需要|一定|
|---|---|---|
|saLifeTimeSeconds|< 14400 秒|> 300 秒|
|Sadatasizekilobytes 不再| |> 1024 KB|
|ipsecEncryption| |AES256-GCMAES256|
|ipsecIntegrity| |SHA256-GCMAES256|
|ikeEncryption| |AES256-GCMAES256|
|ikeIntegrity| |SHA256-GCMAES256|
|dhGroup|DHGroup14、DHGroup24、ECP256、ECP384|DHGroup2|
|pfsGroup|PFS2048 作為、PFS24、ECP256、ECP384||
|

*針對上表中的 dhGroup 和 pfsGroup, 即使可以使用其他設定, 仍會偏好 ECP256 和 ECP384*

### <a name="related-services"></a>相關服務

在設計和設定 Azure VPN 閘道有許多相關服務也必須存在並加以設定:

|服務 | 必要動作|
|--- | ---|
|虛擬網路 | VPN 閘道會連接到虛擬網路。  建立新 VPN 閘道之前, 必須先建立虛擬網路。|
|公用 IP 位址 | S2S VPN 閘道需要公用 IP 位址, 才能建立內部部署 VPN 裝置與 VPN 閘道之間的連線能力。  建立 S2S VPN 閘道之前, 必須先建立公用 IP 位址。|
|Subnet | 必須為 VPN 閘道建立虛擬網路的子網。|
|

## <a name="implementation-steps-using-powershell"></a>使用 PowerShell 的執行步驟

### <a name="role-based-access-control-rbac"></a>角色型存取控制 (RBAC)

1. 建立自訂角色 (例如, virtualNetworkGateway 參與者)。  建立要指派給使用者的角色, 以允許建立和修改 VPN 閘道。 自訂角色應允許下列作業:

   Microsoft. Network/virtualNetworkGateways/*  
   Microsoft. Network/connections/*  
   Microsoft. Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft. Network/publicIPAddresses/*  
   Microsoft. Network/publicIPPrefixes/*  
   Microsoft.Network/routeTables/*  

2. 新增自訂角色給允許建立和管理 VPN 閘道和連線至內部部署環境的使用者。

### <a name="create-vpn-gateway"></a>建立 VPN 閘道

*這些步驟假設已建立虛擬網路*

1. 建立新的公用 IP 位址
2. 建立 VPN 閘道子網
3. 建立 VPN 閘道 IP config
4. 建立 VPN 閘道
5. 建立內部部署 VPN 裝置的局域網路閘道
6. 建立 IPsec 原則 (假設使用自訂 IPsec/IKE 原則)
7. 使用 IPsec 原則建立 VPN 閘道和局域網路閘道之間的連線

### <a name="enforce-tunneling"></a>強制通道

如果需要強制通道, 請在建立 VPN 閘道之前:

1. 建立路由表和路由規則
2. 將路由表與適當的子網產生關聯

建立 VPN 閘道之後:

1. 將 GatewayDefaultSite 設定為 VPN 閘道上的內部部署環境

### <a name="example-powershell-script"></a>範例 PowerShell 腳本

用於建立自訂 IPSEC/IKE 原則的範例 PowerShell 腳本, 符合適用于澳大利亞受保護安全性分類的 ISM 控制項。

它假設虛擬網路、VPN 閘道和本機閘道都存在。

#### <a name="create-an-ipsecike-policy"></a>建立 IPsec/IKE 原則

下列範例指令碼會使用下列演算法和參數來建立 IPsec/IKE 原則：

- IKEv2：AES256、SHA256、DHGroup ECP256
- IPsec：AES256, SHA256, PFS ECP256, SA 存留期14400秒 & 102400000 KB

```powershell
$custompolicy = New-AzIpsecPolicy `
                    -IkeEncryption AES256 `
                    -IkeIntegrity SHA256 `
                    -DhGroup ECP256 `
                    -IpsecEncryption AES256 `
                    -IpsecIntegrity SHA256 `
                    -PfsGroup ECP256 `
                    -SALifeTimeSeconds 14400 `
                    -SADataSizeKilobytes 102400000
```

#### <a name="create-a-s2s-vpn-connection-with-the-custom-ipsecike-policy"></a>使用自訂 IPsec/IKE 原則建立 S2S VPN 連線

```powershell
$vpngw = Get-AzVirtualNetworkGateway `
                    -Name "<yourVPNGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"
$localgw = Get-AzLocalNetworkGateway  `
                    -Name "<yourLocalGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"

New-AzVirtualNetworkGatewayConnection `
                    -Name "ConnectionName" `
                    -ResourceGroupName "<yourResourceGroupName>" `
                    -VirtualNetworkGateway1 $vpngw `
                    -LocalNetworkGateway2 $localgw `
                    -Location "Australia Central" `
                    -ConnectionType IPsec `
                    -IpsecPolicies $custompolicy `
                    -SharedKey "AzureA1b2C3"
```

## <a name="next-steps"></a>後續步驟

本文涵蓋 VPN 閘道的特定設定, 以符合資訊安全性手冊 (ISM) 中指定的需求, 以便在傳輸過程中保護澳大利亞政府保護的資料。 如需設定 VPN 閘道的詳細步驟:

- [Azure 虛擬網路閘道總覽](https://docs.microsoft.com/azure/vpn-gateway/)  
- [什麼是 VPN 閘道？](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
- [使用 PowerShell 建立具有站對站 VPN 連線的 VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
- [建立和管理 VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)