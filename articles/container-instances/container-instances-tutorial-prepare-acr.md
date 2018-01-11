---
title: "Azure 容器執行個體教學課程 - 準備 Azure Container Registry"
description: "Azure 容器執行個體教學課程組件 2 / 3-準備 Azure 容器登錄中"
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: c0aad1f9bbaac9a456b34f75633faba92f57f498
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>部署和使用 Azure Container Registry

這是三段式教學課程的第二段。 在[上一個步驟](container-instances-tutorial-prepare-app.md)，以撰寫簡單的 web 應用程式建立的容器映像[Node.js][nodejs]。 在本教學課程中，您會將此映像推送至 Azure Container Registry。 如果您尚未建立容器映像，請回到[教學課程 1 – 建立容器映像](container-instances-tutorial-prepare-app.md)。

Azure 容器登錄中是以 Azure 為基礎的私人登錄中的 Docker 容器映像。 本教學課程將告訴您如何部署 Azure 容器登錄中執行個體，和發送至它的容器映像。

在本文中，第二個數列，部分您：

> [!div class="checklist"]
> * 部署 Azure 容器登錄中執行個體
> * 標記您 Azure 容器登錄中的容器映像
> * 將映像上傳至您的登錄

下一個文件，數列中的最後一個教學課程中您部署容器從您私用登錄至 Azure 容器執行個體。

## <a name="before-you-begin"></a>開始之前

本教學課程需要您執行 Azure CLI 版本 2.0.23 或更新版本。 執行 `az --version` 以尋找版本。 如果您要安裝或升級，請參閱[安裝 Azure CLI 2.0][azure-cli-install]。

若要完成本教學課程中，您必須安裝在本機上的 Docker 開發環境。 Docker 提供套件，輕鬆地在任何設定 Docker [Mac][docker-mac]， [Windows][docker-windows]，或[Linux] [ docker-linux]系統。

Azure Cloud Shell 不包括完成本教學課程每個步驟所需的 Docker 元件。 您必須完成本教學課程在本機電腦上安裝 Azure CLI 和 Docker 的開發環境。

## <a name="deploy-azure-container-registry"></a>部署 Azure Container Registry

部署 Azure Container Registry 時，您必須先有資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯集合。

使用 [az group create][az-group-create] 命令來建立資源群組。 在此範例中，會在 eastus 區域中建立名為 myResourceGroup 的資源群組。

```azurecli
az group create --name myResourceGroup --location eastus
```

建立 Azure 容器登錄中的以[az acr 建立][ az-acr-create]命令。 容器登錄名稱在 Azure 內**必須是唯一的**，且必須包含 5-50 個英數字元。 以登錄的唯一名稱取代 `<acrName>`：

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

例如，建立名為 *mycontainerregistry082* 的 Azure Container Registry：

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

在本教學課程的其餘部分，我們使用 `<acrName>` 作為您選擇之容器登錄名稱的預留位置。

## <a name="container-registry-login"></a>Container Registry 登入

您必須登入至您的 Azure 容器登錄中執行個體之前推入至它的映像。 使用[az acr 登入][ az-acr-login]命令來完成此作業。 您必須提供您在建立時提供的容器登錄中的唯一名稱。

```azurecli
az acr login --name <acrName>
```

完成後，此命令會傳回 `Login Succeeded` 訊息。

## <a name="tag-container-image"></a>標記容器映像

若要部署的容器映像，從私用的登錄，您必須標記影像的`loginServer`登錄的名稱。

若要查看目前的映像清單，請使用[docker 映像][ docker-images]命令。

```bash
docker images
```

輸出：

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

若要取得 loginServer 名稱，請執行[az acr 顯示][ az-acr-show]命令。 以您的容器登錄名稱取代 `<acrName>`。

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

範例輸出︰

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

以您容器登錄的 loginServer 標記 *aci-tutorial-app*映像。 此外，將 `:v1` 新增至映像名稱的結尾。 此標籤指示映像版本號碼。 取代`<acrLoginServer>`的結果[az acr 顯示][ az-acr-show]命令執行。

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

標記之後，執行 `docker images` 來驗證作業。

```bash
docker images
```

輸出：

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>將映像推送至 Azure Container Registry

推入*aci 教學課程應用*映像登錄[docker push] [ docker-push]命令。 以您在稍早步驟中取得的完整登入伺服器名稱取代 `<acrLoginServer>`。

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

`push`作業花費幾分鐘的時間取決於您的網際網路連線，幾秒鐘，類似於下列輸出：

```bash
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>在 Azure Container Registry 中列出映像

若要傳回一份已推入至 Azure 容器登錄的映像，請使用[az acr 儲存機制清單][ az-acr-repository-list]命令。 使用容器登錄名稱來更新命令。

```azurecli
az acr repository list --name <acrName> --output table
```

輸出：

```azurecli
Result
----------------
aci-tutorial-app
```

然後以查看特定的映像的標記，使用[az acr 儲存機制顯示標籤][ az-acr-repository-show-tags]命令。

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

輸出：

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您可以準備 Azure 容器登錄中用來與 Azure 容器執行個體，使用並推送至登錄的容器映像。 已完成下列步驟：

> [!div class="checklist"]
> * 已部署 Azure Container Registry 執行個體
> * 標記 Azure Container Registry 的容器映像
> * 將映像上傳至 Azure Container Registry

進入下一個教學課程，來了解如何使用 Azure 容器執行個體將容器部署至 Azure 中。

> [!div class="nextstepaction"]
> [將容器部署至 Azure 容器執行個體](./container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
