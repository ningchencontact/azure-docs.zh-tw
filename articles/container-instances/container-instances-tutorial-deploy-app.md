---
title: "Azure 容器執行個體教學課程 - 部署應用程式"
description: "Azure 容器執行個體教學課程第 3 之 3-部署的應用程式"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 471caa1b24dc7017c70782c072b2068f9635244b
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2018
---
# <a name="deploy-a-container-to-azure-container-instances"></a>將容器部署至 Azure 容器執行個體

這是三部分系列的最後一個教學課程。 先前的數列，[建立容器映像來源](container-instances-tutorial-prepare-app.md)和[推送至 Azure 容器登錄中](container-instances-tutorial-prepare-acr.md)。 這篇文章完成教學課程系列 Azure 容器執行個體來部署容器。

在本教學課程中，您：

> [!div class="checklist"]
> * 部署 Azure 容器登錄中使用 Azure CLI 從容器
> * 在瀏覽器中檢視應用程式
> * 檢視容器的記錄檔

## <a name="before-you-begin"></a>開始之前

本教學課程需要您執行 Azure CLI 版本 2.0.23 或更新版本。 執行 `az --version` 以尋找版本。 如果您要安裝或升級，請參閱[安裝 Azure CLI 2.0][azure-cli-install]。

若要完成本教學課程中，您必須安裝在本機上的 Docker 開發環境。 Docker 提供套件，輕鬆地在任何設定 Docker [Mac][docker-mac]， [Windows][docker-windows]，或[Linux] [ docker-linux]系統。

Azure Cloud Shell 不包括完成本教學課程每個步驟所需的 Docker 元件。 您必須完成本教學課程在本機電腦上安裝 Azure CLI 和 Docker 的開發環境。

## <a name="deploy-the-container-using-the-azure-cli"></a>使用 Azure CLI 來部署容器

Azure CLI 能夠透過單一命令將容器部署至 Azure 容器執行個體。 由於容器映像裝載在私人 Azure Container Registry 中，因此您必須納入所需的認證才能存取該映像。 取得使用下列 Azure CLI 命令的認證。

容器登錄的登入伺服器 (以登錄名稱來更新)：

```azurecli
az acr show --name <acrName> --query loginServer
```

容器登錄密碼：

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

若要從容器登錄中部署要求 1 個 CPU 核心和 1 GB 記憶體資源的容器映像，請執行下列命令。 以您從先前兩個命令取得的值取代 `<acrLoginServer>` 和 `<acrPassword>`。

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public --ports 80
```

在幾秒內，您應該就會從 Azure Resource Manager 收到首次的回應。 若要檢視部署的狀態，請使用[az 容器顯示][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

重複[az 容器顯示][ az-container-show]命令之前的狀態變更從*暫止*至*執行*，這應該一些一分鐘才能完成。 當容器狀態為 *Running* (執行中) 時，請繼續進行下一個步驟。

## <a name="view-the-application-and-container-logs"></a>檢視應用程式和容器記錄

部署成功後，顯示容器的公用 IP 位址與[az 容器顯示][ az-container-show]命令：

```bash
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.ip
```

範例輸出：`"13.88.176.27"`

若要查看執行中的應用程式，請在您慣用的瀏覽器中瀏覽至該公用 IP 位址。

![瀏覽器中的 Hello World 應用程式][aci-app-browser]

您也可以檢視容器的記錄輸出：

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

輸出：

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>清除資源

如果您不再需要的任何您在此教學課程中建立的資源，您可以執行[az 群組刪除][ az-group-delete]命令以移除資源群組和它所包含的所有資源。 此命令除了會刪除執行中的容器和所有相關資源之外，也會刪除您所建立的容器登錄。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已完成將容器部署至 Azure 容器執行個體的程序。 已完成下列步驟：

> [!div class="checklist"]
> * 部署 Azure 容器登錄中使用 Azure CLI 從容器
> * 在瀏覽器中檢視應用程式
> * 檢視容器的記錄檔

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md