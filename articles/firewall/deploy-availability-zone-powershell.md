---
title: 部署使用 Azure PowerShell 搭配可用性區域的 Azure 防火牆
description: 在本文中，您會學習如何部署使用 Azure PowerShell Azure 防火牆搭配可用性區域。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: 56958eedceeb4602589d65d5e0eb7b10e8a9ff2d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704004"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>部署使用 Azure PowerShell Azure 防火牆搭配可用性區域

Azure 防火牆可在部署期間進行設定，以跨越多個可用性區域來增加可用性。

這項功能適用於下列案例：

- 您可以增加可用性，可達 99.99%執行時間。 如需詳細資訊，請參閱 Azure 防火牆[服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/)。 選取兩個或多個可用性區域時，會提供 99.99% 運作時間 SLA。
- 您也可以單純基於鄰近性而將 Azure 防火牆關聯到特定區域，並使用服務標準的 99.95% SLA。

如需有關 Azure 防火牆可用性區域的詳細資訊，請參閱[什麼是 Azure 防火牆？](overview.md)

下列 Azure PowerShell 範例會示範如何部署使用可用性區域的 Azure 防火牆。

## <a name="create-a-firewall-with-availability-zones"></a>使用可用性區域中建立的防火牆規則

此範例會在區域 1、 2 和 3 中建立防火牆。

建立標準公用 IP 位址時，不指定任何特定的區域。 這是依預設建立區域備援的 IP 位址。 在所有區域或單一區域中，可以設定標準公用 IP 位址。

務必知道，因為您不能有區域 1 中的防火牆和 IP 位址，區域 2 中。 但您可以在區域 1 中的防火牆和所有的區域中的 IP 位址或防火牆和 IP 位址相同的單一區域的鄰近性用途。

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1)
  -Zone 1,2,3
```

## <a name="next-steps"></a>後續步驟

- [教學課程：監視 Azure 防火牆記錄](./tutorial-diagnostics.md)
