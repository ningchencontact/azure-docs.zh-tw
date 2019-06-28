---
title: 部署多個公用 IP 位址使用 Azure PowerShell 的 Azure 防火牆
description: 在本文中，您會學習如何部署多個公用 IP 位址使用 Azure PowerShell 的 Azure 防火牆。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/21/2019
ms.author: victorh
ms.openlocfilehash: 9ec37197376c815c4fb9072164520a707b02be2b
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312732"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>部署多個公用 IP 位址使用 Azure PowerShell 的 Azure 防火牆

> [!IMPORTANT]
> 具有多個公用 IP 位址的 azure 防火牆目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以部署使用最多 600 的公用 IP 位址的 Azure 防火牆。

這項功能適用於下列案例：

- **DNAT** -您可以將多個標準連接埠執行個體轉譯到後端伺服器。 例如，如果您有兩個公用 IP 位址，您可以轉譯這兩個 IP 位址的 TCP 連接埠 3389 (RDP)。
- **SNAT** -其他連接埠可供連出 SNAT 連線，降低潛在的 SNAT 連接埠耗盡。 此時，Azure 防火牆會隨機選擇的來源公用 IP 位址將用於連線。 如果您有任何下游的篩選條件，就在您網路上，您需要允許您的防火牆與相關聯的所有公用 IP 位址。

下列 Azure PowerShell 範例顯示如何新增、 移除和設定 Azure 防火牆的公用 IP 位址。

> [!NOTE]
> 公開預覽期間，如果您新增或移除公用 IP 位址執行的防火牆，現有的輸入的連線，使用 DNAT 規則可能無法運作 40 120 秒。 您無法移除指派給防火牆，除非防火牆已解除配置或已刪除的第一個公用 IP 位址。

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>將公用 IP 位址新增至現有的防火牆

在此範例中，公用 IP 位址*azFwPublicIp1*為附加至防火牆。

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>移除現有的防火牆中的公用 IP 位址

在此範例中，公用 IP 位址*azFwPublicIp1*為從防火牆中斷連結。

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>建立具有兩個或多個公用 IP 位址的防火牆

此範例會建立防火牆來連接到虛擬網路*vnet*兩個公用 IP 位址。

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

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="next-steps"></a>後續步驟

* [教學課程：監視 Azure 防火牆記錄](./tutorial-diagnostics.md)
