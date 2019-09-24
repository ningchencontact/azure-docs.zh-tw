---
title: 使用 Azure CLI 建立 Azure 私用端點 |Microsoft Docs
description: 瞭解 Azure 私用端點
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 46c08f48efc00c1e4d88ceccb680c0cfd1671b2f
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203569"
---
# <a name="create-a-private-endpoint-using-azure-cli"></a>使用 Azure CLI 建立私人端點
私人端點是 Azure 中私用連結的基本建立區塊。 它可讓 Azure 資源（例如虛擬機器（Vm））私下與私人連結資源進行通訊。 在本快速入門中，您將瞭解如何使用 Azure CLI，在虛擬網路、具有私人端點的 SQL Database 伺服器上建立 VM。 然後，您可以存取 VM，並安全地存取私人連結資源（在此範例中為私用 Azure SQL Database 伺服器）。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您決定改為在本機安裝和使用 CLI，本快速入門會要求您使用 Azure CLI 2.0.28 版或更新版本。 若要尋找您安裝的版本，請執行 `az --version`。 如需安裝或升級的資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

建立任何資源之前，您必須先建立資源群組來裝載虛擬網路。 使用 [az group create](/cli/azure/group) 來建立資源群組。 這個範例會在*westcentralus*位置建立名為*myResourceGroup*的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>建立虛擬網路
使用[az Network vnet create](/cli/azure/network/vnet)來建立虛擬網路。 這個範例會建立名為*myVirtualNetwork*的預設虛擬網路，其中包含一個名為*mySubnet*的子網：

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```
## <a name="disable-subnet-private-endpoint-policies"></a>停用子網私人端點原則 
Azure 會將資源部署到虛擬網路內的子網，因此您必須建立或更新子網，以停用私人端點網路原則。 使用[az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update)來更新名為 * mySubnet * * 的子網設定：

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>建立 VM 
使用 az vm create 建立 VM。 出現提示時，請提供要做為 VM 登入認證使用的密碼。 這個範例會建立名為 *myVm*的 VM： 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 請記下 VM 的公用 IP 位址。 在下一個步驟中，您將使用此位址來從網際網路連線至 VM。

## <a name="create-a-sql-database-server"></a>建立 SQL Database 伺服器 
使用 az SQL Server create 命令來建立 SQL Database 伺服器。 請記住，您 SQL Server 的名稱在整個 Azure 中必須是唯一的，因此請以您自己唯一的值取代括弧中的預留位置值： 

```azurecli-interactive
# Create a logical server in the resource group 
az sql server create \ 
    --name "myserver"\ 
    --resource-group myResourceGroup \ 
    --location WestUS \ 
    --admin-user "sqladmin" \ 
    --admin-password "CHANGE_PASSWORD_1" 
 
# Create a database in the server with zone redundancy as false 
az sql db create \ 
    --resource-group myResourceGroup  \ 
    --server myserver \ 
    --name mySampleDatabase \ 
    --sample-name AdventureWorksLT \ 
    --edition GeneralPurpose \ 
    --family Gen4 \ 
    --capacity 1 
```

請注意，SQL Server 識別碼類似 ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.```于您將在下一個步驟中使用 SQL Server 識別碼。 

## <a name="create-the-private-endpoint"></a>建立私用端點 
在您的虛擬網路中建立 SQL Database 伺服器的私用端點： 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<SQL Server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```
## <a name="configure-the-private-dns-zone"></a>設定私人 DNS 區域 
建立 SQL Database 伺服器網域的私人 DNS 區域，並建立與虛擬網路的關聯連結。 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.database.windows.net" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.database.windows.net"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the SQL server name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>從網際網路連線至 VM

從網際網路連線至 VM *myVm* ，如下所示：

1. 在入口網站的搜尋列中，輸入*myVm*。

1. 選取 [連線] 按鈕。 選取 [連線] 按鈕之後，隨即會開啟 [連線至虛擬機器]。

1. 選取 [下載 RDP 檔案]。 Azure 會建立一個「遠端桌面通訊協定」( *.rdp*) 檔案，並下載至您的電腦。

1. 開啟下載的 .rdp * 檔案。

    1. 如果出現提示，請選取 [連接]。

    1. 輸入您在建立 VM 時指定的使用者名稱和密碼。

        > [!NOTE]
        > 您可能需要選取 [其他選擇] > [使用不同的帳戶]，以指定您在建立 VM 時輸入的認證。

1. 選取 [確定]。

1. 您可能會在登入過程中收到憑證警告。 如果您收到憑證警告，請選取 [是] 或 [繼續]。

1. 當 VM 桌面出現之後，將它最小化以回到您的本機桌面。  

## <a name="access-dql-database-server-privately-from-the-vm"></a>從 VM 私下存取 DQL 資料庫伺服器

在本節中，您會使用私人端點從 VM 連接到 SQL Database 伺服器。

 1. 在 *myVM*的遠端桌面中，開啟 PowerShell。
 2. 輸入 nslookup myserver.database.windows.net  ，您會收到類似下面的訊息： 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    myserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  myserver.database.windows.net 
```
 3. 安裝 SQL Server Management Studio 
 4. 在 [連線到伺服器] 中，輸入或選取這項資訊：伺服器類型：選取 [資料庫引擎]。
 伺服器名稱：選取 [myserver.database.windows.net 使用者名稱]：輸入在建立期間提供的使用者名稱。
 密碼：輸入建立期間所提供的密碼。
 記住密碼：選取 [是]。
 
 5. 選取 **[連線]** 。
 6. 流覽左側功能表中的 [**資料庫**]。
 7. 也從*mydatabase*建立或查詢資訊
 8. 關閉對*myVm*的遠端桌面連線。

## <a name="clean-up-resources"></a>清除資源 
若不再需要，您可以使用 az group delete 來移除資源群組及其擁有的所有資源： 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>後續步驟
- 深入瞭解[Azure 私人連結](private-link-overview.md)
 
