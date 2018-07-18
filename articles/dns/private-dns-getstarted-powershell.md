---
title: 利用 PowerShell 開始使用 Azure DNS 私人區域 | Microsoft Docs
description: 了解如何在 Azure DNS 中建立私人 DNS 區域和記錄。 這份逐步指南將引導您使用 PowerShell 建立和管理第一個私人 DNS 區域和記錄。
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: d9e5c9b8caa5349fbcda9b71f7524fb9e99b976c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
ms.locfileid: "30177649"
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>利用 PowerShell 開始使用 Azure DNS 私人區域

本文將逐步引導您使用 Azure PowerShell 建立第一個私人 DNS 區域和記錄。

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

DNS 區域用來裝載特定網域的 DNS 記錄。 若要開始將網域裝載到 Azure DNS 中，您必須建立該網域名稱的 DNS 區域。 接著在此 DNS 區域內，建立網域的每筆 DNS 記錄。 若要將私人 DNS 區域發佈至虛擬網路，指定可以在區域內解析記錄的虛擬網路清單。  我們稱之為「解析虛擬網路」。  您也可以指定一個虛擬網路，每當 VM 建立、變更 IP 或被終結時，Azure DNS 就會維護其主機名稱記錄。  我們將此稱為「註冊虛擬網路」。

# <a name="get-the-preview-powershell-modules"></a>取得預覽版 PowerShell 模組
這些指示假設您已經安裝並登入 Azure PowerShell，包括確保您有私人區域功能所需的模組。 

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]

## <a name="create-the-resource-group"></a>建立資源群組

建立 DNS 區域之前，要建立包含 DNS 區域的資源群組。 下列範例顯示此命令。

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-private-zone"></a>建立 DNS 私人區域

使用 `New-AzureRmDnsZone` Cmdlet 搭配值為 "Private" 的 ZoneType 參數來建立 DNS 區域。 下列範例會在 *MyResourceGroup* 資源群組中建立 *contoso.local* DNS 區域，並使 *MyAzureVnet* 虛擬網路能夠使用此 DNS 區域。 使用範例來建立 DNS 區域，並將值替換為您自己的值。

請注意，如果省略 ZoneType 參數，則會將區域建立為公用區域，因此如果您需要建立私人區域，這就是必要參數。 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -ResolutionVirtualNetworkId @($vnet.Id)
```

如果您需要 Azure 自動在區域中建立主機名稱記錄，使用*RegistrationVirtualNetworkId* 參數來代替 *ResolutionVirtualNetworkId*。  註冊虛擬網路會自動啟用以供解析。

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -RegistrationVirtualNetworkId @($vnet.Id)
```

## <a name="create-a-dns-record"></a>建立 DNS 記錄

您可以使用 `New-AzureRmDnsRecordSet` Cmdlet 來建立記錄集。 下列範例會在資源群組 "MyResourceGroup" 中的 DNS 區域 "contoso.local" 中，建立具有相對名稱 "db" 的記錄。 記錄集的完整名稱是 "db.contoso.local"。 記錄類型為 'A'，IP 位址是 "10.0.0.4"，TTL 為 3600 秒。

```powershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.0.0.4")
```

關於其他記錄類型、具有多個記錄的記錄集和修改現有的記錄，請參閱[使用 Azure PowerShell 管理 DNS 記錄和記錄集](dns-operations-recordsets.md)。 

## <a name="view-records"></a>檢視記錄

若要列出區域中的 DNS 記錄，請使用︰

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyResourceGroup
```

# <a name="list-dns-private-zones"></a>列出 DNS 私人區域

您可以從 `Get-AzureRmDnsZone` 中省略區域名稱，以列舉資源群組中的所有區域： 此作業會傳回一系列的區域物件。

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

您可以從 `Get-AzureRmDnsZone` 中省略區域名稱和資源群組名稱，以列舉 Azure 訂用帳戶中的所有區域。

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-private-zone"></a>更新 DNS 私人區域

您可以使用 `Set-AzureRmDnsZone`變更 DNS 區域資源。 這個 Cmdlet 不會更新區域內的任何 DNS 記錄集 (請參閱[如何管理 DNS 記錄](dns-operations-recordsets.md))。 它只用來更新區域資源本身的屬性。 可寫入的區域屬性目前受限於[區域資源的 Azure Resource Manager「標記」](dns-zones-records.md#tags)，以及私人區域的 'RegistrationVirtualNetworkId' 和 'ResolutionVirtualNetworkId' 參數。

下列範例會取代連結至一個區域 (名為 MyNewAzureVnet 的新區域) 的註冊虛擬網路。

請注意，您不得為了 update 指定 ZoneType 參數 (與 create 不同)。 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -RegistrationVirtualNetworkId @($vnet.Id)
```

下列範例會取代連結至一個區域 (一個名為 "MyNewAzureVnet" 的新區域) 的解析虛擬網路。

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ResolutionVirtualNetworkId @($vnet.Id)
```

## <a name="delete-a-dns-private-zone"></a>刪除 DNS 私人區域

就如同公用區域，您可以使用 `Remove-AzureRmDnsZone` Cmdlet 刪除 DNS 私人區域。

> [!NOTE]
> 刪除 DNS 區域也會刪除該區域內的所有 DNS 記錄。 此作業無法復原。 如果 DNS 區域正在使用中，當該區域遭到刪除時，使用該區域的服務將會失敗。
>
>若要防止區域意外遭到刪除，請參閱[如何保護 DNS 區域和記錄](dns-protect-zones-recordsets.md)。

請使用下列兩種方法之一來刪除 DNS 區域：

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>使用區域名稱和資源群組名稱來指定區域

```powershell
Remove-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>使用 $zone 物件來指定區域

您可以使用 `Get-AzureRmDnsZone` 所傳回的 `$zone` 物件，指定要刪除的區域。

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

區域物件也可以經由管道輸送，而不是當做參數傳遞：

```powershell
Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup | Remove-AzureRmDnsZone

```

## <a name="confirmation-prompts"></a>確認提示

`New-AzureRmDnsZone`、`Set-AzureRmDnsZone` 和 `Remove-AzureRmDnsZone` cmdlets 皆支援確認提示。

如果 `$ConfirmPreference` PowerShell 喜好設定變數的值為 `Medium` 或更低，則 `New-AzureRmDnsZone` 和 `Set-AzureRmDnsZone` 兩者都會顯示確認提示。 由於刪除 DNS 區域可能造成重大影響，所以如果 `$ConfirmPreference` PowerShell 變數的值不是 `None`，`Remove-AzureRmDnsZone` Cmdlet 就會顯示確認提示。

由於 `$ConfirmPreference` 的預設值是 `High`，預設只有 `Remove-AzureRmDnsZone` 會顯示確認提示。

您可以使用 `-Confirm` 參數覆寫目前 `$ConfirmPreference` 設定。 如果您指定 `-Confirm` 或 `-Confirm:$True`，此 cmdlet 在執行前會提示您進行確認。 如果您指定 `-Confirm:$False`，此 cmdlet 不會提示您進行確認。

如需 `-Confirm` 和 `$ConfirmPreference` 的詳細資訊，請參閱[有關喜好設定變數](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables)。


## <a name="delete-all-resources"></a>刪除所有資源

若要刪除這篇文章中建立的所有資源，請採取下列步驟︰

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>後續步驟

若要深入了解私人 DNS 區域，請參閱[使用 Azure DNS 私人網域](private-dns-overview.md)。

請研讀一些常見案例：在 Azure DNS 中可透過私人區域實現的[私人區域案例](./private-dns-scenarios.md)。

若要深入了解在 Azure DNS 中管理 DNS 記錄，請參閱[使用 PowerShell 在 Azure DNS 中管理 DNS 記錄和記錄集](dns-operations-recordsets.md)。

