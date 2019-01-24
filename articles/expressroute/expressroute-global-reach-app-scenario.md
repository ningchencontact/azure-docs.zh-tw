---
title: Azure ExpressRoute Global Reach 應用程式案例 | Microsoft Do
description: 此頁面提供 Global Reach 的應用程式案例。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,global reach
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/14/2019
ms.author: rambala
ms.openlocfilehash: 5174baae40d7f14720affe90f702c024541a5745
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2019
ms.locfileid: "54333040"
---
# <a name="expressroute-global-reach-application-scenario"></a>ExpressRoute Global Reach 應用程式案例

若要深入了解 ExpressRoute Global Reach，請參閱 [ExpressRoute Global Reach][Global Reach]。 在本文中，我們會逐步解說應用程式案例、比較 ExpressRoute Global Reach 解決方案與其他幾個解決方案、針對範例案例設定 Global Reach，以及確認連線。 

##<a name="application-scenario"></a>應用程式案例

Fabrikam Inc. 在美國東部具有大型實體據點和 Azure 部署。 Fabrikam 可透過 ExpressRoute，在其內部部署與 Azure 部署之間進行後端連線。 Contoso Ltd. 在美國西部具有據點和 Azure 部署。 Contoso 可透過 ExpressRoute，在其內部部署與 Azure 部署之間進行後端連線。  

Fabrikam Inc. 購得 Contoso Ltd。合併以後，Fabrikam 想要讓網路互連。 下圖說明這個案例：

 [![1]][1]

上圖中間的虛線箭號表示必要的網路互連。 下表顯示 Contoso Ltd. 在合併之前，ExpressRoute 的主要私人對等互連路由表。

[![2]][2]

下表顯示 Fabrikam Inc. 在合併之前，ExpressRoute 的主要私人對等互連路由表。

[![3]][3]

## <a name="traditional-solutions"></a>傳統解決方案

### <a name="option-1-interconnect-on-premises-networks"></a>選項 1：讓內部部署網路互連

下圖說明這個選項。 如圖所示，您可以使用站對站 VPN 或其他寬頻連線選項，讓兩個內部部署網路互連，以及管理這兩個實體之間的所有路由。 

[![4]][4]

這個選項具有下列缺點：

- 您是透過次佳的路由來強制進行部署的區域間 Azure 通訊。
- 您會否定 Microsoft 骨幹網路對於區域間通訊的高可用性優點。
- 您會完全承擔區域間通訊的路由責任。

### <a name="option-2-expressroute-cross-connectivity-and-interconnect-on-premises-networks"></a>選項 2：ExpressRoute 交叉連線能力，以及讓內部部署網路互連

下圖說明這個選項。

[![5]][5]

如上圖所示，您可以另外建立 ExpressRoute 電路與跨區域 VNet 之間的連線。 VNet 與 ExpressRoute 線路之間的跨區域連線能夠進行下列通訊：

- 美國西部 / 美國東部 VNet 可分別與 Fabrikam/Contoso 內部部署網路進行通訊。
- 美國西部 VNet 可與美國東部 VNet 進行通訊。

仍然需要兩個內部部署網路之間的互連，內部部署網路才能彼此進行通訊。

此選項可讓私人部署的區域間 Azure 通訊透過 Microsoft 骨幹進行，以及讓內部部署網路之間的通訊透過外部網路進行。 不過，解決方案的主要缺點是您需要建立多個跨區域連線，這會使維護和疑難排解變複雜。 此外，此選項不讓您利用高度可用的 Microsoft 全球骨幹進行兩個內部部署網路之間通訊。

### <a name="option-3-vnet-peering-and-interconnect-on-premises-networks"></a>選項 3：VNet 對等互連和讓內部部署網路互連

下圖說明這個選項。 此選項使用 VNet 對等互連進行區域間 VNet 通訊。 此選項 (如圖所述) 並不完整，因為它不會處理跨區域 VNet 與內部部署環境的通訊 (透過中間的兩個虛線箭號線表示)。 使用內部部署對內部部署的連線 (如選項 1) 或 ExpressRoute 交叉連線能力 (如選項 2)，進行跨區域對內部部署的通訊。

[![6]][6]

此選項可提供區域間 VNet 通訊的最佳路由。 但是，如果 Fabrikam 或 Contoso 有更複雜的 Azure 部署 (例如中樞輪輻 VNet 模型)，便無法達成。 此外，如同先前兩個選項，該選項不讓您利用高度可用的 Microsoft 全球骨幹進行兩個內部部署網路之間通訊。

## <a name="global-reach"></a>Global Reach

ExpressRoute Global Reach 會連結 ExpressRoute 線路的私人對等互連，如下圖所示：

[![7]][7]

在兩個 ExpressRoute 線路之間設定 Global Reach，可讓兩個內部部署網路與兩個區域中的 Azure 部署進行私密通訊。 因此，Global Reach 可透過 Microsoft 骨幹網路滿足所有想要的通訊 (透過本文第一張圖中的虛線表示)。

### <a name="configure-global-reach"></a>設定 Global Reach

若要了解如何設定 ExpressRoute Global Reach，請參閱[設定 Global Reach][Configure Global Reach]。 

因為 Fabrikam Inc. 和 Contoso Ltd. 是以不同的公司讓 Azure 上線，所以這兩家公司的 ExpressRoute 線路位於兩個不同的 Azure 訂用帳戶中。 若要建立跨越訂用帳戶的 Global Reach，您需要在屬於 Contoso Ltd. 的 ExpressRoute 線路中建立授權，然後將它傳遞給 Fabrikam Inc.ExpressRoute 線路。


若要建立 Contoso 的 ExpressRoute 線路授權，請先登入 Contoso 的 Azure 帳戶，然後選取適當的訂用帳戶 (如果有多個訂用帳戶的話)。 這些步驟的 PowerShell 命令如下：

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```
建立 ExpressRoute 線路授權的步驟如下所列：

```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```

'Set-AzureRmExpressRouteCircuit' 輸出會列出 ExpressRouteCircuit。 請注意，私人對等互連識別碼和授權金鑰會列在清單結尾。 請參閱下列範例 PowerShell 輸出程式碼片段：

```powershell
ServiceKey                       : <serviceKey>
Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 65020,
                                       "PrimaryPeerAddressPrefix": "192.168.11.16/30",
                                       "SecondaryPeerAddressPrefix": "192.168.11.20/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 110,
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "4",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
Authorizations                   : [
                                     {
                                       "Name": "Auth4-ASH-Cust11",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/authorizations/Auth4-ASH-Cust11",
                                       "AuthorizationKey": "<authorizationKey>",
                                       "AuthorizationUseStatus": "Used",
                                       "ProvisioningState": "Succeeded"
                                     }
                                   ]
AllowClassicOperations           : False
GatewayManagerEtag               : 4
```

使用對等互連識別碼與授權金鑰，您可以在 Fabrikam 的 ExpressRoute 線路之下建立 Global Reach。 登入 Fabrikam 的 Azure 帳戶。 如果有多個訂用帳戶，請選取適當的訂用帳戶。

Global Reach 可跨越兩個 MSEE 配對，建立一組備援的點對點連線。 對於這兩個點對點連線，您必須指定 /29 位址首碼 (對於執行中範例，我們會使用 192.168.11.64/29)。 使用下列命令來建立 Global Reach 連線：

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix "__.__.__.__/29" -AuthorizationKey "########-####-####-####-############"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

執行上述命令後，需要數分鐘的時間建立備援的 Global Reach 連線。

### <a name="verify-global-reach"></a>驗證 Global Reach

下表顯示 Contoso Ltd. 在設定 Global Reach 之後，ExpressRoute 的主要私人對等互連路由表。

[![8]][8]

下表顯示 Fabrikam Inc. 在設定 Global Reach 之後，ExpressRoute 的主要私人對等互連路由表。

[![9]][9]

在上述表格中，我們看到所有預期的「網路」前置詞，並列出適當的「下一個躍點」。

您可在上面看到 [取得路由表] 刀鋒視窗，其可在 Azure 入口網站中 ExpressRoute 線路的 [私人對等互連] 之下存取。 您也可以使用下列 PowerShell 命令列出 ExpressRoute 路由表：

```powershell
Get-AzExpressRouteCircuitRouteTable -DevicePath 'primary' -ExpressRouteCircuitName "Your_circuit_name" -PeeringType AzurePrivatePeering -ResourceGroupName "Your_resource_group"
```

若要查看次要 MSEE 的路由表，請將上述命令中的 'primary' 替換為關鍵字 'secondary'。

此外，讓我們從不同的網路 Ping 不同的目的地，以驗證資料平面連線能力。 下列三個 Ping 可驗證從 Fabrikam 內部部署網路到 Contoso 內部部署網路、Contoso Azure VNet 和 Fabrikam Azure VNet 的資料平面連線能力。


    C:\Users\FabrikamUser>ping 10.1.11.10
    
    Pinging 10.1.11.10 with 32 bytes of data:
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    
    Ping statistics for 10.1.11.10:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 69ms, Maximum = 69ms, Average = 69ms
    
    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=82ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=81ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 82ms, Average = 78ms
    
    C:\Users\FabrikamUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=2ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 2ms, Maximum = 3ms, Average = 2ms


下列兩個 Ping 可驗證從 Contoso 內部部署網路到 Contoso Azure VNet 和 Fabrikam Azure VNet 的資料平面連線能力。

    C:\Users\ContosoUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=6ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 5ms, Maximum = 6ms, Average = 5ms
    
    C:\Users\ContosoUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=74ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 73ms, Maximum = 74ms, Average = 73ms

下面的 Ping 可驗證從 Fabrikam Azure VNet 到 Contoso Azure VNet 的資料平面連線能力。

    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=78ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 78ms, Average = 77ms

## <a name="optimization-for-latency-sensitive-traffic"></a>延遲敏感性流量的最佳化

Global Reach 會透過 Microsoft Edge 裝置路由傳送流量。 對於本文中考量的特定案例，您可藉由啟用 VNet 對等互連，達到更理想的 VNet 間路由。 同樣地，透過可提供更直接網站間 WAN 連線的服務提供者，可讓您達到更理想的內部部署網路間路由。 在此情況下，您可使用 Global Reach 路由作為這些連線的容錯回復選項。 

## <a name="next-steps"></a>後續步驟

Global Reach 會依照國家/地區在各國家/地區推出。 若要查看您想要的國家/地區是否可使用 Global Reach，請參閱 [ExpressRoute Global Reach][Global Reach]。

<!--Image References-->
[1]: ./media/expressroute-globalreach-applicationscenario/PreMergerScenario.png "應用程式案例"
[2]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Premerger.png "合併前的 Contoso ExpressRoute 路由表"
[3]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Premerger.png "合併前的 Fabrikam ExpressRoute 路由表"
[4]: ./media/expressroute-globalreach-applicationscenario/S2SVPN-Solution.png "讓內部部署網路互連"
[5]: ./media/expressroute-globalreach-applicationscenario/ExR-XConnect-Solution.png "Express Route 交叉連線"
[6]: ./media/expressroute-globalreach-applicationscenario/VNet-peering-Solution.png "VNet 對等互連"
[7]: ./media/expressroute-globalreach-applicationscenario/GlobalReach-Solution.png "Global Reach"
[8]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Postmerger.png "內含 Global Reach 的 Contoso ExpressRoute 路由表"
[9]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Postmerger.png "內含 Global Reach 的 Fabrikam ExpressRoute 路由表"

<!--Link References-->
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach





