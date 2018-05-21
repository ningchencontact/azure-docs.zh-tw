---
title: 快速入門 - 使用 Azure CLI 2.0 建立 Linux VM | Microsoft Docs
description: 在本快速入門中，您會了解如何使用 Azure CLI 2.0 來建立 Linux 虛擬機器
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 1c45f8f010d69337d21fce327933990a573988a4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli-20"></a>快速入門：使用 Azure CLI 2.0 建立 Linux 虛擬機器

Azure CLI 2.0 用於從命令列或在指令碼中建立和管理 Azure 資源。 本快速入門說明如何使用 Azure CLI 2.0，在 Azure 中部署執行 Ubuntu 的 Linux 虛擬機器 (VM)。 若要查看作用中的 VM，接著要以 SSH 連線至 VM，並安裝 NGINX 網頁伺服器。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本快速入門會要求您執行 Azure CLI 2.0.30 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#az_group_create) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>建立虛擬機器

使用 [az vm create](/cli/azure/vm#az_vm_create) 命令來建立 VM。

下列範例會建立名為 myVM 的 VM，新增名為 azureuser 的使用者帳戶，而如果預設金鑰位置 (~/.ssh) 還沒有這些金鑰，則會加以產生。 若要使用一組特定金鑰，請使用 `--ssh-key-value` 選項：

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

建立虛擬機器和支援資源需要幾分鐘的時間。 下列範例輸出顯示 VM 建立作業成功。

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

請注意 VM 輸出中您自己的 `publicIpAddress`。 後續步驟會使用此位址來存取 VM。

## <a name="open-port-80-for-web-traffic"></a>針對 Web 流量開啟連接埠 80

根據預設，只有在 Azure 中部署 Linux VM 時才會開啟 SSH 連線。 使用 [az vm open-port](/cli/azure/vm#az_vm_open_port) 開啟 TCP 連接埠 80 以供搭配 NGINX 網頁伺服器使用：

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>連線至虛擬機器

如常透過 SSH 連線至您的 VM。 以您 VM 的公用 IP 位址取代 **publicIpAddress**，如下您 VM 先前的輸出所示：

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>安裝 Web 伺服器

若要查看作用中的 VM，請安裝 NGINX 網頁伺服器。 若要更新套件來源並安裝最新的 NGINX 套件，請從 SSH 工作階段執行下列命令：

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

完成時，`exit` SSH 工作階段。

## <a name="view-the-web-server-in-action"></a>檢視作用中的網頁伺服器

在已安裝 NGINX 且現在已開放從網際網路連往 VM 的連接埠 80 後，請使用所選的網頁瀏覽器來檢視預設的 NGINX 歡迎頁面。 使用上一個步驟所取得 VM 的公用 IP 位址。 下列範例示範預設的 NGINX 網站：

![預設 NGINX 網站](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [az group delete](/cli/azure/group#az_group_delete) 命令來移除資源群組、VM 和所有相關資源。 確定您已結束 SSH 工作階段並返回您的 VM，然後將資源刪除，如下所示：

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已部署簡單的虛擬機器、對網路流量開啟網路連接埠，以及安裝基本的網頁伺服器。 若要深入了解 Azure 虛擬機器，請繼續 Linux VM 的教學課程。


> [!div class="nextstepaction"]
> [Azure Linux 虛擬機器教學課程](./tutorial-manage-vm.md)
