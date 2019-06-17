---
title: 使用 Azure CLI 建立 Azure DNS 私人區域
description: 在此程序中，您可以建立並測試 Azure DNS 中的私人 DNS 區域和記錄。 這份逐步指南將引導您使用 Azure CLI，來建立和管理第一個私人 DNS 區域和第一筆記錄。
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 6/13/2019
ms.author: victorh
ms.openlocfilehash: d882a9c40efc5e9bcb1a5e1c02f1ac73970d57db
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076417"
---
# <a name="create-an-azure-dns-private-zone-using-the-azure-cli"></a>使用 Azure CLI 建立 Azure DNS 私人區域

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

此程序將逐步引導您逐步完成使用 Azure CLI 建立您的第一個私人 DNS 區域和記錄。

DNS 區域用來裝載特定網域的 DNS 記錄。 若要開始將網域裝載到 Azure DNS 中，您必須建立該網域名稱的 DNS 區域。 接著在此 DNS 區域內，建立網域的每筆 DNS 記錄。 若要將私人 DNS 區域發佈至虛擬網路，指定可以在區域內解析記錄的虛擬網路清單。  這些稱為*連結*虛擬網路。 啟用自動註冊程序時，Azure DNS 也會更新區域記錄每次虛擬機器建立時，變更其 ' 的 IP 位址，或刪除。

在此程序中，您將了解如何：

> [!div class="checklist"]
> * 建立 DNS 私人區域
> * 建立測試虛擬機器
> * 建立其他的 DNS 記錄
> * 測試私人區域

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

如果您想要的話，可以使用 [Azure PowerShell](private-dns-getstarted-powershell.md) 完成本程序。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>建立資源群組

首先，請建立包含 DNS 區域的資源群組： 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-dns-private-zone"></a>建立 DNS 私人區域

下列範例會建立名為虛擬網路**myAzureVNet**。 然後它會建立名為 DNS 區域**private.contoso.com**中**MyAzureResourceGroup**資源群組中，連結至 DNS 區域**MyAzureVnet**虛擬網路，以及啟用自動註冊。

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefixes 10.2.0.0/24

az network private-dns zone create -g MyAzureResourceGroup \
   -n private.contoso.com

az network private-dns link vnet create -g MyAzureResourceGroup -n MyDNSLink \
   -z private.contoso.com -v myAzureVNet -e true
```

如果您想要建立區域只進行名稱解析 （沒有自動的主機名稱註冊），您可以使用`-e false`參數。

### <a name="list-dns-private-zones"></a>列出 DNS 私人區域

若要列舉 DNS 區域，請使用 `az network private-dns zone list`。 如需協助，請參閱 `az network dns zone list --help`。

指定資源群組只會列出資源群組內的區域︰

```azurecli
az network private-dns zone list \
  -g MyAzureResourceGroup
```

省略資源群組則會列出訂用帳戶中的所有區域︰

```azurecli
az network private-dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>建立測試虛擬機器

現在，請建立兩部虛擬機器，讓您可以測試私人 DNS 區域：

```azurecli
az vm create \
 -n myVM01 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter
```

這需要幾分鐘才能完成。

## <a name="create-an-additional-dns-record"></a>建立其他的 DNS 記錄

若要建立 DNS 記錄，請使用 `az network private-dns record-set [record type] add-record` 命令。 如需說明，例如 A 記錄，請參閱 `az network private-dns record-set A add-record --help`。

 下列範例會在資源群組 **MyAzureResourceGroup** 中的 DNS 區域 **private.contoso.com** 中，建立具有相對名稱 **db** 的記錄。 記錄集的完整名稱是 **db.private.contoso.com**。 記錄類型為 "A"，IP 位址是 "10.2.0.4"。

```azurecli
az network private-dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z private.contoso.com \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>檢視 DNS 記錄

若要列出區域中的 DNS 記錄，請執行︰

```azurecli
az network private-dns record-set list \
  -g MyAzureResourceGroup \
  -z private.contoso.com
```

## <a name="test-the-private-zone"></a>測試私人區域

現在，您可以測試 **private.contoso.com** 私人區域的名稱解析。

### <a name="configure-vms-to-allow-inbound-icmp"></a>設定 VM，以允許輸入 ICMP

您可以使用 Ping 命令來測試名稱解析。 因此，請在兩部虛擬機器上設定防火牆，以允許輸入的 ICMP 封包。

1. 連線至 myVM01，然後以系統管理員權限開啟 Windows PowerShell 視窗。
2. 執行以下命令：

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

針對 myVM02 重複。

### <a name="ping-the-vms-by-name"></a>依照名稱 Ping VM

1. 從 myVM02 Windows PowerShell 命令提示字元中，使用自動註冊的主機名稱 Ping myVM01：

   ```
   ping myVM01.private.contoso.com
   ```

   您應該會看到如下所示的輸出：

   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. 現在 Ping 您先前建立的 **db** 名稱：

   ```
   ping db.private.contoso.com
   ```

   您應該會看到如下所示的輸出：

   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>刪除所有資源

當不再需要刪除**MyAzureResourceGroup**来刪除此程序中建立的資源的資源群組。

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>後續步驟

在此程序中，您可以部署私人 DNS 區域，建立 DNS 記錄，並測試區域。
接下來，您可以深入了解私人 DNS 區域。

> [!div class="nextstepaction"]
> [將 Azure DNS 用於私人網域](private-dns-overview.md)
