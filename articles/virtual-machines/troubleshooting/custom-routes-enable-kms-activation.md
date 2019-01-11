---
title: 使用 Azure 自訂路由透過強制通道啟用 KMS 啟用 | Microsoft Docs
description: 示範如何使用 Azure 自訂路由在 Azure 中透過強制通道啟用 KMS 啟用。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: f1e2ab6a954361a7807d78dc2baf5d24af52a679
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/27/2018
ms.locfileid: "53797332"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>強制通道案例中的 Windows 啟用失敗

本文說明如何解決在站對站 VPN 連線或 ExpressRoute 案例中啟用強制通道時可能遇到的 KMS 啟用問題。

## <a name="symptom"></a>徵狀

您在 Azure 虛擬網路子網路上啟用[強制通道](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)，以將所有網際網路繫結流量導向回到您的內部部署網路。 在此案例中，執行 Windows Server 2012 R2 或更新版本的 Azure 虛擬機器 (VM) 可以成功啟動 Windows。 不過，執行舊版 Windows 的 VM 無法啟動 Windows。 

## <a name="cause"></a>原因

Azure Windows VM 需要連接到 Azure KMS 伺服器以進行 Windows 啟用。 啟用要求啟用要求都必須來自 Azure 的公用 IP 位址。 在強制通道的案例中，啟用會失敗，因為啟用要求來自內部部署網路而不是 Azure 的公用 IP。 

## <a name="solution"></a>解決方法

若要解決此問題，請使用 Azure 自訂路由將啟用流量路由到 Azure KMS 伺服器 (23.102.135.246)。 

IP 位址 23.102.135.246 是 Azure 全域雲端 KMS 伺服器的 IP 位址。 其 DNS 名稱是 kms.core.windows.net。 如果您使用其他 Azure 平台 (例如 Azure Germany)，則必須使用對應 KMS 伺服器的 IP 位址。 如需詳細資訊，請參閱下列表格：

|平台| KMS DNS|KMS IP|
|------|-------|-------|
|Azure 全域|kms.core.windows.net|23.102.135.246|
|Azure Germany|kms.core.cloudapi.de|51.4.143.248|
|Azure US Government|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure China 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


若要新增自訂的路由，請遵循下列步驟：

### <a name="for-resource-manager-vms"></a>針對 Resource Manager VM

1. 開啟 Azure PowerShell，然後[登入您的 Azure 訂用帳戶](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。
2. 執行下列命令：

    ```powershell
    # First, we will get the virtual network hosts the VMs that has activation problems. In this case, I get virtual network ArmVNet-DM in Resource Group ArmVNet-DM

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, we create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out

    $RouteTable = New-AzureRmRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzureRmRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzureRmRouteTable -RouteTable $RouteTable
    ```
3. 移至有啟動問題的 VM，使用 [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) 測試它是否可以連線到 KMS 伺服器：

        psping kms.core.windows.net:1688

4. 嘗試啟動 Windows 並查看問題是否已解決。

### <a name="for-classic-vms"></a>適用於傳統 VM

1. 開啟 Azure PowerShell，然後[登入您的 Azure 訂用帳戶](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。
2. 執行下列命令：

    ```powershell
    # First, we will create a new route table
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the routetable that was created
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply KMS route table to the subnet that host the problem VMs (in this case, I will apply it to the subnet named Subnet-1)
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. 移至有啟動問題的 VM，使用 [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) 測試它是否可以連線到 KMS 伺服器：

        psping kms.core.windows.net:1688

4. 嘗試啟動 Windows 並查看問題是否已解決。

## <a name="next-steps"></a>後續步驟

- [KMS 用戶端安裝識別碼](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [檢閱並選取啟用方法](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)