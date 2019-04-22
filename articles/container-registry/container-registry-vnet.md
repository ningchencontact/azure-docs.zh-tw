---
title: 部署到虛擬網路的 Azure container registry
description: 允許存取至 Azure container registry，只能從 Azure 虛擬網路中的資源或公用 IP 位址範圍。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.openlocfilehash: 15b67218b129b5e017e67651587c389af412d7a1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59268399"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Azure container registry 使用 Azure 虛擬網路或防火牆規則限制存取

[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)提供安全的私人網路，為您的 Azure 和內部部署資源。 藉由部署到 Azure 虛擬網路私人 Azure 容器登錄，您可以確保只有在虛擬網路中的資源存取登錄。 跨單位案例中，您也可以設定防火牆規則以允許僅來自特定 IP 位址的登錄存取權。

本文將說明兩個案例，來建立網路存取規則，以限制存取權的 Azure container registry： 從虛擬機器部署在相同的網路，進出 VM 的公用 IP 位址。

> [!IMPORTANT]
> 此功能目前在預覽階段，但[有某些限制](#preview-limitations)。 若您同意[補充的使用規定][terms-of-use]，即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。
>

## <a name="preview-limitations"></a>預覽限制

* 只有**Premium**容器登錄庫可以使用網路存取規則來設定。 如需登錄的服務層的詳細資訊，請參閱[Azure Container Registry Sku](container-registry-skus.md)。 

* 只有[Azure Kubernetes Service](../aks/intro-kubernetes.md)叢集或 Azure[虛擬機器](../virtual-machines/linux/overview.md)可以做為主機用來存取虛擬網路中的容器登錄庫。 *目前不支援其他 Azure 服務，包括 Azure 容器執行個體。*

* [ACR 工作](container-registry-tasks-overview.md)作業目前不支援在容器登錄部署至虛擬網路。

* 每個登錄最多 100 個虛擬網路規則，可支援。

## <a name="prerequisites"></a>必要條件

* 若要使用 Azure CLI 本文中，Azure CLI 版本 2.0.58 中的步驟，或更新版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

* 如果您還沒有容器登錄庫，建立一個 (需要 Premium SKU) 和推播範例映像時，例如`hello-world`從 Docker Hub。 例如，使用[Azure 入口網站][ quickstart-portal]或[Azure CLI] [ quickstart-cli]建立登錄庫。 

## <a name="about-network-rules-for-a-container-registry"></a>關於 container registry 的網路規則

預設的 Azure container registry 會接受透過網際網路從任何網路上主機的連線。 透過虛擬網路中，您可以允許只有 Azure 資源，例如 AKS 叢集或 Azure VM，安全地存取登錄中，而不需要跨網路界限。 您也可以設定網路防火牆規則，以允許清單特定的公用網際網路 IP 位址範圍。 

若要限制登錄存取權，請先將變更登錄的預設動作，因此它會拒絕所有網路連線。 然後，新增網路存取規則。 用戶端授與存取權，透過網路規則必須繼續[向容器登錄](https://docs.microsoft.com/azure/container-registry/container-registry-authentication)和授權來存取資料。

### <a name="service-endpoint-for-subnets"></a>子網路的服務端點

若要允許來自子網路存取虛擬網路中，您需要新增[服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)Azure Container Registry 服務。 

多租用戶的服務，例如 Azure Container Registry，使用一組 IP 位址為所有客戶。 服務端點會指派一個端點，來存取登錄。 此端點可讓流量有最佳路由資源透過 Azure 骨幹網路。 虛擬網路和子網路的身分識別也會隨著每項要求傳輸。

### <a name="firewall-rules"></a>防火牆規則

對於 IP 網路規則，提供允許的網際網路位址範圍，例如使用 CIDR 表示法*16.17.18.0/24*或個別的 IP 位址，例如*16.17.18.19*。 IP 網路規則只允許*公開*網際網路 IP 位址。 IP 規則中不允許保留私人網路 （如 RFC 1918 中所定義） 的 IP 位址範圍。

## <a name="create-a-docker-enabled-virtual-machine"></a>建立啟用 Docker 的虛擬機器

在本文中，使用已啟用 Docker 的 Ubuntu VM 來存取 Azure container registry。 若要在登錄中使用 Azure Active Directory 驗證，同時安裝[Azure CLI] [ azure-cli]在 VM 上。 如果您已經有 Azure 虛擬機器，略過此步驟中建立。

您可以使用相同的資源群組，您的虛擬機器和您的容器登錄。 此安裝程式簡化清除結束，但不需要。 如果您選擇建立的虛擬機器和虛擬網路的個別的資源群組，執行[az 群組建立][az-group-create]。 下列範例會建立名為的資源群組*myResourceGroup*中*westcentralus*位置：

```azurecli
az group create --name myResourceGroup --location westus
```

現在，將預設值部署 Ubuntu Azure 虛擬機器[az vm 建立][az-vm-create]。 下列範例會建立名為的 VM *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

系統需要花幾分鐘的時間來建立 VM。 命令完成之後，請記下 Azure CLI 所顯示的 `publicIpAddress`。 使用此位址來建立 SSH 連線到 VM，以及 （選擇性） 更新版本的安裝程式的防火牆規則。

### <a name="install-docker-on-the-vm"></a>在 VM 上安裝 Docker

在 VM 執行之後，與 VM 建立 SSH 連線。 以您 VM 的公用 IP 位址取代 *publicIpAddress*。

```bash
ssh azureuser@publicIpAddress
```

執行下列命令以安裝在 Ubuntu VM 上的 Docker:

```bash
sudo apt install docker.io -y
```

安裝之後，執行下列命令確認 Docker 在 VM 上正常執行：

```bash
sudo docker run -it hello-world
```

輸出：

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>安裝 Azure CLI

請遵循[使用 apt 安裝 Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) 中的步驟在您的 Ubuntu 虛擬機器上安裝 Azure CLI。 在本文中，請確定您在安裝版本 2.0.58 或更新版本。

結束 SSH 連線。

## <a name="allow-access-from-a-virtual-network"></a>允許從虛擬網路的存取

在本節中，設定您的容器登錄，以允許來自子網路的存取在 Azure 虛擬網路中。 提供使用 Azure CLI 和 Azure 入口網站的對等步驟。

### <a name="allow-access-from-a-virtual-network---cli"></a>允許從虛擬網路-CLI 存取

#### <a name="add-a-service-endpoint-to-a-subnet"></a>將服務端點新增至子網路

當您建立 VM 時，Azure 預設會在相同的資源群組中建立虛擬網路。 虛擬網路的名稱根據虛擬機器的名稱。 例如，如果您命名您的虛擬機器*myDockerVM*，預設虛擬網路名稱*myDockerVMVNET*，與子網路名為*myDockerVMSubnet*。 在 Azure 入口網站或使用，請確認這[az 網路 vnet 清單][ az-network-vnet-list]命令：

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
```

輸出：

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

使用[az 網路的 vnet 子網路更新][ az-network-vnet-subnet-update]命令，將新增**Microsoft.ContainerRegistry**至子網路的服務端點。 取代您的虛擬網路和子網路，在下列命令中的名稱：

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

使用[az 網路的 vnet 子網路顯示][ az-network-vnet-subnet-show]命令來擷取子網路的資源識別碼。 您需要在稍後步驟中設定網路存取規則。

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

輸出：

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>變更登錄的預設網路存取

根據預設，Azure container registry 會允許任何網路上主機的連線。 若要限制對選取的網路存取，變更預設動作來拒絕存取。 使用下列登錄的名稱來替代[az acr update] [ az-acr-update]命令：

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>將網路規則新增至登錄

使用[az acr 網路規則新增][ az-acr-network-rule-add]命令，將網路規則新增至您的登錄，以允許從 VM 的子網路的存取。 取代容器登錄的名稱和下列命令中的子網路的資源識別碼： 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

若要繼續[確認登錄的存取權](#verify-access-to-the-registry)。

### <a name="allow-access-from-a-virtual-network---portal"></a>允許存取，從虛擬網路-入口網站

#### <a name="add-service-endpoint-to-subnet"></a>將服務端點新增至子網路

當您建立 VM 時，Azure 預設會在相同的資源群組中建立虛擬網路。 虛擬網路的名稱根據虛擬機器的名稱。 例如，如果您命名您的虛擬機器*myDockerVM*，預設虛擬網路名稱*myDockerVMVNET*，與子網路名為*myDockerVMSubnet*。

若要新增 Azure Container Registry 服務端點的子網路︰

1. 在頂端的搜尋方塊[Azure 入口網站][azure-portal]，輸入*虛擬網路*。 當搜尋結果中出現**虛擬網路**時加以選取。
1. 從虛擬網路清單中，選取您的虛擬機器部署所在，例如虛擬網路*myDockerVMVNET*。
1. 底下**設定**，選取**子網路**。
1. 選取您的虛擬機器部署所在，這類的子網路*myDockerVMSubnet*。
1. 底下**服務端點**，選取**Microsoft.ContainerRegistry**。
1. 選取 [ **儲存**]。

![將服務端點新增至子網路][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>設定網路存取的登錄

根據預設，Azure container registry 會允許任何網路上主機的連線。 若要限制虛擬網路的存取權：

1. 在入口網站中，瀏覽至您的容器登錄。
1. 底下**設定**，選取**防火牆和虛擬網路**。
1. 若要預設拒絕存取，請選擇允許**所選網路**存取權。 
1. 選取 **將現有的虛擬網路新增**，並選取 虛擬網路和您設定與服務端點的子網路。 選取 [新增] 。
1. 選取 [ **儲存**]。

![設定容器登錄的虛擬網路][acr-vnet-portal]

若要繼續[確認登錄的存取權](#verify-access-to-the-registry)。

## <a name="allow-access-from-an-ip-address"></a>允許來自 IP 位址的存取

在本節中，設定您的容器登錄，以允許來自子網路的存取在 Azure 虛擬網路中。 提供使用 Azure CLI 和 Azure 入口網站的對等步驟。

### <a name="allow-access-from-an-ip-address---cli"></a>允許存取的 IP 位址-CLI

#### <a name="change-default-network-access-to-registry"></a>變更登錄的預設網路存取

如果您尚未這麼做，更新預設為拒絕存取的登錄設定。 使用下列登錄的名稱來替代[az acr update] [ az-acr-update]命令：

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>從登錄移除網路規則

如果您先前已加入網路規則，以允許從 VM 的子網路的存取，請移除子網路的服務端點和網路規則。 Container registry 的名稱與您在先前步驟中擷取的子網路的資源識別碼取代[az acr 網路規則移除][ az-acr-network-rule-remove]命令： 

```azurecli
# Remove service endpoint

az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints ""

# Remove network rule

az acr network-rule remove --name mycontainerregistry --subnet <subnet-resource-id>
```

#### <a name="add-network-rule-to-registry"></a>將網路規則新增至登錄

使用[az acr 網路規則新增][ az-acr-network-rule-add]命令，將網路規則新增至您的登錄，以允許從 VM 的 IP 位址的存取。 取代容器登錄的名稱和下列命令中的虛擬機器的公用 IP 位址。

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

若要繼續[確認登錄的存取權](#verify-access-to-the-registry)。

### <a name="allow-access-from-an-ip-address---portal"></a>允許存取來自 IP 位址-入口網站

#### <a name="remove-existing-network-rule-from-registry"></a>從登錄移除現有的網路規則

如果您先前已加入網路規則，以允許從 VM 的子網路的存取，請移除現有的規則。 如果您想要從不同的 VM 存取登錄，請略過本節。

* 更新 Azure Container registry 移除子網路的服務端點的子網路設定。 

  1. 在  [Azure 入口網站][azure-portal]，瀏覽至您的虛擬機器部署所在的虛擬網路。
  1. 底下**設定**，選取**子網路**。
  1. 選取您的虛擬機器部署所在的子網路。
  1. 底下**服務端點**，移除的核取方塊**Microsoft.ContainerRegistry**。 
  1. 選取 [ **儲存**]。

* 移除 允許存取登錄的子網路的網路規則。

  1. 在入口網站中，瀏覽至您的容器登錄。
  1. 底下**設定**，選取**防火牆和虛擬網路**。
  1. 底下**虛擬網路**，選取虛擬網路的名稱，然後選取**移除**。
  1. 選取 [ **儲存**]。

#### <a name="add-network-rule-to-registry"></a>將網路規則新增至登錄

1. 在入口網站中，瀏覽至您的容器登錄。
1. 底下**設定**，選取**防火牆和虛擬網路**。
1. 如果您尚未這麼做，選擇允許存取權**選取的網路**。 
1. 底下**虛擬網路**，確定已選取任何網路。
1. 底下**防火牆**，輸入 VM 的公用 IP 位址。 或者，您也可以輸入包含 VM 的 IP 位址的 CIDR 標記法的位址範圍。
1. 選取 [ **儲存**]。

![設定容器登錄的防火牆規則][acr-vnet-firewall-portal]

若要繼續[確認登錄的存取權](#verify-access-to-the-registry)。

## <a name="verify-access-to-the-registry"></a>確認登錄的存取權

之後等候幾分鐘的時間來更新組態，請確認 VM 可以存取的容器登錄。 請透過 SSH 連線到您的 VM，並執行[az acr login] [ az-acr-login]命令來登入您的登錄。 

```bash
az acr login --name mycontainerregistry
```

您可以執行登錄作業，例如執行`docker pull`從登錄提取範例映像。 取代適用於您的登錄，加上登錄登入伺服器名稱 （全部小寫） 的映像和標記值：

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker 已成功提取映像的 vm。

此範例示範您可以透過網路存取規則存取私人容器登錄。 不過，從不同的登入主機沒有網路存取規則，設定，無法存取登錄。 如果您嘗試從另一部主機使用登入`az acr login`命令或`docker login`命令時，輸出會與下列類似：

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>還原預設登錄存取

若要還原至預設為允許存取登錄，移除任何已設定的網路規則。 然後設定預設動作，以允許存取。 提供使用 Azure CLI 和 Azure 入口網站的對等步驟。

### <a name="restore-default-registry-access---cli"></a>還原預設登錄存取權-CLI

#### <a name="remove-network-rules"></a>移除網路規則

若要查看您的登錄設定的網路規則的清單，請執行下列[az acr 網路規則清單][ az-acr-network-rule-list]命令：

```azurecli
az acr network-rule list--name mycontainerregistry 
```

針對每個已設定的規則，執行[az acr 網路規則移除][ az-acr-network-rule-remove]命令來移除它。 例如︰

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet

# Remove a rule that allows access for an IP address or CIDR range such as 23.45.1.0/24.

az acr network-rule remove \
  --name mycontainerregistry \
  --ip-address 23.45.1.0/24
```

#### <a name="allow-access"></a>允許存取

使用下列登錄的名稱來替代[az acr update] [ az-acr-update]命令：
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>還原預設登錄存取權-入口網站


1. 在入口網站中，瀏覽至您的容器登錄，然後選取**防火牆和虛擬網路**。
1. 底下**虛擬網路**，選取每個虛擬網路，然後選取**移除**。
1. 底下**防火牆**選取每個位址範圍，然後選取 [刪除] 圖示。
1. 底下**允許從存取**，選取**所有網路**。 
1. 選取 [ **儲存**]。

## <a name="clean-up-resources"></a>清除資源

如果您已經建立所有相同的資源中的 Azure 資源群組，而且不再需要它們，您可以選擇性地使用單一刪除的資源[az 群組刪除](/cli/azure/group)命令：

```azurecli
az group delete --name myResourceGroup
```

若要清除您在入口網站中的資源，請瀏覽至 myResourceGroup 資源群組。 一旦載入資源群組時，按一下**刪除資源群組**來移除資源群組和儲存於該處的資源。

## <a name="next-steps"></a>後續步驟

雖然本文簡短，但仍討論了幾項虛擬網路資源及功能。 Azure 虛擬網路文件涵蓋這些主題的詳細說明：

* [虛擬網路](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [子網路](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [服務端點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png
[acr-vnet-portal]: ./media/container-registry-vnet/acr-vnet-portal.png
[acr-vnet-firewall-portal]: ./media/container-registry-vnet/acr-vnet-firewall-portal.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
