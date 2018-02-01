---
title: "快速入門 - 使用 Azure CLI 在 Azure 中建立私用的 Docker 登錄"
description: "快速了解如何使用 Azure CLI 建立私用的 Docker 容器登錄。"
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 12/07/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: a74a1ce5c9401d6445f5feec4af8d5cb771d2c64
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2018
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>使用 Azure CLI 建立容器登錄庫

Azure Container Registry 是用於儲存私用 Docker 容器映像的受控 Docker 容器登錄服務。 本指南詳述如何使用 Azure CLI 建立 Azure Container Registry 執行個體。

此快速入門需要您執行 Azure CLI 2.0.25 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0][azure-cli]。

您也必須在本機上安裝 Docker。 Docker 提供可輕鬆在 [Mac][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系統上設定 Docker 的套件。

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create][az-group-create] 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>建立容器登錄庫

在本快速入門中，我們會建立「基本」登錄。 有數個不同的 SKU 提供 Azure Container Registry，簡略說明於下表。 如需個別項目更詳細的資訊，請參閱[容器登錄 SKU][container-registry-skus]。

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

使用 [az acr create][az-acr-create] 命令建立 ACR 執行個體。

登錄名稱在 Azure 內必須是唯一的，且包含 5-50 個英數字元。 下列範例中使用 *myContainerRegistry007*。 請將此更新為唯一的值。

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

建立登錄時，輸出大致如下：

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

在本快入入門的其餘部分，我們使用 `<acrName>` 作為容器登錄名稱的預留位置。

## <a name="log-in-to-acr"></a>登入 ACR

發送和提取容器映像之前，您必須登入 ACR 執行個體。 若要這樣做，請使用 [az acr login][az-acr-login] 命令。

```azurecli
az acr login --name <acrName>
```

完成後，此命令會傳回 `Login Succeeded` 訊息。

## <a name="push-image-to-acr"></a>推送映像到 ACR

若要推送映像到 Azure Container Registry，您必須先有映像。 如果您還沒有任何本機容器映像，請執行下列命令，從 Docker 中樞提取現有的映像。

```bash
docker pull microsoft/aci-helloworld
```

您必須使用 ACR 登入伺服器的完整名稱來標記映像，才能將映像推送至您的登錄。 請執行下列命令，取得 ACR 執行個體的完整登入伺服器名稱。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

使用 [docker tag][docker-tag] 命令來標記映像。 將 `<acrLoginServer>` 取代為 ACR 執行個體的登入伺服器名稱。

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

最後，使用 [docker push][docker-push] 將映像推送到 ACR 執行個體。 將 `<acrLoginServer>` 取代為 ACR 執行個體的登入伺服器名稱。

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>列出容器映像

下列範例會列出登錄中的存放庫：

```azurecli
az acr repository list --name <acrName> --output table
```

輸出：

```bash
Result
----------------
aci-helloworld
```

下列範例會列出在 **aci-helloworld** 的標籤上。

```azurecli
az acr repository show-tags --name <acrName> --repository aci-helloworld --output table
```

輸出：

```bash
Result
--------
v1
```

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [az group delete][az-group-delete] 命令移除資源群組、ACR 執行個體和所有容器映像。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您使用 Azure CLI 建立了 Azure Container Registry。 如果您想要使用 Azure Container Registry 搭配 Azure 容器執行特體，請繼續進行 Azure 容器執行個體教學課程。

> [!div class="nextstepaction"]
> [Azure 容器執行個體教學課程][container-instances-tutorial-prepare-app]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md