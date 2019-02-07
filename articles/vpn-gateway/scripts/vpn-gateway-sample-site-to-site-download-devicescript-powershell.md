---
title: Azure PowerShell 指令碼範例 - 下載裝置組態範本 | Microsoft Docs
description: 下載裝置組態範本。
services: vpn-gateway
documentationcenter: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 04/17/2018
ms.author: alzam
ms.openlocfilehash: 1f89d6a33346abc8a84f5bc5c0e46e5431fb80ec
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55510931"
---
# <a name="download-vpn-device-template-using-powershell"></a>使用 PowerShell 下載 VPN 裝置範本

此指令碼會下載連線的 VPN 裝置範本


```azurepowershell-interactive
# Declare variables
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration template for the connection
Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG `
-DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="clean-up-resources"></a>清除資源

當您不再需要所建立的資源時，請使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令來刪除資源群組。 這會刪除資源群組及其包含的所有資源。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立部署。 下表中的每個項目都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice](/powershell/module/azurerm.network/Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice) | 列出所有可用的 VPN 裝置模型和版本。 |
| [Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript](/powershell/module/azurerm.network/Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript) | 下載連線的組態範本。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。
