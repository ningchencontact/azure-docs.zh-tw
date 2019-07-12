---
title: 部署 Azure 虛擬網路-資源管理員範本 （預覽） 中的 IPv6 雙重堆疊應用程式
titlesuffix: Azure Virtual Network
description: 這篇文章示範如何部署 IPv6 雙重堆疊應用程式使用 Azure Resource Manager VM 範本的 Azure 虛擬網路中。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: ae90bc4a12763803f38224d917c4644a68ae7d6b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130874"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---template-preview"></a>部署在 Azure 範本 （預覽） 中的 IPv6 雙重堆疊應用程式

本文提供一份適用於 Azure Resource Manager VM 範本的一部分使用的 IPv6 設定工作。 使用本文中所述的範本來部署包含雙重堆疊虛擬網路使用 IPv4 和 IPv6 子網路、 負載平衡器使用雙重 （IPv4 + IPv6） 前端組態，具有有雙重 IP 的 Nic 的 Vm 的 Azure 中的雙重堆疊 （IPv4 + IPv6） 應用程式組態 」、 「 網路安全性群組和 「 公用 Ip 」。 

## <a name="required-configurations"></a>所需的組態

搜尋範本等節，以查看他們應該進行範本中。

### <a name="ipv6-addressspace-for-the-virtual-network"></a>虛擬網路的 IPv6 addressSpace

若要新增的 [範本] 區段：

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>IPv6 虛擬網路 addressSpace 內的 IPv6 子網路

若要新增的 [範本] 區段：
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>適用於 NIC 的 IPv6 設定

若要新增的 [範本] 區段：
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>IPv6 網路安全性群組 (NSG) 規則

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "ace:cab:deca:deed::/64",
              "destinationAddressPrefix": "cab:ace:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>條件式組態

如果您使用網路虛擬設備，新增 IPv6 路由的路由表中。 否則，此設定是選擇性的。

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "ace:cab:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "deca:cab:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>選用設定

### <a name="ipv6-internet-access-for-the-virtual-network"></a>虛擬網路的 IPv6 網際網路存取

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>IPv6 公用 IP 位址

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>IPv6 前端負載平衡器

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>IPv6 的負載平衡器後端位址集區

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>IPv6 負載平衡器規則，用以將傳入和傳出的連接埠產生關聯

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>範例 JSON 的 VM 範本
按一下 [此處](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/)IPv6 雙重堆疊應用程式使用 Azure Resource Manager 範本的 Azure 虛擬網路中的部署。

## <a name="next-steps"></a>後續步驟

您可以找到有關定價的詳細資料[公用 IP 位址](https://azure.microsoft.com/pricing/details/ip-addresses/)，[網路頻寬](https://azure.microsoft.com/pricing/details/bandwidth/)，或[Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/)。
