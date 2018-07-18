---
title: 利用 Azure CLI 2.0 開始使用 Azure DNS 私人區域 | Microsoft Docs
description: 了解如何在 Azure DNS 中建立私人 DNS 區域和記錄。 這份逐步指南將引導您使用 Azure CLI 2.0 建立和管理第一個 DNS 私人區域和記錄。
services: dns
documentationcenter: na
author: KumuD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: d10f3201adb972468d8de7e66a940232ed19562d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
ms.locfileid: "30191444"
---
# <a name="get-started-with-azure-dns-private-zones-using-azure-cli-20"></a>利用 Azure CLI 2.0 開始使用 Azure DNS 私人區域

> [!div class="op_single_selector"]
> * [PowerShell](private-dns-getstarted-powershell.md)
> * [Azure CLI 2.0](private-dns-getstarted-cli.md)

本文將逐步引導您使用跨平台 Azure CLI 2.0 (適用於 Windows、Mac 和 Linux)，建立第一個 DNS 私人區域和記錄。 您也可以使用 Azure PowerShell 來執行這些步驟。

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

DNS 區域用來裝載特定網域的 DNS 記錄。 若要開始將網域裝載到 Azure DNS 中，您必須建立該網域名稱的 DNS 區域。 接著在此 DNS 區域內，建立網域的每筆 DNS 記錄。 若要將私人 DNS 區域發佈至虛擬網路，指定可以在區域內解析記錄的虛擬網路清單。  我們稱之為「解析虛擬網路」。  您也可以指定一個虛擬網路，每當 VM 建立、變更 IP 或被終結時，Azure DNS 就會維護其主機名稱記錄。  我們將此稱為「註冊虛擬網路」。

這些指示假設您已經安裝並登入 Azure CLI 2.0，並且已經安裝支援私人區域所需的 CLI 擴充功能。 如需說明，請參閱[如何使用 Azure CLI 2.0 管理 DNS 區域](dns-operations-dnszones-cli.md)。

## <a name="to-installuse-azure-dns-private-zones-feature-public-preview"></a>安裝/使用 Azure DNS 私人區域功能 (公開預覽)
Azure DNS 私人區域功能是透過 Azure CLI 的擴充功能，以公開預覽版本發行。 安裝 “dns” Azure CLI 擴充功能 

```
az extension add --name dns
``` 

## <a name="create-the-resource-group"></a>建立資源群組

建立 DNS 區域之前，會建立資源群組以包含 DNS 區域。 以下顯示命令。

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-private-zone"></a>建立 DNS 私人區域

使用 `az network dns zone create` 命令建立 DNS 私人區域。 若要查看此命令的說明，請輸入 `az network dns zone create --help`。

下列範例會在 MyResourceGroup 資源群組中建立名為 contoso.local 的 DNS 私人區域，並使用 resolution-vnets 參數讓 MyAzureVnet 虛擬網路能夠使用 (連結至) 此 DNS 私人區域。 使用範例來建立 DNS 區域，並將值替換為您自己的值。

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --resolution-vnets MyAzureVnet
```

注意：在上述範例中，"MyAzureVnet" 虛擬網路屬於與私人區域相同的資源群組和訂用帳戶。 如果您需要連結屬於不同資源群組或訂用帳戶的虛擬網路，您必須指定完整的 Azure Resource Manager 識別碼，而不只是針對 --resolution-vnets 參數指定虛擬網路名稱。 

如果您需要 Azure 自動在區域中建立主機名稱記錄，請使用 registration-vnets 參數來代替 resolution-vnets。  註冊虛擬網路會自動啟用以供解析。

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --registration-vnets MyAzureVnet
```

## <a name="create-a-dns-record"></a>建立 DNS 記錄

若要建立 DNS 記錄，請使用 `az network dns record-set [record type] add-record` 命令。 如需說明，例如 A 記錄，請參閱 `azure network dns record-set A add-record --help`。

下列範例會在資源群組 "MyResourceGroup" 中的 DNS 區域 "contoso.local" 中，建立具有相對名稱 "ip1" 的記錄。 記錄集的完整名稱是 "ip1.contoso.local"。 記錄類型為 "A"，IP 位址是 "10.0.0.1"。

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.local -n ip1 -a 10.0.0.1
```

關於其他記錄類型、具有多個記錄的記錄集、其他 TTL 值和修改現有的記錄，請參閱[使用 Azure CLI 2.0 管理 DNS 記錄和記錄集](dns-operations-recordsets-cli.md)。

## <a name="view-records"></a>檢視記錄

若要列出區域中的 DNS 記錄，請使用︰

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="get-a-dns-private-zone"></a>取得 DNS 私人區域

若要擷取 DNS 私人區域，請使用 `az network dns zone show`。 如需協助，請參閱 `az network dns zone show --help`。

下列範例會從資源群組 MyResourceGroup 傳回 DNS 區域 contoso.local 及其相關聯的資料。 

```azurecli
az network dns zone show --resource-group MyResourceGroup --name contoso.local
```

以下是回應範例。

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.local",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.local",
  "nameServers": null,
  "numberOfRecordSets": 1,
  "registrationVirtualNetworks": [],
  "resolutionVirtualNetworks": [
    {
      "additionalProperties": {},
      "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyAzureVnet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
  "resourceGroup": "MyResourceGroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones",
  "zoneType": "Private"
}
```

請注意，`az network dns zone show` 不會傳回 DNS 記錄。 若要列出 DNS 記錄，請使用 `az network dns record-set list`。


## <a name="list-dns-zones"></a>列出 DNS 區域

若要列舉 DNS 區域，請使用 `az network dns zone list`。 如需協助，請參閱 `az network dns zone list --help`。

指定資源群組只會列出資源群組內的區域︰

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

省略資源群組則會列出訂用帳戶中的所有區域︰

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>更新 DNS 區域

您可以使用 `az network dns zone update`變更 DNS 區域資源。 如需協助，請參閱 `az network dns zone update --help`。

此命令不會更新區域內的任何 DNS 記錄集 (請參閱[如何管理 DNS 記錄](dns-operations-recordsets-cli.md))。 它只用來更新區域資源本身的屬性。 對於私人區域，您可以更新連結到區域的註冊或解析虛擬網路。 

下列範例會示範如何更新連結至 DNS 私人區域的解析虛擬網路。 所指定的新虛擬網路會取代現有連結的解析虛擬網路。

```azurecli
az network dns zone update --resource-group MyResourceGroup --name contoso.local --zone-type Private --resolution-vnets MyNewAzureVnet
```

## <a name="delete-a-dns-zone"></a>刪除 DNS 區域

可以使用 `az network dns zone delete`刪除 DNS 區域。 如需協助，請參閱 `az network dns zone delete --help`。

> [!NOTE]
> 刪除 DNS 區域也會刪除該區域內的所有 DNS 記錄。 此作業無法復原。 如果 DNS 區域正在使用中，當該區域遭到刪除時，使用該區域的服務將會失敗。
>
>若要防止區域意外遭到刪除，請參閱[如何保護 DNS 區域和記錄](dns-protect-zones-recordsets.md)。

此命令會提示您確認。 選擇性的 `--yes` 參數會隱藏這個提示。

下列範例示範如何從資源群組 MyResourceGroup 刪除區域 contoso.local。

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.local
```

## <a name="delete-all-resources"></a>刪除所有資源
 
若要刪除這篇文章中建立的所有資源，請採取下列步驟︰

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure DNS，請參閱 [Azure DNS 概觀](dns-overview.md)。

若要深入了解在 Azure DNS 中管理 DNS 區域，請參閱[使用 Azure CLI 2.0 在 Azure DNS 中管理 DNS 區域](dns-operations-dnszones-cli.md)。

若要深入了解在 Azure DNS 中管理 DNS 記錄，請參閱[使用 Azure CLI 2.0 在 Azure DNS 中管理 DNS 記錄和記錄集](dns-operations-recordsets-cli.md)。
