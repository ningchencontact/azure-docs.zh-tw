---
title: 快速入門 - 使用 Azure 應用程式閘道引導網路流量 - Azure CLI | Microsoft Docs
description: 深入了解如何使用 Azure CLI 建立 Azure 應用程式閘道，以將網路流量導向後端集區中的虛擬機器。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: d14b8c9c752c9d41a42f092662c5f3aa88840dc5
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157712"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>快速入門：使用 Azure 應用程式閘道引導網路流量 - Azure CLI

本快速入門說明如何使用 Azure CLI，快速建立在其後端集區中具有兩個虛擬機器的應用程式閘道。 然後進行測試以確定它正常運作。 您會使用 Azure 應用程式閘道，將接聽程式指派給連接埠、建立規則以及將資源新增至後端集區，來將應用程式網路流量導向至特定資源。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，請執行 Azure CLI 2.0.4 版或更新版本。 若要尋找版本，請執行 **az --version**。 如需安裝或升級的相關資訊，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

在 Azure 中，您可以將相關資源配置到資源群組。 使用 [az group create](/cli/azure/group#az-group-create) 建立資源群組。 

下列範例會在 eastus 位置建立名為 myResourceGroupAG 的資源群組。

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>建立網路資源 

在建立虛擬網路時，應用程式閘道可與其他資源進行通訊。 您建立應用程式閘道時，可以同時建立虛擬網路。 在此範例中您會建立兩個子網路：一個用於應用程式閘道，另一個用於虛擬機器。 應用程式閘道子網路只能包含應用程式閘道。 不允許任何其他資源。

若要建立虛擬網路和子網路，請使用 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create)。 執行 [az network public-ip create](/cli/azure/network/public-ip#az-public-ip-create) 可建立公用 IP 位址。

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-backend-servers"></a>建立後端伺服器

在此範例中，您會建立兩個虛擬機器，供 Azure 作為應用程式閘道的後端伺服器。 

### <a name="create-two-virtual-machines"></a>建立兩部虛擬機器

請在虛擬機器上安裝 [NGINX Web 伺服器](https://docs.nginx.com/nginx/)，以確認已成功建立應用程式閘道。 您可以使用 cloud-init 組態檔來安裝 NGINX，並在 Linux 虛擬機器上執行 "Hello World" Node.js 應用程式。 如需 Cloud-init 的詳細資訊，請參閱 [Azure 中虛擬機器的 Cloud-init 支援](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)。

在 Azure Cloud Shell 中，將下列組態複製並貼到名為 *cloud-init.txt* 的檔案中。 輸入 *editor cloud-init.txt* 以建立檔案。

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

使用 [az network nic create](/cli/azure/network/nic#az-network-nic-create) 建立網路介面。 若要建立虛擬機器，請使用 [az vm create](/cli/azure/vm#az-vm-create)。

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>建立應用程式閘道

使用 [az network application-gateway create](/cli/azure/network/application-gateway#az-application-gateway-create) 建立應用程式閘道。 當您使用 Azure CLI 建立應用程式閘道時，必須指定設定資訊，例如容量、SKU 和 HTTP 設定。 Azure 會隨即新增網路介面的私人 IP 位址，作為應用程式閘道後端集區中的伺服器。

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Azure 建立應用程式閘道最多可能需要 30 分鐘。 建立之後，您可以在 [應用程式閘道] 頁面的 [設定] 區段中檢視下列設定：

- **appGatewayBackendPool**：位於 [後端集區] 頁面上。 它會指定所需的後端集區。
- **appGatewayBackendHttpSettings**：位於 [HTTP 設定] 頁面上。 它會指定應用程式閘道應使用連接埠 80 和 HTTP 通訊協定進行通訊。
- **appGatewayHttpListener**：位於 [接聽程式] 頁面上。 它會指定與 **appGatewayBackendPool** 相關聯的預設接聽程式。
- **appGatewayFrontendIP**：位於 [前端 IP 組態] 頁面上。 它會將 *myAGPublicIPAddress* 指派給 **appGatewayHttpListener**。
- **rule1**：位於 [規則] 頁面上。 它會指定與 **appGatewayHttpListener** 相關聯的預設路由規則。

## <a name="test-the-application-gateway"></a>測試應用程式閘道

雖然 Azure 不需要 NGINX Web 伺服器即可建立應用程式閘道，但您仍會在本快速入門中加以安裝，以確認 Azure 是否已成功建立應用程式閘道。 若要取得新應用程式閘道的公用 IP 位址，請使用 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show)。 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
``` 

將公用 IP 位址複製並貼到瀏覽器的網址列中。
    
![測試應用程式閘道](./media/quick-create-cli/application-gateway-nginxtest.png)

當您重新整理瀏覽器時，您應該會看到第二個 VM 的名稱。

## <a name="clean-up-resources"></a>清除資源

當您不再需要先前為應用程式閘道建立的資源時，請使用 [az group delete](/cli/azure/group#az-group-delete) 命令移除資源群組。 藉由移除資源群組，您也可以移除應用程式閘道及其所有相關資源。

```azurecli-interactive 
az group delete --name myResourceGroupAG
```
 
## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure CLI 以應用程式閘道管理網路流量](./tutorial-manage-web-traffic-cli.md)

